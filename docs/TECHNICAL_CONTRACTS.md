# Contratos Técnicos — Topeca Clip Studio

**Etapa:** ETAPA_01 — especificação funcional e técnica<br>
**Estado:** ETAPA_01 aprovada com ressalvas e encerrada; SQLite por projeto e decisões complementares aprovados para implementação futura; validações de qualidade, desempenho e direitos permanecem abertas; nenhuma implementação criada<br>
**Data:** 2026-11-19

Este documento define limites entre componentes e dados. Requisitos de produto estão em `PRODUCT_SPECIFICATION.md`; hardware e retenção seguem `HARDWARE_CONSTRAINTS.md` e `DATA_RETENTION_POLICY.md`.

## 1. Arquitetura lógica

| Componente | Responsabilidade | Não deve |
|---|---|---|
| `ui` | Apresentar projetos, fonte, candidatos, progresso, confirmação e erros; enviar comandos do usuário. | Executar FFmpeg/OpenCV, regra de ranking ou acesso SQL diretamente. |
| `application` (casos de uso) | Orquestrar importar, analisar, revisar, exportar e cancelar; validar transições e permissões. | Manter widgets, buffers de mídia ou regras de apresentação. |
| `core` / domínio | Tipos validados, intervalos, perfis, estados, políticas e erros de domínio. | Depender de Qt, FFmpeg ou caminhos visuais. |
| `media` | Portas e adaptadores para FFprobe, FFmpeg, OpenCV e validação de streams/saídas. | Alterar a fonte ou esconder erro de ferramenta. |
| `analysis` | Extrair recursos em janelas limitadas, produzir candidatos e evidências explicáveis. | Interpretar pontuação como probabilidade ou transmitir mídia a serviço externo. |
| `editor` | Compor intervalos selecionados e preset vertical. | Iniciar exportação sem validação do pedido. |
| `workers` | Executar casos de uso fora da thread UI, emitir progresso e atender cancelamento. | Acessar widgets ou executar operações sem dono de tarefa. |
| `database` | Repositórios locais, transações e migrações do projeto. | Guardar arquivos de vídeo, tokens ou credenciais. |
| Adaptadores | Implementar portas com PySide6, SQLite, FFmpeg/FFprobe e OpenCV. | Ser requisito para o domínio reconhecer regras do produto. |

Dependências permitidas: `ui -> application -> core/ports`; serviços de análise/edição usam `core` e portas; adaptadores implementam portas. A comunicação de volta à interface é por eventos tipados e fila limitada, nunca por callbacks que alterem widgets a partir de uma thread worker.

## 2. Dados de domínio

Identificadores são UUID; horários persistidos em UTC ISO-8601; tempos de mídia em milissegundos inteiros para evitar arredondamento acumulado.

| Modelo | Campos mínimos | Invariantes |
|---|---|---|
| `Project` | `id`, `name`, `created_at`, `updated_at`, `schema_version`, `workspace_path`, `database_path` | Projeto local; banco `project.sqlite3` na pasta do projeto; não contém os bytes da gravação original. |
| `MediaAsset` | `id`, `project_id`, `role`, `source_path`, `size_bytes`, `mtime_utc`, `duration_ms`, streams de áudio/vídeo, estado de disponibilidade | Papel em `original`, `intermediate`, `proxy`, `export` ou `published`; original nunca é destino de escrita. Caminho ausente/alterado exige revalidação. |
| `AnalysisProfile` | `id`, versão, intervalos de corte/separação, parâmetros de amostragem e pesos de sinais | Valores validados e persistidos como snapshot usado pela execução. |
| `AnalysisRun` | `id`, `project_id`, `media_id`, `profile_snapshot`, versão de ferramentas/algoritmo, estado, horários, erro resumido | Resultados ligados à fonte e ao perfil exatos; uma execução falha não produz sucesso vazio. |
| `Candidate` | `id`, `analysis_run_id`, `start_ms`, `end_ms`, `score`, códigos de evidência, decisão do usuário | `0 <= start_ms < end_ms <= duration_ms`; pontuação 0–100 é ranking relativo, não probabilidade. Decisão é `pending`, `accepted` ou `rejected`. |
| `ClipEdit` | `id`, `candidate_id`, limites ajustados, versão e nota opcional | Limites editados continuam dentro da mídia; alterar limites cria versão/atualização auditável, sem alterar a fonte. |
| `ExportBatch` | `id`, `project_id`, versão/configuração do perfil de exportação, estado agregado, timestamps | Um lote tem uma ou mais tarefas ordenadas e não excede a concorrência definida. |
| `ExportJob` | `id`, `batch_id`, `clip_edit_id`, destino solicitado, codec solicitado/usado, estado, progresso, resultado, erro | Só passa a `succeeded` após saída validada. Colisão não autoriza overwrite. |
| `Artifact` | `id`, `project_id`, `role`, `path`, tamanho, metadata, relação com fonte/clip, estado de validação/publicação | `published` só após confirmação explícita do usuário; tentativa de upload não basta. |

