# Especificação do Produto — Topeca Clip Studio

**Etapa:** ETAPA_01 — especificação funcional e técnica<br>
**Estado:** aprovada com ressalvas na ETAPA_01; pendências de produto permanecem abertas e não há autorização de implementação<br>
**Data:** 2026-11-19

## 1. Visão do produto

O Topeca Clip Studio é um aplicativo desktop para Windows que ajuda Alexandre Topeca a transformar gravações locais de gameplay do PS5 em Shorts verticais. O produto analisa sinais locais de áudio e imagem para sugerir intervalos, mantém o usuário no controle da seleção e dos limites dos cortes e exporta os itens aprovados em lote.

O aplicativo é local-first: gravações, análise, projetos e exportações permanecem no computador. Não envia mídia para serviços externos, não publica no YouTube e não depende de APIs pagas de IA.

## 2. Problema, público e contexto

- **Problema:** revisar manualmente horas de gameplay para encontrar momentos aproveitáveis é demorado; ferramentas automáticas podem sugerir cortes sem contexto e reenquadrar incorretamente a ação ou o HUD.
- **Público inicial:** o próprio canal Alexandre Topeca e criadores que editam gameplay de console em um PC Windows.
- **Fluxo de origem:** o usuário fornece ou seleciona um arquivo local de gameplay, analisa e revisa os cortes, exporta localmente e, se desejar, publica manualmente no YouTube Studio. Arquivos de transmissões já publicadas podem ser usados quando o próprio usuário os disponibilizar legitimamente em arquivo local.
- **Formatos iniciais de importação:** MP4, MKV, MOV e M4V. Não haverá downloader, raspagem ou dependência de serviço externo para obter mídia.
- **Referência funcional:** Wayin.ai inspira a categoria de fluxo (sugestões, revisão, cortes e formato vertical). A interface, o conteúdo e a identidade do produto devem ser originais, sem copiar marca ou apresentação visual de terceiros.
- **Identidade:** nome Topeca Clip Studio e mascote Mutano Maromba. A direção visual aprovada descreve personagem masculino verde-limão e musculoso, orelhas pontudas, cabelo verde-escuro espetado, óculos escuros apoiados na cabeça, fones pretos com detalhes rosa, regata preta com estampa tropical e acessórios gamer, em estética neon rosa, roxo e verde. A referência fornecida é direção visual, não comprovação de procedência ou autorização de direitos.
- **Pacote visual futuro:** seis expressões — Vitória/Hype, Surpresa, Risada, Raiva/Frustração, Concentração e Confusão — em PNG com fundo transparente, mantendo personagem, anatomia, proporções, acessórios e linguagem artística consistentes. As expressões são recursos visuais; não implicam detecção de emoção por IA.

## 3. Objetivos e limites

### Objetivos

1. Reduzir o tempo necessário para localizar possíveis momentos interessantes.
2. Tornar as sugestões auditáveis e fáceis de aceitar, rejeitar ou ajustar.
3. Exportar cortes verticais consistentes, preservando o conteúdo original.
4. Manter a interface responsiva em gravações longas e em hardware com recursos limitados.
5. Deixar claro que pontuações heurísticas não representam compreensão semântica nem probabilidade de viralizar.

### Fora de escopo

- Captura direta do PS5, gravação de live ou integração obrigatória com OBS.
- Upload, publicação automática, OAuth, agendamento ou gerenciamento de canal do YouTube. A publicação é manual pelo usuário no YouTube Studio.
- Envio de gravações a serviços de IA, nuvem ou telemetria.
- Promessa de compreender a narrativa do jogo ou de identificar automaticamente o que é engraçado/importante.
- Reenquadramento inteligente por rastreamento de personagem, OCR ou compreensão semântica no MVP.
- Exclusão automática de originais, temporários ou exportações.

## 4. Requisitos funcionais

