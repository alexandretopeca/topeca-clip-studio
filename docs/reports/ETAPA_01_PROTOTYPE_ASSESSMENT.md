# ETAPA_01 — Avaliação do protótipo MVP 0.1

**Data:** 2026-11-19<br>
**Estado:** avaliação estática concluída; protótipo não executado<br>
**Arquivo analisado:** `Topeca_Clip_Studio_MVP_0_1.zip`<br>
**SHA-256 do ZIP:** `E9847DB0ACDC5D4AB3E9E32F605BB85C2EE16B7FE90744C832AE1E5D04510753`

## 1. Método e limites

O arquivo foi encontrado na pasta de Downloads fornecida, aberto em modo somente leitura e inspecionado sem extrair seus arquivos para o repositório. O código e os textos foram lidos diretamente das entradas ZIP. O protótipo e seus scripts `.bat` não foram executados; nenhuma dependência foi instalada. Portanto, esta é uma avaliação estática do conteúdo disponível, não confirmação de funcionamento ou desempenho.

## 2. Conteúdo identificado

| Entrada no ZIP | Tamanho | Observação |
|---|---:|---|
| `src/core.py` | 8.453 bytes | FFprobe/FFmpeg, análise heurística e exportação vertical. |
| `src/app.py` | 13.378 bytes | Interface PySide6, workers `QThread` e fluxo de seleção/exportação. |
| `README.md` | 4.422 bytes | Fluxo, instalação, sinais e limitações declaradas. |
| `requirements.txt` | 47 bytes | `PySide6`, `opencv-python` e `numpy`; FFmpeg externo. |
| `config.json` | 230 bytes | Duração, intervalo, amostragem, candidatos e tamanho de saída. O código lido não consome este arquivo. |
| `install_windows.bat` | 664 bytes | Cria `.venv`, atualiza pip e instala requisitos. Não executado. |
| `run_windows.bat` | 238 bytes | Ativa `.venv` e executa `src/app.py`. Não executado. |
| `assets/README.txt` | 173 bytes | Não há arte visual do mascote no ZIP. |
| `.gitignore`, `LICENSE.txt`, `.gitkeep` | — | Regras básicas; texto de licença curto, sem inventário de licenças de terceiros. |

Não foram encontrados testes automatizados, banco de dados ou arquivos de modelo.

## 3. Funcionalidades estáticas identificadas

- Seleção de MP4, MKV, MOV e M4V e consulta de duração, dimensão e streams por FFprobe.
- Extração de áudio mono a 8 kHz e cálculo de energia RMS por segundo.
- Amostragem de imagem por intervalo, redução para 320 × 180 e cálculo simples de diferença entre quadros para movimento/transição.
- Combinação dos sinais com pesos 0,48 áudio, 0,32 movimento e 0,20 mudança visual; seleção de picos com limite e espaçamento mínimo.
- Lista de candidatos com horários, nota e texto de evidência; o usuário pode marcar/desmarcar, selecionar todos e limpar seleção.
- Exportação serial dos itens marcados para 1080 × 1920. A imagem inteira é colocada sobre uma cópia ampliada/desfocada; o filtro não rastreia a ação.
- Tentativa de encoder `h264_nvenc` quando o nome do encoder aparece na listagem do FFmpeg; caso não apareça, uso de `libx264`.
- Interface PySide6 com worker QThread para análise/exportação e mensagens básicas de progresso/erro.

O README reconhece que a pontuação não entende o jogo, não equivale à probabilidade de viralizar e que não há captura PS5 nem publicação YouTube.

## 4. Avaliação técnica

### Pontos positivos

- Separação inicial entre `core.py` e `app.py`, com interface de subprocessos FFmpeg/FFprobe por argumentos e sem `shell=True`.
- Fluxo conceitual útil para o produto: importar, sugerir, revisar manualmente e exportar mais de um corte.
- A exportação vertical mantém o frame horizontal completo, evitando corte automático de HUD/personagem no protótipo.
- A tentativa de NVENC tem fallback para CPU quando o encoder não aparece na compilação do FFmpeg.
- As limitações semânticas estão documentadas com honestidade no README.

### Problemas e débitos técnicos

