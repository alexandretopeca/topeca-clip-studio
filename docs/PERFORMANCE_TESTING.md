# Metodologia de Testes de Desempenho

**Estado:** protocolo documental. Nenhum benchmark pesado foi executado ou autorizado por este documento.

## Objetivo e autorização

Toda funcionalidade que processar mídia deve ter um plano de desempenho reproduzível antes de ser considerada pronta. O plano deve especificar escopo, arquivo de teste, limites de tempo/espaço e métricas. Benchmarks prolongados, múltiplas execuções ou testes com mídia real exigem autorização explícita; não são iniciados automaticamente pela existência desta metodologia.

Não marcar desempenho como aprovado sem evidências registradas e critérios de aceite previamente aprovados. Se não houver um limite aprovado, reportar os valores medidos sem classificação de aprovação.

## Dados obrigatórios por execução

| Grupo | Registrar |
|---|---|
| Identificação | ID do teste, data/hora e fuso, responsável, versão/commit do código quando houver, sistema, CPU, GPU, driver e memória disponível antes do teste. |
| Entrada | Classe de retenção (original/cópia de teste), identificador sem caminho pessoal, hash SHA-256, resolução, codec, pixel format, FPS, duração, tamanho e bitrate quando disponível. |
| Saída | Resolução, codec, FPS, duração, tamanho, resultado da validação e localização redigida ou identificador do artefato. |
| FFmpeg | Versão/build, comando completo com argumentos, encoder/decoder, preset, filtros, aceleração selecionada e logs de erro. Remover caminhos pessoais de relatórios públicos, sem descartar evidência local necessária. |
| Tempo e vazão | Horários monotônicos de início/fim, duração de parede e quadros processados. Calcular FPS processados como quadros concluídos divididos pelo tempo de execução; separar de FPS de reprodução em tempo real. |
| RAM | Uso inicial e pico do processo/aplicação em MiB; registrar método e intervalo de amostragem. |
| VRAM | Uso inicial e pico em MiB, método e intervalo de amostragem; diferenciar memória do processo se a ferramenta permitir de memória total da GPU. |
| CPU/GPU | Utilização média e pico (%) durante a janela, ferramenta, intervalo de amostragem e concorrência observada. |
| Temporários | Espaço livre antes/depois, pico de bytes temporários, arquivos intermediários gerados e limpeza realizada, sempre conforme a política de retenção. |
| Condições | Alimentação, estado térmico observado, aplicativos concorrentes relevantes, número de repetições e qualquer desvio do plano. Não alterar configurações do equipamento apenas para produzir um resultado. |
| Resultado | Passou/falhou/não executado, critério comparado, limitações, erros e decisão. Preservar logs de falha e resultados brutos. |

## Procedimento reproduzível

1. Obter aprovação para o teste e declarar antes da execução a funcionalidade, resolução, duração, codec, número de execuções e limites de recursos.
2. Selecionar mídia de teste autorizada. Preferir uma cópia não valiosa; nunca modificar ou remover o original. Registrar hash e metadados com FFprobe.
3. Confirmar espaço temporário suficiente e registrar uso inicial de RAM/VRAM/CPU/GPU e disco.
4. Fixar e registrar a versão do código, FFmpeg e argumentos completos. Não trocar encoder, preset, resolução ou filtros entre comparações sem documentar uma nova configuração.
5. Capturar início/fim com relógio monotônico e amostrar recursos em intervalo declarado. Registrar também stderr/stdout e códigos de saída.
6. Validar o arquivo produzido com FFprobe e uma checagem de integridade adequada ao formato; registrar duração, dimensões, codec e tamanho da saída.
7. Registrar espaço temporário máximo/final, arquivos intermediários e seu tratamento. Não apagar nada automaticamente; seguir `DATA_RETENTION_POLICY.md`.
8. Repetir somente a quantidade previamente aprovada. Informar execuções descartadas, aquecimento e desvios; não selecionar apenas o melhor resultado.
9. Consolidar a tabela de resultados e comparar somente contra critérios aprovados para aquela funcionalidade e máquina de referência.

## Registro de execução

Uma tabela por caso de teste deve conter:

| Campo | Valor |
|---|---|
| ID / estado / autorização | A preencher antes de executar |
| Plataforma e versão do código | A preencher |
| Resolução, codec, FPS e duração de entrada | A preencher |
| FFmpeg e comando/configuração | A preencher |
| Tempo / quadros / FPS processados | A preencher |
| RAM inicial/pico; VRAM inicial/pico | A preencher |
| CPU/GPU média/pico | A preencher |
| Espaço temporário inicial/pico/final | A preencher |
| Saída e validação | A preencher |
| Resultado, critério, erros e condições | A preencher |

## Estado atual

Até a data deste documento, não há benchmark multimídia de desempenho executado para o aplicativo. Os testes curtos sintéticos de disponibilidade de NVENC/NVDEC registrados no diagnóstico anterior não são benchmarks de throughput e não aprovam desempenho em 1080p, 4K ou gravações longas.