| ID | Prioridade | Requisito e critério verificável |
|---|---|---|
| FR-01 | MVP | Criar, salvar, abrir e renomear um projeto local em banco SQLite `project.sqlite3` na pasta do projeto. Após fechar e reabrir, recuperar referências às mídias, perfis, análises, candidatos, decisões, ajustes e exportações. |
| FR-02 | MVP | Importar arquivo local MP4, MKV, MOV ou M4V fornecido/selecionado pelo usuário. O pré-voo usa FFprobe, verifica existência, streams, duração e legibilidade; arquivo inválido gera erro acionável, sem iniciar análise nem modificar a fonte. |
| FR-03 | MVP | Exibir nome, duração, resolução, taxa de quadros, codecs e presença de áudio. Se a fonte for movida ou alterada, o projeto informa que precisa ser localizada/revalidada. |
| FR-04 | MVP | Analisar a mídia local por trechos, combinando sinais explicáveis de energia de áudio, movimento e mudanças visuais. A análise pode gerar zero ou mais candidatos dentro dos limites da fonte. |
| FR-05 | MVP | Exibir cada candidato com início/fim, duração, pontuação relativa e sinais que contribuíram. A interface nunca apresenta a pontuação como probabilidade de sucesso. |
| FR-06 | MVP | Permitir pré-visualizar, aceitar, rejeitar e ajustar manualmente os limites de cada candidato antes da exportação. Nenhum corte é exportado sem seleção explícita. |
| FR-07 | MVP | Exportar cortes selecionados no perfil padrão Equilibrado: 1080 × 1920, MP4/H.264, CQ 20 inicial, AAC 48 kHz quando houver áudio, e FPS de origem até 60; fontes acima de 60 FPS são limitadas a 60, sem interpolação. O layout inicial preserva a imagem horizontal inteira sobre fundo ampliado/desfocado; não recorta automaticamente a ação. |
| FR-08 | MVP | Executar exportações em lote sequencialmente, mostrando progresso por item e resultado individual. A saída é validada com FFprobe antes de ser marcada como concluída. |
| FR-09 | MVP | Permitir escolher pasta de saída e tratar colisão de nomes sem sobrescrever arquivos existentes por padrão. Relatar codec efetivamente usado e qualquer fallback de encoder. |
| FR-10 | MVP | Mostrar estados, erros e cancelamento de análise/exportação. Cancelar não altera a fonte; saída parcial nunca é apresentada como exportação concluída. |
| FR-11 | MVP | Processar mídia local sem envio externo. Não incluir downloader, upload, telemetria, credenciais ou chamadas a APIs externas; a publicação dos Shorts é manual pelo usuário no YouTube Studio. |
| FR-12 | MVP | Preservar originais por padrão. Qualquer limpeza futura de intermediários ou exportações exige confirmação específica e segue `DATA_RETENTION_POLICY.md`. |
| FR-13 | Evolução | Permitir análise contextual por modelos locais compactos, após avaliar licença, recursos e desempenho. |
| FR-14 | Evolução | Reenquadrar verticalmente seguindo a ação/personagem, com visualização e possibilidade de corrigir o resultado. |
| FR-15 | Evolução | Usar OCR, marcadores de jogo ou fala como sinais adicionais configuráveis, sem serviço pago ou envio remoto. |

## 5. Requisitos não funcionais

| ID | Requisito |
|---|---|
| NFR-01 Localidade | Todas as operações de mídia, persistência e inferência aprovadas ocorrem localmente. A ausência de conectividade não impede o fluxo principal após a instalação. |
| NFR-02 Compatibilidade | Alvo inicial: Windows 11 x64 e Python 3.12. FFmpeg/FFprobe são executáveis externos detectados em runtime. A ausência de NVENC deve ter fallback explícito para CPU ou erro explicativo. |
| NFR-03 Memória | Não decodificar nem manter uma gravação inteira em RAM. Análise de áudio e quadros usa janelas limitadas e descarta buffers já consumidos. O limite numérico de RAM/VRAM é pendente de medição e aprovação; não há alegação de desempenho nesta especificação. |
| NFR-04 Desempenho | Objetivo: processar uma gravação de até 4 horas — análise, revisão assistida e exportação dos cortes selecionados — em uma janela operacional de 2 a 4 horas. É uma meta a validar, não capacidade demonstrada. Validar progressivamente em arquivos representativos de 1080p e 4K, com medições autorizadas conforme `PERFORMANCE_TESTING.md`; não declarar suporte de desempenho 4K antes dos resultados. |
| NFR-05 Responsividade | Operações de mídia não bloqueiam a thread da interface. Progresso deve indicar fase e item; o usuário pode cancelar tarefas ativas. |
| NFR-06 Integridade | A fonte é somente leitura. Exportações só passam a concluídas após código de saída válido e verificação dos metadados de saída; falha é visível e preserva evidência útil. |
| NFR-07 Privacidade | Logs não incluem conteúdo de mídia, credenciais ou caminhos pessoais em relatórios públicos. Dados técnicos necessários ficam locais e com acesso restrito ao usuário. |
| NFR-08 Retenção | Não excluir automaticamente arquivos. A classificação e confirmação seguem `DATA_RETENTION_POLICY.md`; publicar significa apenas após confirmação do usuário, fora do app no MVP. |
| NFR-09 Manutenibilidade | Separar interface, casos de uso, domínio, persistência e adaptadores FFmpeg/OpenCV; contratos não dependem de widgets ou processos concretos. |
| NFR-10 Acessibilidade | Ações principais utilizáveis por teclado, estados comunicados por texto e contraste legível no tema gamer. |

## 6. Fluxo completo do usuário

1. Iniciar o aplicativo e criar ou abrir um projeto local.
2. Selecionar um arquivo local de gameplay fornecido pelo usuário (inclusive arquivo legitimamente disponibilizado de uma transmissão publicada) ou localizar novamente uma fonte ausente.
3. Verificar os metadados e o espaço temporário disponível; escolher ou confirmar pasta de trabalho/saída.
4. Selecionar perfil de análise (duração pretendida e distância mínima entre candidatos) e iniciar.
5. Acompanhar pré-voo, análise de áudio, amostragem de imagem e classificação; cancelar se necessário.
6. Revisar a lista ordenada e os sinais explicativos; pré-visualizar o trecho.
7. Aceitar/rejeitar candidatos e ajustar início/fim manualmente; salvar as decisões no projeto.
8. Selecionar os cortes para exportar e confirmar pasta, preset e nomes sem sobrescrita.
9. Acompanhar a fila serial, o progresso e avisos de fallback/erro por item.
10. Inspecionar os arquivos validados e abrir a pasta de saída.
11. Publicar manualmente pelo YouTube Studio, se desejar. O aplicativo não faz upload nem assume que uma publicação foi concluída.
12. Manter fontes e saídas; eventual limpeza exige confirmação conforme a política de retenção.

