# ETAPA_01 — Relatório de Validação

**Data:** 2026-11-19<br>
**Escopo:** validação documental da especificação, contratos e avaliação estática do protótipo.<br>
**Decisão formal:** aprovada com ressalvas e encerrada pelo responsável do projeto.

## Critérios documentais de aceite

| Critério | Resultado | Evidência |
|---|---|---|
| Requisitos funcionais e não funcionais documentados | Aceito na revisão formal | `docs/PRODUCT_SPECIFICATION.md`, FR-01 a FR-15 e NFR-01 a NFR-10. |
| Fluxo de uso e MVP definidos | Aceito na revisão formal | Fluxo completo e escopo obrigatório em `docs/PRODUCT_SPECIFICATION.md`. |
| Modelos e contratos entre componentes documentados | Aceito na revisão formal | Arquitetura lógica, modelos, DTOs/eventos, estados, erros e contratos em `docs/TECHNICAL_CONTRACTS.md`. |
| Protótipo MVP 0.1 avaliado | Aceito na revisão formal | Avaliação estática, achados, riscos e recomendação em `docs/reports/ETAPA_01_PROTOTYPE_ASSESSMENT.md`. |
| Restrições de hardware e retenção incorporadas | Aceito na revisão formal | Requisitos de janelas limitadas, recurso opcional, preservação e confirmação nos documentos de especificação e contratos. |
| Roadmap e status consistentes com a decisão | Atualizados | `docs/DEVELOPMENT_ROADMAP.md` e `docs/PROJECT_STATUS.md` registram encerramento com ressalvas e ETAPA_02 não autorizada. |
| Histórico e alterações locais anteriores preservados | Verificado documentalmente | Os quatro registros ETAPA_00 pendentes não foram alterados; a alteração em `PROJECT_STATUS.md` foi necessária para corrigir o estado conflitante da ETAPA_01 e está descrita no changelog. |

## Ressalvas abertas

1. Definir FPS e qualidade padrão da exportação.
2. Disponibilizar amostra de gameplay autorizada e critérios de avaliação da qualidade dos cortes.
3. Definir orçamentos de tempo, RAM, VRAM e disco para vídeos de até 3 horas e arquivos 4K.
4. Aprovar a arte do Mutano Maromba e confirmar os direitos de utilização.
5. Confirmar formalmente a persistência SQLite por projeto.

Estas ressalvas não invalidam o aceite documental nem autorizam presumir respostas; devem ser resolvidas antes das funcionalidades correspondentes.

## Testes e verificações não executados

| Verificação | Resultado | Evidência |
|---|---|---|
| Testes de software | Não executados | A ETAPA_01 não implementou código. |
| Testes funcionais do aplicativo | Não executados | Nenhuma aplicação ou protótipo foi executado. |
| Benchmarks/performance | Não executados | Nenhum benchmark foi autorizado ou realizado nesta formalização. |
| Instalação/atualização de dependências | Não executada | Nenhum comando de instalação foi executado. |
| Execução do ZIP MVP 0.1 | Não executada | A avaliação permanece estática, conforme o escopo aprovado. |
| Verificação de whitespace documental | Aprovado | `git diff --check` terminou com código `0`; verificação PowerShell dos seis documentos Markdown não rastreados não encontrou whitespace final e confirmou newline final. |

## Estado da etapa

**ETAPA_01: aprovada com ressalvas e encerrada no escopo documental.** A implementação não foi iniciada. A ETAPA_02 permanece explicitamente não autorizada.

## Formalização complementar das decisões — 2026-11-19

Esta atualização documental complementa a aprovação inicial e não altera seu histórico.

| Critério de consistência documental | Resultado esperado nesta atualização |
|---|---|
| Perfil padrão Equilibrado e parâmetros de saída | 1080 × 1920, MP4/H.264, NVENC preferencial com fallback CPU, FPS de origem até 60 (limite 60 acima disso, sem interpolação), AAC 48 kHz com áudio e CQ 20 inicial; validação visual/tamanho ainda pendente. |
| Origem local e avaliação de cortes | MP4/MKV/MOV/M4V fornecidos pelo usuário; nenhum downloader. Avaliação futura com anotações humanas, precisão, cobertura e redundância; revisão humana antes de exportar. |
| Meta de desempenho | Até 4 h de origem e janela operacional de 2–4 h para análise, revisão assistida e exportação; objetivo ainda não validado, com ensaios progressivos em 1080p e 4K. |
| Mascote | Direção visual e seis expressões PNG transparentes documentadas como pacote visual futuro; procedência/direitos da arte-base permanecem abertos; sem inferência emocional. |
| Persistência/publicação | SQLite `project.sqlite3` por projeto aprovado; publicação manual no YouTube Studio, sem integração de upload no MVP. |
| Autorização da etapa seguinte | ETAPA_01 continua encerrada com ressalvas; ETAPA_02 permanece explicitamente não autorizada. |

### Ressalvas vigentes após a formalização

1. Disponibilizar amostra de gameplay autorizada.
2. Executar avaliação dos cortes com intervalos anotados por humanos e métricas de precisão, cobertura e redundância.
3. Validar o objetivo de 2–4 horas com arquivos representativos de até 4 horas em 1080p e 4K, coletando métricas de recursos; nenhum limite numérico de RAM/VRAM é presumido.
4. Confirmar procedência e direitos de utilização da arte-base do Mutano Maromba.

### Verificações não realizadas

- Nenhum teste funcional, benchmark, processamento de gravação real ou execução do protótipo.
- Nenhuma implementação, instalação de dependências ou alteração de configuração.
- Nenhum staging, commit, push ou alteração de tag.
- Os ensaios de desempenho descritos são trabalho futuro, dependente de autorização e amostras representativas; a meta de 2–4 horas e o desempenho 4K não estão aprovados como resultados.

### Verificações documentais locais — 2026-11-19

- `git diff --check`: aprovado, código de saída `0`.
- UTF-8 estrito, ausência de whitespace final e newline final: aprovados nos sete documentos autorizados.
- Consistência entre especificação, contratos, roadmap e status: aprovada documentalmente. As decisões confirmadas deixaram de constar como pendências atuais; quatro ressalvas continuam abertas. A ETAPA_02 está explicitamente não autorizada.
- Escopo Git: `git diff --name-only` contém somente documentos; não há arquivos staged. Os quatro documentos históricos ETAPA_00 protegidos mantiveram seus hashes SHA-256 de baseline; nenhum código ou avaliação estática do protótipo foi alterado.
- Estado observado: seis arquivos rastreados modificados e seis não rastreados, todos sob `docs/`; os quatro arquivos ETAPA_00 modificados e o relatório de avaliação já estavam pendentes antes desta atualização.
- Commit, push, merge e operações de tag: nenhum executado nesta formalização.

Estas verificações cobrem documentação e estado local; não são testes de software nem evidência de desempenho.