### Persistência aprovada

Cada projeto terá um banco SQLite `project.sqlite3` na pasta do projeto. O banco utilizará versionamento de schema, migrações, transações e chaves estrangeiras. Persistirá referências e metadados das mídias, perfis, análises, candidatos, decisões, ajustes e exportações, mas nunca os bytes das gravações. Os originais permanecem em seus locais; arquivos MP4 exportados ficam na pasta de saída escolhida. Mídia ausente ou alterada exige localização/revalidação pelo usuário, sem busca, cópia ou alteração silenciosa. O projeto deve poder ser fechado e reaberto sem perda do progresso.

Não guardar senhas, tokens, conteúdo de vídeo/áudio, miniaturas permanentes sem finalidade aprovada ou caminhos pessoais em logs públicos. Uma operação de importação não calcula hash de horas de mídia por padrão; hash completo pode ser solicitado em fluxo de integridade com indicação de custo e confirmação quando aplicável.

## 3. Contratos de entrada e saída

### 3.1 Inspeção de mídia

- **Entrada:** arquivo local MP4, MKV, MOV ou M4V, fornecido/selecionado pelo usuário, caminho absoluto existente, projeto e cancelamento. Arquivo de transmissão publicada só é aceito quando o próprio usuário o disponibilizar legitimamente. Não há downloader ou raspagem.
- **Pré-condições:** leitura permitida; arquivo não vazio; espaço de trabalho suficiente para a operação prevista.
- **Saída:** `MediaMetadata` com duração, streams, codec, resolução, taxa de quadros racional, presença de áudio e tamanho. A saída inclui `warnings` para metadados ausentes ou taxa não constante.
- **Falha:** `MEDIA_NOT_FOUND`, `MEDIA_UNREADABLE`, `PROBE_FAILED`, `UNSUPPORTED_MEDIA`, `INSUFFICIENT_SPACE` ou `CANCELLED`. Não iniciar análise quando a inspeção não valida a fonte.

### 3.2 Análise

- **Entrada:** `MediaAsset`, perfil imutável da execução e token de cancelamento.
- **Processamento:** janelas temporais configuráveis (ponto inicial proposto: 60 s, com sobreposição técnica mínima nos limites), sem reter frames/áudio completos. Ponto inicial de sinais: RMS de áudio mono e amostragem de imagem a 1 quadro/s em 320 × 180.
- **Saída:** `AnalysisRun` e lista ordenada de `Candidate`, cada um com intervalo, score relativo e códigos de evidência (por exemplo, energia de áudio, movimento, transição).
- **Invariantes:** candidatos são limitados à duração da fonte; o resultado é determinístico para fonte/perfil/versão iguais quando o backend permitir; nenhuma sugestão é exportada antes da seleção humana.
- **Falha:** erro explícito e estado terminal `failed`; erro de áudio ou imagem não se converte em vetor de zeros ou lista aparentemente válida.

### 3.3 Revisão

- **Entrada:** candidatos persistidos e `MediaMetadata`.
- **Saída:** decisões aceitas/rejeitadas e `ClipEdit` com limites atualizados, persistidos em transação.
- **Validação:** limites não negativos, início menor que fim, fim dentro da duração; avisar quando um ajuste produzir duração fora do perfil.
- **UI:** toda ação deve corresponder a comando do caso de uso; preview é somente leitura e seek deve ser cancelável.

### 3.4 Exportação

