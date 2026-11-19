# Restrições de Hardware

**Base:** evidências do diagnóstico local da ETAPA_00. Valores de memória livre e espaço em disco são instantâneos, não requisitos mínimos do produto.

## Limites observados na máquina de referência

- 16 GB de RAM instalados; aproximadamente 1,6 GB estavam disponíveis no momento da coleta.
- GPU NVIDIA com 4 GB de VRAM.
- SSD NVMe com aproximadamente 211 GB livres no momento da coleta.
- NVENC H.264/HEVC e decodificação CUDA funcionaram em clipes sintéticos curtos.
- Não foram medidos throughput sustentado, temperatura, gravações reais, 1080p ou 4K.

O relatório detalhado com fabricante/modelo, versões de driver, sistema e inventário do notebook é mantido localmente e excluído do repositório público.

## Implicações de projeto

- Ler gravações por intervalos e limitar buffers e lotes; não decodificar horas de conteúdo integralmente na RAM.
- Serializar tarefas pesadas de GPU por padrão e limitar concorrência de CPU.
- Usar análise em resolução/amostragem configurável, mantendo a mídia original para exportação.
- Tratar NVENC/NVDEC como aceleração opcional: detectar disponibilidade e fornecer fallback explícito.
- Usar modelos compactos e lotes pequenos como ponto inicial; não presumir que modelos grandes cabem na VRAM.
- Monitorar e reportar espaço temporário antes de exportar.
- Validar desempenho com arquivos representativos antes de declarar suporte a resoluções ou prazos.

Estes dados orientam o desenvolvimento no equipamento observado; não definem requisitos mínimos nem garantem desempenho em outros computadores.
