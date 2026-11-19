# Status do Projeto — Topeca Clip Studio

**Atualizado em:** 2026-11-19<br>
**Etapa atual:** Encerramento da ETAPA_01 — especificação funcional e técnica<br>
**Situação da execução:** ETAPA_00 está aprovada, encerrada e publicada. Decisão formal do responsável: ETAPA_01 aprovada com ressalvas e encerrada no escopo documental. Os três entregáveis foram revisados e aceitos; decisões complementares foram formalizadas, permanecendo quatro ressalvas abertas. Não houve implementação, instalação ou benchmark. A ETAPA_02 não está autorizada. Alterações locais não commitadas anteriores e desta formalização foram preservadas.

## Etapas

| Etapa | Situação |
|---|---|
| ETAPA_00 — Validação, estrutura, integração GitHub e governança | Aprovada, encerrada e publicada em `origin` |
| ETAPA_01 — Especificação e avaliação do protótipo | Aprovada com ressalvas e encerrada; documentação aceita; implementação não iniciada |
| ETAPA_02 | Não iniciada nem autorizada |

## Critérios de aceite da ETAPA_00

- [x] Python identificado e executado.
- [x] FFmpeg identificado e validado com codificação curta.
- [x] FFprobe validado com consulta real a amostra sintética.
- [x] NVIDIA identificada; NVENC H.264/HEVC e decodificação CUDA aprovados em testes curtos.
- [x] Estrutura documental `docs/logs/` e `docs/reports/` criada.
- [x] Ambiente virtual `.venv` executado e verificado.
- [x] Log, relatório de validação, changelog e status central registrados.
- [x] Nenhuma dependência instalada nem alteração em Windows, driver ou variável de ambiente realizada nesta validação.
- [x] Todos os 20 diretórios solicitados foram criados.
- [x] Arquivos-base e documentos arquiteturais foram criados sem implementar funcionalidades.
- [x] `.gitignore` foi testado com exemplos de arquivos protegidos e exceções.
- [x] Relatório detalhado de hardware foi avaliado, preservado localmente e excluído do Git público.
- [x] Git local iniciado em `main`; `origin` configurado para o repositório oficial.
- [x] Repositório remoto verificado como vazio (sem refs/histórico no momento da consulta).
- [x] Na validação inicial, nenhum commit, push, fetch ou instalação de dependências foi realizado; os commits e a tag foram publicados posteriormente após autorização.
- [x] Apenas arquivos autorizados foram staged; `git diff --cached --check` passou e o diff completo foi revisado.
- [x] `docs/PERFORMANCE_TESTING.md` documenta métricas e metodologia reproduzível sem executar benchmark pesado.
- [x] `docs/DATA_RETENTION_POLICY.md` define classes, confirmações, preservação e auditoria sem implementar exclusões.
- [x] Arquitetura e roadmap atualizados com as diretrizes permanentes.
- [x] Logs, relatório de validação e changelog atualizados sem remover o histórico anterior.
- [x] Aprovação técnica da ETAPA_00.
- [x] Primeiro commit local do scaffold e da governança concluído após a revisão do primeiro versionamento.
- [x] Dois commits e tag publicados; refs remotos confirmados.

## ETAPA_01 — Entregáveis e critérios de aceite

- [x] Analisar documentação arquitetural, roadmap, hardware, desempenho, retenção e status existentes.
- [x] Avaliar estaticamente `Topeca_Clip_Studio_MVP_0_1.zip` sem extraí-lo no repositório ou executá-lo.
- [x] Definir visão, requisitos, fluxo, MVP e pendências em `docs/PRODUCT_SPECIFICATION.md`.
- [x] Definir arquitetura lógica, modelos e contratos em `docs/TECHNICAL_CONTRACTS.md`.
- [x] Registrar achados e recomendações em `docs/reports/ETAPA_01_PROTOTYPE_ASSESSMENT.md`.
- [x] Atualizar roadmap e este status.
- [x] Revisão dos três entregáveis e aprovação formal com ressalvas pelo responsável.
- [x] Aprovação formal com ressalvas e encerramento da ETAPA_01.

**Implementação:** não iniciada. Nenhum código do ZIP foi copiado; nenhum protótipo foi executado; nenhuma dependência foi instalada ou benchmark realizado.

## ETAPA_01 — Formalização complementar das decisões (2026-11-19)