- **Entrada:** lista ordenada de `ClipEdit` aceitos, source validada, pasta escolhida pelo usuário e preset versionado.
- **Saída por item:** `ExportArtifact` com caminho, dimensões, duração, codec de vídeo/áudio, FPS, tamanho, encoder efetivo, estado de validação e relação com a origem/corte.
- **Perfil padrão aprovado — Equilibrado:** MP4, H.264, 1080 × 1920 (9:16), encoder NVIDIA NVENC preferencial com fallback explícito para CPU, CQ 20 inicial e AAC a 48 kHz quando a fonte possuir áudio. CQ 20 está aprovado como parâmetro inicial para implementação futura, mas qualidade visual e tamanho dos arquivos ainda precisam de validação; não foi definido bitrate ou tamanho máximo.
- **FPS:** preservar a taxa racional da fonte até 60 FPS; acima de 60, limitar a saída a 60 FPS; abaixo de 60, preservar a taxa original sem interpolação automática.
- **Perfis secundários propostos, não selecionados:** Desempenho CQ 23 e Qualidade CQ 18. O perfil Equilibrado é o padrão aprovado.
- **Layout:** imagem horizontal integral em foreground sobre fundo ampliado/desfocado; não recortar automaticamente a ação no MVP.
- **Encoder/fallback:** registrar o encoder efetivamente usado. O parâmetro de qualidade precisa ser mapeado para o encoder selecionado e validado; não presumir equivalência visual entre NVENC e CPU.
- **Validação:** inspecionar cada saída com FFprobe; confirmar streams, codec, dimensão e duração compatíveis com o pedido. Não apresentar arquivo não validado como pronto.
- **Sobrescrita:** default nunca sobrescreve. Gerar nome livre com identificador estável/único ou solicitar escolha do usuário; não usar overwrite forçado.
- **Lote:** sequencial, com estado individual e agregado. Falha de um item não marca os seguintes como concluídos. Registrar outputs parciais e oferecer escolha explícita sobre seu destino/limpeza.

### 3.5 Publicação

O aplicativo gera arquivos MP4 locais. O usuário revisa o resultado e realiza a publicação manualmente pelo YouTube Studio. O MVP não implementa OAuth, upload automático, agendamento ou gerenciamento de canal; não presume publicação concluída sem confirmação do usuário.

## 4. Comunicação UI, serviços e workers

Comandos de aplicação propostos: `CreateProject`, `ImportMedia`, `StartAnalysis`, `SetCandidateDecision`, `UpdateClipEdit`, `StartExportBatch`, `CancelJob`, `LocateMissingMedia`.

Eventos tipados propostos:

| Evento | Conteúdo |
|---|---|
| `JobStateChanged` | `job_id`, estado anterior/novo, timestamp. |
| `JobProgress` | `job_id`, fase, fração opcional 0–1, item atual/total, mensagem localizável. |
| `CandidateProduced` | `analysis_run_id`, candidato validado (pode ser entregue em lotes limitados). |
| `ExportItemCompleted` | `job_id`, artifact e estado de validação. |
| `JobFailed` | `job_id`, código de erro, mensagem segura e detalhe técnico local. |

A thread UI não espera processos. Workers recebem serviços e DTOs imutáveis, nunca referências a widgets. Limitar fila/eventos para não gerar uma notificação por frame. Regra inicial para o notebook: apenas um pipeline pesado ativo; candidatos de um lote são exportados sequencialmente. Qualquer aumento de concorrência depende de medições aprovadas.

## 5. Estados de processamento

### Análise

`queued -> validating -> probing -> sampling_audio -> sampling_video -> ranking -> succeeded`

De qualquer estado ativo, cancelamento segue `cancelling -> cancelled`; falha segue `failed`. A tela de revisão (`review_pending`) é estado do projeto após uma análise concluída, não uma tarefa de processamento ativa. Estados terminais não voltam a ativos; uma nova execução recebe novo ID.

### Exportação

`queued -> validating -> preparing_output -> encoding -> validating_output -> succeeded`

Também admite `cancelling -> cancelled` e `failed`. Em lote, cada item tem estado próprio; o estado do lote é `succeeded`, `partial_failure`, `failed` ou `cancelled` conforme seus itens. Em reinício inesperado, tarefa ativa vira `interrupted`; nunca é presumida concluída nem reiniciada sobre arquivo existente automaticamente.

## 6. Erros, logs e cancelamento

Códigos mínimos: `INVALID_REQUEST`, `MEDIA_NOT_FOUND`, `MEDIA_CHANGED`, `PROBE_FAILED`, `UNSUPPORTED_MEDIA`, `INSUFFICIENT_SPACE`, `ANALYSIS_FAILED`, `EXPORT_FAILED`, `OUTPUT_COLLISION`, `OUTPUT_VALIDATION_FAILED`, `CANCELLED`, `PERSISTENCE_FAILED`, `INTERNAL_ERROR`.

