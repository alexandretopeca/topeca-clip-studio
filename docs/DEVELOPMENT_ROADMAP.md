# Roadmap de Desenvolvimento

**Estado:** proposta para revisão técnica. Este roadmap não autoriza iniciar etapas futuras.

## ETAPA_00 — Preparação estrutural

- [x] Registrar o diagnóstico e as validações já executadas.
- [x] Criar a estrutura inicial de diretórios e documentos do projeto.
- [x] Preparar `.gitignore` e configurar a referência remota após verificar o estado local e remoto.
- [x] Documentar metodologia de desempenho e política de retenção de mídia.
- [x] Definir princípios arquiteturais para classificação de mídia, preservação e logs.
- [ ] Revisão e aprovação técnica da complementação.

## Etapas futuras propostas

As etapas abaixo são sugestões de sequência. Escopo, critérios de aceite e autorização devem ser aprovados antes do início de cada uma.

1. **ETAPA_01 — Especificação e contratos:** validar requisitos, casos de uso, modelos de dados e critérios de aceite.
2. **ETAPA_02 — Fundação da aplicação:** estabelecer configuração, logging, tratamento de erros e testes automatizados mínimos.
3. **ETAPA_03 — Inspeção de mídia:** integrar FFprobe e apresentar metadados sem modificar os arquivos de origem.
4. **ETAPA_04 — Fluxo de projeto e cortes:** persistir projetos e permitir revisão manual de intervalos.
5. **ETAPA_05 — Exportação:** enfileirar cortes, usar aceleração disponível com fallback e validar os arquivos gerados.
6. **ETAPA_06 — Análise assistida:** avaliar áudio/visão em lotes pequenos, com revisão humana e limites de recursos.

Nenhum item de etapa futura foi implementado nesta complementação.

## Requisitos transversais para etapas futuras

- Antes de implementar qualquer funcionalidade multimídia, definir seu caso de desempenho e quais métricas são relevantes. Registrar configuração, condições e resultados; não declarar aprovação sem critérios e evidências.
- Obter autorização explícita antes de benchmarks pesados, longos ou repetidos. A existência de `PERFORMANCE_TESTING.md` não autoriza execução.
- Definir a classe de retenção de cada arquivo produzido. Originais são preservados; nenhuma exclusão automática, baseada em nome/idade/espaço ou sem confirmação específica.
- Só registrar uma exportação como publicada após confirmação do usuário. A limpeza futura deverá verificar identidade/caminho/integridade e preferir quarentena/lixeira, mantendo auditoria.
- Manter logs detalhados enquanto uma etapa não estiver aprovada. Após aprovação, consolidar resultados permanentes antes de arquivar/remover logs operacionais redundantes.

Estes requisitos não iniciam a ETAPA_01 nem autorizam mecanismos de exclusão, publicação ou benchmarks.
