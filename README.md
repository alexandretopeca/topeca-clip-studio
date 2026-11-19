# Topeca Clip Studio

Aplicativo desktop planejado para organizar gravações de gameplay, analisar mídia e preparar cortes verticais. Este repositório está em preparação: **nenhuma funcionalidade do aplicativo foi implementada**.

## Situação

- ETAPA_00: estrutura inicial e preparação do repositório.
- A complementação está documentada e aguarda aprovação técnica.
- Não há comando de execução do aplicativo neste estágio.
- Nenhum commit ou push foi realizado durante a preparação.

## Estrutura

- `src/topeca_clip_studio/`: código da aplicação, organizado por domínio.
- `assets/`: recursos visuais e templates aprovados para distribuição.
- `models/`: modelos locais; pesos e arquivos grandes são ignorados pelo Git.
- `tests/`: testes do projeto.
- `projects/`: dados locais de projetos; conteúdo não deve ser publicado.
- `output/` e `temp/`: resultados e arquivos temporários locais, exceto os marcadores de diretório.
- `docs/`: arquitetura, roadmap, restrições e registros de etapa.

## Ambiente Python

O workspace já possui um `.venv`; preserve-o e não o recrie. O ambiente verificado utiliza Python 3.12.10. Ainda não há dependências de runtime declaradas em `requirements.txt` nem ponto de entrada executável.

Quando dependências forem aprovadas e adicionadas, os passos de instalação e execução deverão ser documentados antes de serem necessários. Não instale pacotes apenas para executar este scaffold.

## Proteção de dados

O `.gitignore` protege ambientes locais, credenciais, configurações privadas, gravações, mídia pessoal, modelos pesados e conteúdo de `projects/`, `temp/` e `output/`. Revise as regras antes de adicionar novos tipos de mídia ou arquivos locais.

O relatório detalhado `DIAGNOSTICO_HARDWARE_TOPECA.md` descreve o equipamento e o ambiente pessoal de desenvolvimento; foi mantido no workspace, mas excluído do Git público.