| Severidade | Problema observado estaticamente | Consequência / correção necessária |
|---|---|---|
| Alta | `analyse_audio` converte a duração inteira para WAV, lê todos os frames e cria cópia `float32`; um vídeo de 3 h a 8 kHz gera cerca de 165 MiB de PCM `int16` e 330 MiB de amostras `float32`, antes dos buffers/cópias temporárias. | Risco elevado de pressão de RAM e I/O com a disponibilidade baixa observada. Converter e agregar em blocos; nunca ler a gravação toda na memória. Valores calculados são estimativas de formato, não medição do aplicativo; não se sabe se o processo ultrapassaria limites sem teste. |
| Alta | Falha de conversão de áudio com retorno diferente de zero vira vetor de zeros, produzindo resultado aparentemente válido. | Propagar erro tipado e não gerar candidatos silenciosos nem falso sucesso. |
| Alta | Exportação usa `-y` e nome com timestamp de resolução de segundo; uma segunda execução no mesmo segundo pode substituir arquivos anteriores. | Remover overwrite forçado, verificar colisões e validar destino antes de iniciar. |
| Alta | Verificação NVENC apenas procura texto `h264_nvenc` em `-encoders`; se o encoder estiver listado mas não puder inicializar, não há retry CPU. | Verificar capacidade operacional ou tratar falha; fallback deve ser explícito, registrado e visível ao usuário. |
| Alta | Saída não é reaberta/validada com FFprobe; uma falha posterior no lote deixa arquivos parciais e a UI só exibe erro genérico. | Validar por item, distinguir parcial/sucesso e permitir decisão de retenção sem apagar automaticamente. |
| Média/alta | `VideoCapture` faz seek para cada segundo de duração e decodifica o frame antes de reduzi-lo; não há NVDEC explícito. Para 3 h, são até 10.800 seeks/amostras. | Seek repetido e GOP longo podem custar muito; usar leitura sequencial por janelas ou estratégia medida. Não há evidência de aceleração CUDA na análise. |
| Média | Callbacks numéricos de progresso não chegam à UI: `analyse_audio` não recebe callback e a análise de vídeo recebe `lambda` que descarta progresso. A barra é indeterminada. | Contrato de progresso por fase/intervalo e eventos limitados. |
| Média | `QThread` captura `Exception` e envia somente `str(e)`, sem log estruturado ou código; não há cancelamento cooperativo/subprocesso, timeout ou controle de processo órfão. | Erros difíceis de diagnosticar e operações longas sem cancelamento confiável. |
| Média | `config.json` não é lido; perfil e saídas estão codificados na UI/funções. O caminho `output` é fixo ao lado do pacote e criado no import do módulo. | Configuração enganosa, pouco controle do usuário e efeito colateral ao importar. Persistir configuração/projeto e solicitar destinos explícitos. |
| Média | Primeiros cinco candidatos já aparecem marcados; não há preview no app, ajuste de limites ou persistência do estado do usuário. | Risco de exportar algo não revisado e perda de trabalho ao fechar. Iniciar sem seleção e exigir decisão explícita. |
| Média | Usuário ainda pode trocar o vídeo enquanto uma análise está ativa; resultados e exportação podem se associar a fontes diferentes. | Imobilizar a fonte durante a tarefa ou associar cada resultado a um identificador imutável da mídia/execução. |
| Média | Não há repositório de projetos, migrações, logs duráveis, testes automatizados ou validação de reinício. | Refazer a fundação de persistência e cobertura de falhas antes do uso confiável. |
| Baixa/média | `Candidate` não valida seus limites/score; tipo de retorno é parcialmente `dict` e callbacks sem tipos/contrato. | Introduzir DTOs/tipos de domínio validados e testes de invariantes. |

O fundo desfocado a 1080 × 1920 e a codificação podem ser caros em CPU/GPU; sem benchmark não se pode afirmar velocidade ou suporte sustentado a 4K. O formato de saída e codec são fixos, a pasta é fixa e o lote é serial, mas não há retomada.

## 5. Compatibilidade com o equipamento de referência