- Exibir erro acionável sem esconder stderr/código de saída em log local restrito.
- Não usar fallback silencioso, retorno vazio com aparência de sucesso ou `except` que descarte falha.
- Construir comandos FFmpeg/FFprobe como lista de argumentos, sem shell; validar opções/pastas controladas pelo usuário.
- Configurar timeouts de inspeção e observar o processo de longa duração; cancelamento termina o subprocesso, aguarda encerramento e confirma que não ficou processo órfão.
- Não incluir áudio, frames, tokens ou caminho pessoal em logs públicos. Logs locais podem registrar identificador de tarefa, fase, ferramenta, código de saída e caminho redigido.
- Falha de limpeza não deve mascarar o erro primário; registrar a falha e manter o item para tratamento explícito.

## 7. Recursos, segurança e retenção

- Hardware de referência: Windows 11, Python 3.12, Intel Core i5-11400H, 16 GB RAM, NVIDIA RTX 3050 Laptop com 4 GB VRAM, SSD NVMe e FFmpeg com NVENC/NVDEC. A disponibilidade instantânea de RAM anteriormente medida foi baixa; a configuração não é garantia de throughput.
- FFmpeg/FFprobe 9.0.2 e NVENC/NVDEC foram verificados em clipes sintéticos curtos na ETAPA_00; isso não comprova desempenho de análise, exportação longa ou 4K.
- NVENC/NVDEC são capacidades opcionais. Verificar disponibilidade operacional, registrar encoder usado e mostrar fallback CPU. Não presumir que o Toolkit CUDA esteja instalado.
- Processar áudio/vídeo em trechos, limitar buffers e evitar várias codificações simultâneas. Consultar espaço antes de gerar intermediários.
- Meta de desempenho aprovada para validação: gravação de até 4 horas, incluindo análise, revisão assistida e exportação dos cortes selecionados, em janela operacional de 2 a 4 horas. É um objetivo, não resultado demonstrado. Validar progressivamente em arquivos representativos de 1080p e 4K, sob autorização, registrando tempo total, RAM, VRAM, CPU, GPU, temperatura e espaço temporário. Limites numéricos de memória dependem de medições; não declarar suporte 4K antes da validação.
- Originais são somente leitura e permanecem no local. Temporários, proxies e exportações são classificados e só podem ser removidos após confirmação explícita e verificação de identidade/integridade.
- App sem upload, telemetria ou chamadas de IA remotas. Modelos futuros devem ser locais, licenciados e opt-in; seus arquivos de pesos não entram no Git.
- Guardar dados do projeto fora do repositório, em diretório escolhido pelo usuário. Não armazenar credenciais.
- Direção visual futura do mascote: pacote de seis PNGs transparentes (Vitória/Hype, Surpresa, Risada, Raiva/Frustração, Concentração e Confusão), mantendo personagem, anatomia, proporções, acessórios e linguagem artística consistentes. A referência visual fornecida orienta a arte, mas procedência e direitos de uso ainda precisam de confirmação. São assets estáticos; não introduzem classificação emocional ou dependência de IA.

## 8. Critérios de aceite para implementação futura

| Verificação | Evidência exigida antes de aprovar a funcionalidade |
|---|---|
| Importação | Casos válidos, corrompidos, sem stream de áudio, caminho inexistente e arquivo movido; fonte inalterada. |
| Análise | Casos sem áudio e com áudio; limites de intervalo; teste determinístico do ranking; prova de que falhas não viram score zero silencioso. |
| Recursos | Teste autorizado em fonte representativa, incluindo duração longa; registrar RAM, VRAM, CPU, GPU, tempo e temporários conforme `PERFORMANCE_TESTING.md`. |
| Revisão | Aceitar/rejeitar/ajustar/salvar/reabrir preserva as decisões e mantém intervalos válidos. |
| Exportação | H.264 1080 × 1920, áudio opcional, corte exato dentro da tolerância aprovada, saída validada e colisão sem overwrite. |
| Cancelamento/falha | UI continua responsiva; processo termina; estado é correto; fonte intacta; nenhum parcial aparece como sucesso. |
| Privacidade/retenção | Inspeção do fluxo sem rede; confirmação e auditoria para limpeza; originais preservados. |

Nenhum destes testes foi executado nesta ETAPA_01. A meta temporal de 2–4 horas está aprovada como objetivo, mas não foi validada; limites numéricos de RAM/VRAM e tolerâncias de duração dependem de medições autorizadas e aprovação do responsável.
