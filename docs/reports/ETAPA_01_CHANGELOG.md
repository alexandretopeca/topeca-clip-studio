# ETAPA_01 — Registro de Alterações

**Data:** 2026-11-19<br>
**Decisão registrada:** aprovada com ressalvas e encerrada.

## Decisão formal e entregáveis aceitos

O responsável pelo projeto revisou e aceitou:

- `docs/PRODUCT_SPECIFICATION.md`
- `docs/TECHNICAL_CONTRACTS.md`
- `docs/reports/ETAPA_01_PROTOTYPE_ASSESSMENT.md`

Também foram revisados `docs/DEVELOPMENT_ROADMAP.md` e `docs/PROJECT_STATUS.md`. A aprovação encerra a etapa de especificação; não autoriza iniciar a ETAPA_02.

## Documentos criados nesta formalização

| Arquivo | Conteúdo | Impacto |
|---|---|---|
| `docs/reports/ETAPA_01_CHANGELOG.md` | Registra a decisão, os documentos aceitos, as ressalvas e o escopo exclusivamente documental. | Rastreabilidade; sem alteração executável. |
| `docs/reports/ETAPA_01_VALIDATION_REPORT.md` | Registra critérios documentais, evidências disponíveis e verificações não executadas. | Evidência da revisão; não declara testes funcionais ou desempenho. |
| `docs/logs/ETAPA_01_EXECUTION_LOG.md` | Cronologia das verificações e atualizações desta formalização. | Registro documental apenas. |

## Documentos atualizados

| Arquivo | Alteração | Justificativa |
|---|---|---|
| `docs/PRODUCT_SPECIFICATION.md` | Estado documental atualizado de rascunho para aprovada com ressalvas; pendências mantidas e sem autorização de implementação. | Refletir a decisão formal sem converter recomendações em decisões. |
| `docs/TECHNICAL_CONTRACTS.md` | Estado documental atualizado para aprovado com ressalvas; SQLite e demais pendências permanecem abertas. | Refletir a decisão formal preservando os pontos que ainda dependem do responsável. |
| `docs/DEVELOPMENT_ROADMAP.md` | Estado da ETAPA_01 alterado de “autorizada/em execução” para “aprovada com ressalvas e encerrada”; entregáveis aceitos explicitados; cinco pendências e falta de autorização da ETAPA_02 mantidas. | Refletir a decisão formal sem promover recomendações a decisões. |
| `docs/PROJECT_STATUS.md` | Etapa atual e tabela atualizadas; revisão/aprovação marcada como concluída; cinco ressalvas listadas; ETAPA_02 mantida não autorizada; implementação permanece não iniciada. | Corrigir o estado anterior, que informava ETAPA_01 em execução e aguardando aprovação. |

## Alterações locais anteriores e preservação

Antes desta formalização, `git status --short` mostrava cinco documentos ETAPA_00 modificados após a publicação. `docs/PROJECT_STATUS.md` era um deles e foi alterado nesta tarefa por ser necessário corrigir a informação conflitante “ETAPA_01 autorizada e em execução” para a decisão agora formalizada. A mudança ficou limitada ao estado da etapa, à tabela, ao aceite e às pendências; os registros de publicação da ETAPA_00 foram preservados.

Os outros quatro documentos pendentes de publicação da ETAPA_00 não foram alterados nesta tarefa:

- `docs/logs/ETAPA_00_EXECUTION_LOG.md`
- `docs/reports/ETAPA_00_CHANGELOG.md`
- `docs/reports/ETAPA_00_GIT_RELEASE_REVIEW.md`
- `docs/reports/ETAPA_00_VALIDATION_REPORT.md`

Os três entregáveis da ETAPA_01 preexistentes foram preservados. `docs/PRODUCT_SPECIFICATION.md` e `docs/TECHNICAL_CONTRACTS.md` receberam somente a atualização do estado de aprovação em seus cabeçalhos; o conteúdo técnico permaneceu intacto. A avaliação do protótipo não foi modificada. O roadmap recebeu a atualização de estado e ressalvas.

## Pendências preservadas