- H.264 NVENC é plausível porque a ETAPA_00 verificou o encoder em clipe sintético curto; isso não comprova que o protótipo consiga inicializá-lo nem mede velocidade/temperatura.
- O fallback `libx264` pode funcionar, mas pode ser lento em lote e em 1080 × 1920; nenhuma taxa foi medida.
- A análise por OpenCV faz seeks repetidos e decodifica frames antes do resize; o código não solicita explicitamente NVDEC/CUDA, portanto não há evidência de decodificação acelerada nessa etapa.
- O arquivo completo de áudio convertido e arrays associados pressionam RAM em gravações longas, especialmente com aproximadamente 1,6 GB livre no instante do diagnóstico.
- A disponibilidade de PySide6/OpenCV/NumPy no ambiente atual não foi revalidada e nada foi instalado para esta avaliação.
- Compatibilidade funcional com 1080p, 4K ou gravações de 3 horas não foi testada. Nenhuma conclusão de throughput é afirmada.

## 6. Recomendação por componente

| Componente/ideia | Recomendação | Fundamentação |
|---|---|---|
| Fluxo importar → sugestões → revisão → exportação | Reaproveitar como requisito/fluxo, não como implementação pronta | Corresponde ao núcleo do produto, mas falta validação, preview persistente e controle de saída. |
| `Candidate` e ranking 48/32/20 | Refatorar | Reaproveitar o conceito de sinais explicáveis; criar modelo validado, processamento em blocos e qualidade avaliada em gameplay anotada. Pesos não são aprovados. |
| Wrapper FFprobe/FFmpeg | Refatorar/substituir | Argumentos sem shell são bom ponto de partida; acrescentar timeouts, estados, validação, cancelamento, proteção contra overwrite e erros tipados. |
| Filtro vertical desfocado | Reaproveitar como preset visual opcional, após validação | Preserva a imagem inteira; não constitui reenquadramento inteligente nem foi medido. |
| Interface PySide6 e estilo | Reaproveitar apenas referências de UX | A implementação é uma janela monolítica sem projeto persistente, pré-visualização ou controles confiáveis. Construir UI sobre casos de uso/contratos novos. |
| Workers `QThread` | Reaproveitar o padrão assíncrono, reimplementar o contrato | Há separação básica da UI, mas falta cancelamento, progresso real, encerramento, logging e prevenção de corrida. |
| `config.json` e scripts `.bat` | Não copiar; substituir por configuração validada e empacotamento aprovado | Config não é consumida; instalador atualiza pip/instala dependências e não foi executado nem aprovado para o ambiente. |
| Dependências PySide6/OpenCV/NumPy | Avaliar futuramente, sem instalação nesta etapa | São declaradas no protótipo, mas precisam de versões, licenças, compatibilidade e aprovação de ambiente. |
| Código-fonte do ZIP | Não copiar nesta etapa | Reaproveitamento requer autorização específica e revisão de licença/proveniência; a avaliação não modifica o repositório com código do protótipo. |

## 7. Riscos e ações recomendadas

1. Eliminar o processamento integral de áudio e seeks aleatórios repetidos antes de aceitar fontes longas.
2. Corrigir os falsos-success, erros silenciosos, overwrite, saída parcial, progresso e cancelamento.
3. Criar persistência local e vínculos estáveis entre mídia, análise, candidatos e exportações.
4. Implementar pré-visualização e revisão/ajuste explícitos antes de permitir exportação.
5. Manter NVENC como capacidade detectada/runtime com fallback informado; não supor aceleração de análise por CUDA.
6. Definir amostra de gameplay autorizada e métricas de qualidade/recursos; só executar benchmark mediante autorização conforme `PERFORMANCE_TESTING.md`.
7. Revisar licenças de dependências/arte e confirmar direitos do código antes de qualquer reaproveitamento literal.

**Conclusão:** o MVP 0.1 é uma prova de conceito útil para os requisitos de fluxo e heurística, não uma base pronta para uso em gravações longas. Recomendação geral: refazer a fundação e os contratos; reaproveitar apenas ideias, pesos como baseline experimental e filtro visual opcional, após aprovação. Nenhum arquivo do ZIP foi copiado e nenhuma execução/teste de runtime foi realizado.