## 7. Escopo obrigatório do MVP

- Importação local e pré-voo com FFprobe.
- Projeto local persistente, sem copiar a gravação original.
- Banco `project.sqlite3` por projeto, com recuperação do progresso ao reabrir.
- Análise heurística explicável de áudio e quadros, segmentada e limitada em memória.
- Lista de candidatos com pré-visualização, seleção/rejeição e ajuste manual.
- Exportação individual e em lote sequencial para 1080 × 1920 MP4/H.264, layout de fundo desfocado, áudio AAC opcional.
- Validação de cada saída, tratamento de erro/cancelamento e proteção contra sobrescrita.
- Fallback explícito quando NVENC não estiver disponível ou falhar.
- Perfil Equilibrado de exportação com parâmetros definidos nesta especificação; CQ 20 permanece sujeito a validação visual e de tamanho dos arquivos.
- Publicação manual pelo usuário no YouTube Studio; sem integração de upload.
- Logs locais seguros e nenhum envio de mídia.
- Política de preservação; nenhum mecanismo de exclusão automática.

## 8. Parâmetros de análise e decisões de exportação

Os parâmetros de análise derivados do protótipo são baseline inicial, não resultados medidos. O perfil de exportação Equilibrado foi aprovado como decisão para implementação futura; sua qualidade visual e tamanho de arquivo ainda precisam de validação.

| Parâmetro | Decisão/valor | Observação |
|---|---|---|
| Perfil padrão | Equilibrado | Perfil aprovado para implementação futura. |
| Resolução/contêiner/vídeo | 1080 × 1920, MP4, H.264 | Vertical 9:16. |
| Encoder | NVIDIA NVENC preferencial; fallback explícito para CPU | Registrar o encoder efetivamente utilizado. |
| FPS | Preservar a taxa da fonte até 60 FPS; acima disso, limitar a 60 FPS | Abaixo de 60 FPS, preservar a fonte; sem interpolação automática. |
| Áudio | AAC, 48 kHz, se a fonte possuir áudio | Sem faixa de áudio na origem, não criar uma artificialmente. |
| Qualidade | CQ 20 inicial no perfil Equilibrado | Aprovado como parâmetro inicial para implementação futura; validar visualmente e quanto ao tamanho dos arquivos. Não foi definido bitrate nem tamanho máximo. |
| Perfis secundários | Desempenho: CQ 23; Qualidade: CQ 18 | Opções propostas, não selecionadas como padrão. |
| Duração de corte | 35 s por padrão; configuração entre 15 e 60 s | Baseline do protótipo; requer avaliação com gameplay anotada. |
| Separação entre candidatos | 18 s por padrão; configuração entre 8 e 180 s | Baseline do protótipo; não garante diversidade semântica. |
| Número máximo inicial | 30 candidatos por análise | Limite de apresentação proposto. |
| Sinais de análise | energia RMS de áudio, movimento e mudança visual | Pesos do protótipo (0,48/0,32/0,20) são baseline não validado. |
| Amostragem visual | alvo inicial 1 quadro/s, reduzido para 320 × 180 | Executar em fluxo/trechos; não representa uso de NVDEC comprovado. |
| Reenquadramento | imagem inteira ajustada sobre fundo ampliado/desfocado | Preserva HUD, mas não segue a ação; reenquadramento inteligente é futuro. |

## 9. Validação e ressalvas ainda abertas

As decisões de perfil, origem local de mídia, persistência SQLite e publicação manual estão formalizadas. Permanecem abertas as seguintes validações e confirmações:

1. **Amostra de gameplay:** o responsável ainda deve disponibilizar uma amostra autorizada.
2. **Qualidade dos cortes:** anotar manualmente intervalos interessantes e comparar com os candidatos, avaliando precisão dos candidatos relevantes, cobertura dos momentos anotados e redundância entre cortes. A revisão humana antes de exportar é obrigatória; não declarar qualidade do algoritmo antes da avaliação.
3. **Desempenho:** validar a meta de 2–4 horas com arquivos representativos de até 4 horas em 1080p e 4K. Monitorar tempo total, RAM, VRAM, CPU, GPU, temperatura e espaço temporário; limites numéricos de RAM/VRAM dependem de medições autorizadas.
4. **Direitos da arte:** confirmar procedência e direitos de uso da referência visual antes de produzir ou distribuir o pacote de expressões do mascote.

Não há resultados de benchmarks ou avaliação de qualidade nesta etapa; nenhum teste com mídia real ou execução do protótipo foi realizado.
