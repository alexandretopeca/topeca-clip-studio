# Roadmap de Desenvolvimento

**Estado:** ETAPA_00 concluída e publicada; ETAPA_01 aprovada com ressalvas e encerrada. Nenhuma etapa posterior está autorizada automaticamente.

## ETAPA_00 — Preparação estrutural

- [x] Registrar o diagnóstico e as validações já executadas.
- [x] Criar a estrutura inicial de diretórios e documentos do projeto.
- [x] Preparar `.gitignore` e configurar a referência remota após verificar o estado local e remoto.
- [x] Documentar metodologia de desempenho e política de retenção de mídia.
- [x] Definir princípios arquiteturais para classificação de mídia, preservação e logs.
- [x] Revisão e aprovação técnica da complementação; etapa encerrada.
- [x] Publicação inicial dos dois commits e da tag aprovada em `origin`.

## ETAPA_01 — Especificação funcional e técnica

**Estado:** aprovada com ressalvas e encerrada no escopo documental. Nenhuma implementação iniciada.

- [x] Criar `docs/PRODUCT_SPECIFICATION.md` com requisitos, fluxo, MVP, futuro e pendências de produto.
- [x] Criar `docs/TECHNICAL_CONTRACTS.md` com arquitetura lógica, modelos, APIs entre camadas, estados e políticas.
- [x] Criar `docs/reports/ETAPA_01_PROTOTYPE_ASSESSMENT.md` com avaliação estática do ZIP MVP 0.1.
- [x] Atualizar este roadmap e `docs/PROJECT_STATUS.md`.
- [x] Não executar protótipo, não instalar dependências, não copiar código e não iniciar ETAPA_02.

**Decisão formal:** aprovada com ressalvas e encerrada. Os três entregáveis abaixo foram revisados e aceitos; `docs/DEVELOPMENT_ROADMAP.md` e `docs/PROJECT_STATUS.md` também foram revisados.

- [x] `docs/PRODUCT_SPECIFICATION.md` revisado e aceito.
- [x] `docs/TECHNICAL_CONTRACTS.md` revisado e aceito.
- [x] `docs/reports/ETAPA_01_PROTOTYPE_ASSESSMENT.md` revisado e aceito.
- [x] Registrar as ressalvas sem convertê-las em decisões aprovadas.

### Decisões complementares formalizadas

- **Exportação:** perfil padrão Equilibrado aprovado para implementação futura: 1080 × 1920, MP4/H.264, NVENC preferencial com fallback explícito para CPU, FPS de origem até 60 (acima disso, limitar a 60 sem interpolação) e AAC 48 kHz quando houver áudio. CQ 20 é o parâmetro inicial aprovado, ainda sujeito a validação visual e de tamanho; não há bitrate/tamanho máximo definido. Desempenho CQ 23 e Qualidade CQ 18 são opções secundárias propostas, não selecionadas.
- **Origem e avaliação:** importação de arquivos locais MP4/MKV/MOV/M4V fornecidos pelo usuário aprovada; arquivos de transmissões publicadas só se disponibilizados legitimamente pelo próprio usuário. Sem downloader ou raspagem. A avaliação futura usará intervalos anotados por humanos, precisão, cobertura e redundância; revisão humana antes de exportar.
- **Desempenho:** objetivo aprovado para validar: gravação de até 4 horas, incluindo análise, revisão assistida e exportação dos cortes escolhidos, em janela operacional de 2–4 horas. Testar progressivamente em 1080p e 4K, sob autorização, medindo tempo, RAM, VRAM, CPU, GPU, temperatura e temporários. É meta, não desempenho demonstrado; não declarar suporte 4K antes da validação.
- **Mascote:** direção visual e pacote futuro de seis expressões em PNG transparente formalizados: Vitória/Hype, Surpresa, Risada, Raiva/Frustração, Concentração e Confusão, com personagem consistente. A imagem de referência não comprova procedência nem direitos de uso; não implica IA de emoção.
- **Persistência:** SQLite por projeto aprovada. Cada pasta de projeto terá `project.sqlite3`, com versionamento de schema, migrações, transações e chaves estrangeiras; o banco guarda referências/metadados e progresso, não bytes de vídeo.
- **Publicação:** Shorts exportados localmente e publicação manual pelo usuário no YouTube Studio; sem OAuth, upload automático, agendamento ou gerenciamento de canal no MVP.

### Ressalvas abertas — resolver antes das funcionalidades correspondentes

1. Disponibilizar amostra de gameplay autorizada.
2. Executar avaliação da qualidade dos cortes com intervalos anotados por humanos, comparando precisão, cobertura e redundância.
3. Validar o objetivo de 2–4 horas com arquivos representativos de até 4 horas, em 1080p e 4K; definir limites de recursos somente a partir de medições autorizadas.
4. Confirmar procedência e direitos de utilização da arte-base do Mutano Maromba.

As decisões acima não demonstram qualidade ou desempenho e não autorizam implementação. A ETAPA_01 permanece encerrada; a ETAPA_02 e etapas posteriores seguem sem autorização.

## Etapas posteriores propostas

As etapas abaixo são sugestões de sequência. Escopo, critérios de aceite e autorização devem ser aprovados antes do início de cada uma.

1. **ETAPA_02 — Fundação da aplicação:** estabelecer configuração validada, logging, tratamento de erros, contratos aprovados, persistência local e testes mínimos.
2. **ETAPA_03 — Inspeção de mídia:** integrar FFprobe, validar entradas e apresentar metadados sem modificar os arquivos de origem.
3. **ETAPA_04 — Projeto e revisão de cortes:** persistir projetos, pré-visualizar, selecionar e ajustar intervalos manualmente.
4. **ETAPA_05 — Exportação:** executar cortes em lote sequencial, usar encoder disponível com fallback explícito e validar cada arquivo.
5. **ETAPA_06 — Análise assistida:** processar áudio/visão em trechos limitados, produzir sinais explicáveis e manter revisão humana.
6. **Evolução posterior, sem número/autorização:** avaliar modelos locais e reenquadramento inteligente, depois de licença, orçamento de hardware e critérios de qualidade aprovados.

Nenhum item de etapa futura foi implementado nesta complementação.

## Requisitos transversais para etapas futuras

- Antes de implementar qualquer funcionalidade multimídia, definir seu caso de desempenho e quais métricas são relevantes. Registrar configuração, condições e resultados; não declarar aprovação sem critérios e evidências.
- Obter autorização explícita antes de benchmarks pesados, longos ou repetidos. A existência de `PERFORMANCE_TESTING.md` não autoriza execução.
- Definir a classe de retenção de cada arquivo produzido. Originais são preservados; nenhuma exclusão automática, baseada em nome/idade/espaço ou sem confirmação específica.
- Só registrar uma exportação como publicada após confirmação do usuário. A limpeza futura deverá verificar identidade/caminho/integridade e preferir quarentena/lixeira, mantendo auditoria.
- Manter logs detalhados enquanto uma etapa não estiver aprovada. Após aprovação, consolidar resultados permanentes antes de arquivar/remover logs operacionais redundantes.

Estes requisitos não autorizam as ETAPAS_02–06, mecanismos de exclusão/publicação nem benchmarks. A ETAPA_01 foi encerrada somente no escopo de especificação e avaliação documental.
