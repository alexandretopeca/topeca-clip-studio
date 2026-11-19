# Arquitetura — Topeca Clip Studio

**Estado:** proposta estrutural da ETAPA_00; sem implementação e sujeita à aprovação técnica.

## Princípios

- Separar interface, regras de domínio, acesso a mídia e tarefas demoradas.
- Processar arquivos em fluxo e por intervalos; não carregar gravações completas na memória.
- Tratar arquivos de origem como somente leitura e escrever resultados em destinos escolhidos pelo usuário.
- Manter a UI responsiva, com progresso, cancelamento e erros explícitos.
- Isolar integrações externas (FFmpeg/FFprobe, aceleração de hardware e futuros modelos) atrás de interfaces substituíveis.
- Tornar o processamento reproduzível registrando parâmetros e metadados necessários, sem armazenar credenciais.
- Classificar cada artefato de mídia por etapa do ciclo de vida; preservar originais por padrão e nunca inferir publicação ou autorização de exclusão.
- Definir e registrar métricas de desempenho para cada funcionalidade de processamento, sem declarar aprovação sem evidências.

## Organização proposta

| Diretório | Responsabilidade prevista |
|---|---|
| `core/` | Tipos, configuração validada, erros e coordenação de casos de uso. |
| `media/` | Descoberta de mídia, leitura de metadados e operações de mídia. |
| `analysis/` | Análise temporal de áudio/vídeo e produção de candidatos a cortes. |
| `editor/` | Descrição de cortes, enquadramento vertical e parâmetros de exportação. |
| `mascot/` | Recursos e apresentação do mascote. |
| `templates/` | Modelos de projeto e presets versionáveis, sem dados pessoais. |
| `database/` | Persistência local de metadados e estado de tarefas, conforme decisão futura. |
| `workers/` | Execução assíncrona, filas, progresso, cancelamento e retomada. |
| `ui/` | Interface desktop e apresentação do estado das operações. |
| `assets/` | Ícones, arte aprovada e recursos estáticos distribuíveis. |
| `models/` | Local reservado para modelos; pesos não são versionados no Git. |

## Fluxo de dados previsto

1. O usuário seleciona uma gravação e um destino de trabalho.
2. A camada de mídia obtém metadados e valida formato, duração e espaço disponível.
3. Workers analisam áudio e quadros em janelas limitadas e retornam intervalos candidatos.
4. A interface apresenta candidatos para revisão humana.
5. O editor converte os intervalos aprovados em tarefas de exportação.
6. FFmpeg executa a exportação quando disponível; a aplicação valida o resultado e reporta falhas.
7. Metadados locais podem registrar projeto, classificação, relação derivado/original e estado de tarefa, sem armazenar a gravação original.

Esse fluxo é conceitual. Nenhuma etapa, banco de dados ou contrato de módulo foi implementado ou aprovado por este documento.

## Ciclo de vida de mídia e desempenho

Originais, intermediários, proxies, exportações e publicados são classes distintas, conforme `DATA_RETENTION_POLICY.md`. A aplicação deve preservar os originais, registrar relações entre derivados e fonte, e manter exportações como não publicadas até confirmação explícita do usuário. Falta de espaço ou idade de arquivo não autorizam exclusão. Qualquer limpeza futura exigirá seleção/identificação do item, confirmação explícita, verificação e preferência por quarentena recuperável, com registro de auditoria.

Toda funcionalidade de processamento terá um caso de desempenho reproduzível conforme `PERFORMANCE_TESTING.md`, com tempo, FPS, RAM, VRAM, CPU/GPU, espaço temporário, metadados da mídia e parâmetros do FFmpeg. Testes pesados exigem autorização; resultados sem critério aprovado serão reportados como medições, não como aprovação.

Logs de etapas não aprovadas e evidências de falhas permanecem preservados. Após aprovação de uma etapa, resultados permanentes serão consolidados antes de arquivar/remover logs operacionais redundantes. Essas regras são requisitos documentais; nenhum mecanismo automático de retenção ou telemetria foi implementado.