1. Definir FPS e qualidade padrão da exportação.
2. Disponibilizar amostra de gameplay autorizada e estabelecer critérios para avaliar a qualidade dos cortes sugeridos.
3. Definir orçamentos de tempo de processamento, RAM, VRAM e espaço em disco, especialmente para vídeos de até 3 horas e arquivos 4K.
4. Aprovar a arte do mascote Mutano Maromba e confirmar os direitos de utilização.
5. Confirmar formalmente a persistência SQLite por projeto.

Nenhuma recomendação foi registrada como decisão aprovada.

## Implementação, dependências e Git

- Implementação de software: **nenhuma**.
- Código do protótipo copiado: **não**.
- Protótipo executado: **não**.
- Dependências instaladas/atualizadas: **nenhuma**.
- Benchmarks/testes funcionais: **não executados**.
- Commit, push ou alteração de tag: **não realizados**.
- A ETAPA_02 permanece não autorizada.

## Formalização complementar das decisões — 2026-11-19

O responsável confirmou decisões complementares após a aprovação inicial. Este registro complementa o histórico acima; não o reescreve. A autorização continua limitada à documentação e aos registros da ETAPA_01.

### Decisões formalizadas

- Perfil padrão de exportação **Equilibrado**: 1080 × 1920, MP4/H.264, NVENC preferencial com fallback explícito para CPU, FPS da fonte até 60 (acima disso, limitar a 60 sem interpolação), AAC 48 kHz se houver áudio e CQ 20 como parâmetro inicial aprovado para implementação futura. CQ 20 ainda requer validação visual e de tamanho; bitrate e tamanho máximo não foram definidos. Desempenho CQ 23 e Qualidade CQ 18 permanecem opções secundárias propostas.
- Importação de arquivos locais MP4/MKV/MOV/M4V fornecidos pelo usuário; sem downloader, raspagem ou dependência externa para obtenção de mídia. Arquivo de transmissão publicada é admissível se fornecido legitimamente pelo usuário.
- Avaliação futura da seleção por anotações humanas, precisão dos candidatos relevantes, cobertura dos momentos anotados e redundância; revisão humana obrigatória antes da exportação.
- Objetivo de desempenho a validar: gravação de até 4 horas, incluindo análise, revisão assistida e exportação dos cortes selecionados, em janela operacional de 2–4 horas. Testes progressivos em 1080p e 4K deverão monitorar tempo, RAM, VRAM, CPU, GPU, temperatura e temporários. Não é resultado medido nem prova de suporte 4K; limites numéricos de memória não foram fixados.
- Direção visual futura do Mutano Maromba e pacote de seis expressões PNG transparentes: Vitória/Hype, Surpresa, Risada, Raiva/Frustração, Concentração e Confusão. A referência visual não comprova procedência/direitos; não há geração de imagem nem inferência emocional nesta atividade.
- SQLite por projeto aprovado: `project.sqlite3` na pasta do projeto, schema versionado com migrações, transações e chaves estrangeiras; persistir referências, metadados e progresso, nunca bytes de vídeo. Originais ficam em seus locais e MP4s exportados na pasta de saída.
- Publicação manual pelo usuário no YouTube Studio; sem OAuth, upload automático, agendamento ou gerenciamento de canal no MVP.

### Arquivos atualizados nesta formalização complementar

- `docs/PRODUCT_SPECIFICATION.md`
- `docs/TECHNICAL_CONTRACTS.md`
- `docs/DEVELOPMENT_ROADMAP.md`
- `docs/PROJECT_STATUS.md`
- `docs/reports/ETAPA_01_CHANGELOG.md`
- `docs/reports/ETAPA_01_VALIDATION_REPORT.md`
- `docs/logs/ETAPA_01_EXECUTION_LOG.md`

`docs/reports/ETAPA_01_PROTOTYPE_ASSESSMENT.md` não foi alterado. Os quatro documentos históricos ETAPA_00 pendentes foram preservados.

### Ressalvas remanescentes e limites

1. Disponibilizar amostra de gameplay autorizada.
2. Executar avaliação de qualidade dos cortes com intervalos anotados por humanos.
3. Validar a meta de 2–4 horas com fontes representativas de até 4 horas em 1080p e 4K; a meta não foi demonstrada.
4. Confirmar procedência e direitos de utilização da arte-base.

Nenhuma implementação, teste funcional, processamento de gravação real ou benchmark foi realizado. Nenhuma dependência foi instalada. Não houve staging, commit, push ou alteração de tag; a ETAPA_02 permanece não autorizada.
