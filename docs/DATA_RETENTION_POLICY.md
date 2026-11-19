# Política de Retenção de Dados

**Estado:** política documental da ETAPA_00. Nenhum mecanismo de movimentação, publicação ou exclusão foi implementado.

## Classes de mídia

| Classe | Descrição | Regra padrão |
|---|---|---|
| Originais | Gravações e mídia fonte fornecidas pelo usuário. | Preservar; tratar como somente leitura e nunca excluir automaticamente. |
| Intermediários | Segmentos, áudio extraído, quadros, arquivos de trabalho e outros produtos intermediários. | Manter enquanto necessários; só limpar após confirmação explícita do usuário e verificação do item. |
| Proxies | Representações reduzidas/recompressas para navegação ou análise. | Identificar como derivados; não substituir o original; limpeza requer confirmação explícita. |
| Exportações | Cortes finais gerados localmente e ainda não confirmados como publicados. | Preservar por padrão. Não inferir publicação a partir de nome, pasta ou sucesso de codificação. |
| Publicados | Exportações cujo upload foi confirmado pelo usuário como concluído. | Podem se tornar elegíveis para exclusão, mas nunca são removidas sem confirmação específica. |

## Organização conceitual

Projetos podem separar referências a originais, intermediários, proxies, exportações e publicações. A organização e os nomes finais de pastas serão decididos antes de implementação; estes rótulos não afirmam que diretórios ou mecanismos já existam.

Metadados de projeto devem manter a classificação do arquivo, relação com o original, estado de processamento/publicação e destino escolhido. Não armazenar senhas, tokens ou credenciais de serviços de publicação.

## Confirmação de publicação

Uma exportação só muda para a classe `Publicados` após confirmação explícita do usuário de que o upload foi concluído. Um comando de upload iniciado, ausência de erro local, nome de arquivo, presença em uma pasta ou resposta ambígua não constituem confirmação. Sem confirmação, manter como exportação local.

## Política de exclusão

- Não implementar nem presumir exclusão automática.
- Preservar originais por padrão. Intermediários, proxies e exportações não são removidos apenas por idade, espaço baixo, nome ou localização.
- Exigir confirmação explícita e específica do usuário para cada ação de remoção, informando classe, identidade do arquivo, caminho de destino e motivo.
- Antes da ação, verificar o caminho resolvido, identidade e integridade do arquivo (por exemplo, tamanho, timestamps e hash quando aplicável). Não selecionar ou excluir arquivos apenas com base no nome.
- Preferir mover para lixeira/quarentena recuperável em vez de exclusão definitiva; a ação e a localização de quarentena também devem ser registradas.
- Após a ação, registrar caminho/identificador, classe, motivo, data/hora, confirmação, resultado e espaço recuperado. Se a ação falhar, preservar o erro e não reportar sucesso.
- Se a publicação não foi confirmada, não tratar o arquivo como publicado nem elegível pela regra específica de exportações publicadas.

## Logs e auditoria

Logs de uma etapa não aprovada não devem ser apagados. Após aprovação, resultados relevantes, falhas, decisões e benchmarks devem ser consolidados em relatórios permanentes; logs operacionais redundantes só podem ser arquivados ou removidos conforme a governança aprovada, preservando o histórico Git e as evidências necessárias.

## Estado de implementação

Esta política define comportamento futuro, não implementa limpeza, quarentena, verificação de publicação ou gestão automática de retenção. Qualquer implementação requer escopo, UX de confirmação, testes e aprovação próprios.