- **Exportação Equilibrada:** padrão aprovado para implementação futura: 1080 × 1920, MP4/H.264, NVENC preferencial com fallback explícito para CPU, FPS da fonte até 60 (acima disso, limitar a 60 sem interpolação), AAC 48 kHz se houver áudio e CQ 20 inicial. CQ 20 ainda requer validação visual e de tamanho de arquivo; não foi definido bitrate/tamanho máximo. Desempenho CQ 23 e Qualidade CQ 18 são opções secundárias propostas.
- **Origem e avaliação:** importação local de MP4, MKV, MOV e M4V fornecidos pelo usuário aprovada, inclusive arquivo de transmissão publicada disponibilizado legitimamente pelo usuário; sem downloader/raspagem. Avaliação futura usará intervalos anotados por humanos, precisão, cobertura e redundância; revisão humana permanece obrigatória antes da exportação.
- **Desempenho:** objetivo aprovado para validação: gravação de até 4 horas, incluindo análise, revisão assistida e exportação dos cortes selecionados, em janela de 2–4 horas. Testar progressivamente em 1080p e 4K; é meta, não capacidade demonstrada.
- **Mascote:** direção visual e pacote futuro de seis expressões transparentes aprovados como orientação de produto. Procedência e direitos de uso da referência continuam pendentes; não há geração de imagem nem inferência emocional nesta formalização.
- **Persistência:** SQLite por projeto aprovado: `project.sqlite3` na pasta do projeto, schema versionado e migrável, transações e chaves estrangeiras. Guardará referências/metadados/progresso, não bytes de vídeo; originais permanecem nos locais e MP4s exportados na pasta de saída.
- **Publicação:** manual pelo usuário no YouTube Studio; sem OAuth, upload automático, agendamento ou gerenciamento de canal no MVP.

**Ressalvas ainda abertas:**

1. Disponibilizar amostra de gameplay autorizada.
2. Executar avaliação de qualidade dos cortes com anotações humanas, comparando precisão, cobertura e redundância.
3. Validar o objetivo de 2–4 horas com arquivos representativos de até 4 horas em 1080p e 4K; limites de recursos dependem de medições autorizadas.
4. Confirmar procedência e direitos de utilização da arte-base do Mutano Maromba.

As decisões formalizadas não comprovam qualidade ou desempenho e não autorizam implementação. Resolver as ressalvas antes das funcionalidades correspondentes.

## Problemas conhecidos e limitações

- Apenas cerca de 1,6 GB de RAM estavam disponíveis durante a coleta inicial; o valor é variável.
- A GPU NVIDIA tem 4 GB de VRAM.
- NumPy, OpenCV, PySide6, PyTorch e TensorFlow não foram encontrados no ambiente Python verificado.
- `nvcc` não foi encontrado no PATH; CUDA Toolkit de desenvolvimento não confirmado.
- Não foram testadas gravações reais, processamento 1080p/4K, carga prolongada nem velocidade de exportação sustentada.
- A origem do ambiente virtual preexistente `.venv` não foi atribuída à ETAPA_00.
- `configurePythonEnvironment` foi chamado no diagnóstico anterior e reportou ambiente `system`; possível efeito persistente na seleção de interpretador não foi confirmado.
- Não há código de aplicação, dependências de runtime ou suíte funcional nesta estrutura.
- `pyproject.toml` declara `setuptools>=68` como backend de build, mas build/instalação não foram executados.
- A publicação inicial foi verificada com `git ls-remote origin`; `main` e a tag aprovada apontam para os commits esperados.
- Nenhum benchmark de desempenho foi executado para o aplicativo; os testes de codec anteriores foram apenas testes curtos de disponibilidade.
- Mecanismos de retenção, confirmação de publicação, quarentena e exclusão não estão implementados.
- `pyproject.toml` declara `0.1.0a1` (PEP 440).
- A tag anotada `v0.1.0-alpha.1` foi encontrada localmente antes do push e publicada sem alteração; aponta para `107f9b0`. Sua origem/data de criação anterior à publicação não foram determinadas.
- `origin/main` aponta para `75ea0e68fe2c0224c4b4eb3022b5118c4422c9e4`; a tag remota anotada e sua referência peeled apontam, respectivamente, para o objeto da tag e para `107f9b0c166c8626f73d56ea758acadac961f481`.

## Bloqueios

- ETAPA_01 foi encerrada documentalmente; as quatro ressalvas remanescentes permanecem abertas antes das funcionalidades correspondentes.
- A ETAPA_02 permanece explicitamente não autorizada.

## Próxima etapa

**ETAPA_02 não autorizada.** Seu início depende de autorização explícita do responsável pelo projeto.

## Contexto histórico do primeiro versionamento

Os registros anteriores desta etapa descrevem corretamente os estados pré-commit e pré-publicação. O primeiro commit (`107f9b0`) e o segundo commit documental (`75ea0e6`) foram criados localmente e, posteriormente, publicados em `origin/main`. A tag local existente foi publicada sem modificação e aponta para o primeiro commit. Os registros históricos foram preservados nos relatórios e no log.
