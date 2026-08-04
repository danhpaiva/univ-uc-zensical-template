# univ-uc-zensical-template

Template de site de documentação para Unidades Curriculares (UCs) da UNA, construído com [Zensical](https://zensical.org) e publicado automaticamente no GitHub Pages via GitHub Actions.

Use este repositório como ponto de partida para montar o material de uma disciplina: aulas, guias, atividades e avisos, tudo em Markdown, com busca, modo escuro/claro automático, navegação por abas e botão de "editar esta página" já configurados.

## 📋 Sumário

- [Sobre o projeto](#sobre-o-projeto)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Como usar este template](#como-usar-este-template)
- [Configuração local](#configuração-local)
- [Publicação no GitHub Pages](#publicação-no-github-pages)
- [Fluxo de branches (CI/CD)](#fluxo-de-branches-cicd)
- [Personalização](#personalização)
- [Escrevendo conteúdo](#escrevendo-conteúdo)
- [Stack técnica](#stack-técnica)
- [Licença](#licença)

## 🎯 Sobre o projeto

Este template nasceu para dar a professores e alunos da UNA uma forma rápida e padronizada de publicar material didático como um site navegável — sem precisar administrar servidor, banco de dados ou build manual. Basta escrever `.md`, dar `git push` e o site é gerado e publicado sozinho.

O gerador de site usado é o **[Zensical](https://zensical.org)**, sucessor do [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) feito pela mesma equipe — mesmo visual, mesmas configurações em `mkdocs.yml`, porém mais rápido e com instalação mais simples (um único pacote Python).

Principais recursos já configurados:

- 🌓 Alternância automática entre modo claro e escuro (segue o sistema do visitante)
- 🔎 Busca client-side embutida, em português
- 🗂️ Navegação em abas no topo + menu lateral por seções
- 📑 Sumário lateral que acompanha a rolagem da página
- 💻 Blocos de código com destaque de sintaxe e botão de copiar
- 📝 Botão "editar esta página" apontando para o repositório no GitHub
- 📐 Admonitions (`!!! info`, `!!! warning`, `!!! danger`, `!!! tip`, `??? abstract`, etc.) e abas de conteúdo (`=== "Aba"`)
- 🚀 Deploy automático no GitHub Pages a cada `push` na branch `main`
- ✅ Validação de build (links quebrados) a cada `push` na branch `develop`, sem publicar

## 📂 Estrutura do repositório

```text
.
├── .github/
│   ├── workflows/ci.yml      # Build (develop e main) + deploy no GitHub Pages (main)
│   └── dependabot.yml        # Atualiza requirements.txt e as GitHub Actions semanalmente
├── docs/                     # Conteúdo publicado no site (docs_dir)
│   ├── assets/                   # Imagens, logo, favicon
│   ├── aulas/                    # Material das aulas
│   ├── trabalhos/                 # Atividades e projetos avaliativos
│   ├── ferramentas.md             # Guia de instalação das ferramentas do curso
│   ├── setup.md                   # Guia do professor (configurar e publicar o template)
│   └── index.md                   # Página inicial do site
├── rascunhos/                 # Anotações/aulas em rascunho — versionadas no repo, NUNCA publicadas
│   └── 02-aula.md
├── mkdocs.yml                 # Configuração do site (nome, tema, menu, extensões)
├── requirements.txt            # Dependências Python (Zensical)
├── .editorconfig
├── LICENSE
└── README.md
```

> **Por que `rascunhos/` fica fora de `docs/`?** O Zensical publica tudo o que está dentro de `docs_dir` (por padrão, a pasta `docs/`). Diferente do MkDocs, ele ainda não suporta a opção `exclude_docs` para excluir subpastas do build ([veja a lista de configurações ainda não suportadas](https://zensical.org/compatibility/configuration/)) — testamos e, com `exclude_docs`, o conteúdo era publicado do mesmo jeito. Por isso, qualquer conteúdo em elaboração deve ficar em `rascunhos/`, na raiz do repositório — assim continua versionado no Git, mas nunca aparece no site publicado.

## 🚀 Como usar este template

1. No GitHub, clique em **"Use this template" → "Create a new repository"** para criar seu próprio repositório a partir deste template (em vez de fazer fork).
2. Clone o seu novo repositório na sua máquina.
3. Ajuste os dados do seu curso/repositório (veja [Personalização](#personalização)).
4. Siga a [Configuração local](#configuração-local) para rodar o site na sua máquina e a [Publicação no GitHub Pages](#publicação-no-github-pages) para deixá-lo público.

O guia completo, com o mesmo conteúdo desta seção e mais detalhes, também está disponível dentro do próprio site em **Guia do Professor** ([`docs/setup.md`](docs/setup.md)).

## 💻 Configuração local

Pré-requisito: [Python 3.10+](https://www.python.org/downloads/) instalado.

```bash
# 1. Clone o repositório
git clone https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git
cd SEU_REPOSITORIO

# 2. Crie e ative um ambiente virtual
python -m venv venv
# Windows:
.\venv\Scripts\activate
# Linux/macOS:
source venv/bin/activate

# 3. Instale as dependências (Zensical)
pip install -r requirements.txt

# 4. Rode o servidor local com recarregamento automático
zensical serve
```

Acesse `http://127.0.0.1:8000` no navegador. Deixe o comando rodando em um terminal enquanto edita os arquivos `.md` — a página recarrega sozinha a cada alteração salva.

Para gerar apenas os arquivos estáticos finais (o que a CI faz antes de publicar):

```bash
zensical build --strict
```

O resultado vai para a pasta `site/` (ignorada pelo Git). A flag `--strict` faz o comando falhar se houver link interno quebrado — útil para pegar erros antes de publicar.

## 🌐 Publicação no GitHub Pages

O deploy é automático via [`.github/workflows/ci.yml`](.github/workflows/ci.yml), mas exige duas configurações manuais **uma única vez**, no repositório do GitHub:

1. **Settings → Pages → Build and deployment → Source**: selecione **"GitHub Actions"** (⚠️ não deixe em "Deploy from a branch" — essa é a causa mais comum do erro `Deployment failed, try again later`).
2. **Settings → Actions → General → Workflow permissions**: selecione **"Read and write permissions"** e salve.

Depois disso, basta:

3. `git push` na branch **`main`** → o site é construído e publicado automaticamente no GitHub Pages.

Se aparecer `Deployment failed, try again later`, quase sempre é porque o passo 1 ainda não foi feito — corrija e depois use **Actions → (workflow) → Re-run jobs**, ou faça um novo push na `main`. Um aviso sobre *Node 20* no log é apenas informativo e não é a causa do erro.

## 🔀 Fluxo de branches (CI/CD)

O workflow [`ci.yml`](.github/workflows/ci.yml) roda em dois cenários diferentes:

| Branch | O que acontece |
| :--- | :--- |
| `develop` | Instala dependências e roda `zensical build --strict`. Serve apenas para **validar** que o site builda sem erro (ex.: links quebrados). Nada é publicado. |
| `main` | Faz o mesmo build e, se ele passar, também configura o GitHub Pages, empacota `site/` e publica (job `deploy`). |

Fluxo recomendado: escreva e revise o conteúdo na `develop` (ou em uma branch própria), abra PR para a `main` quando estiver pronto, e o merge/push na `main` publica a versão nova automaticamente.

O Dependabot ([`dependabot.yml`](.github/dependabot.yml)) mantém `requirements.txt` e as actions do workflow atualizadas semanalmente, abrindo PRs automáticos.

## 🎨 Personalização

Praticamente toda a identidade do site é configurada em [`mkdocs.yml`](mkdocs.yml) — o Zensical lê esse arquivo sem precisar de conversão:

| O que mudar | Onde |
| :--- | :--- |
| Nome da disciplina | `site_name` |
| URL do GitHub Pages, autor | `site_url`, `site_author` |
| Link do repositório / botão "editar página" | `repo_url`, `repo_name`, `edit_uri` |
| Logo e favicon | `theme.logo`, `theme.favicon` (troque a imagem em `docs/assets/`) |
| Fontes | `theme.font.text`, `theme.font.code` |
| Cor primária/destaque | `theme.palette[].primary` e `.accent` (ex.: `red`, `blue`, `deep purple`) |
| Menu lateral / seções | `nav:` |
| Recursos de navegação (abas, busca, etc.) | `theme.features:` |

Para caixas de destaque (admonitions) no conteúdo, use a sintaxe do Material, com recuo de 4 espaços:

```markdown
!!! tip "Dica"
    Seu conteúdo aqui com recuo de 4 espaços.

!!! danger "Atenção"
    Use para avisos importantes, como prazos.
```

Estatísticas via Google Analytics podem ser habilitadas descomentando o bloco `extra.analytics` no final do `mkdocs.yml`.

## ✍️ Escrevendo conteúdo

- Todo o conteúdo publicado fica em `docs/`, em Markdown.
- Adicione a página ao menu editando `nav:` em `mkdocs.yml` — páginas que não estão no `nav` ainda são geradas, mas não aparecem no menu.
- Rascunhos e material em elaboração vão em `rascunhos/` (fora de `docs/`) até estarem prontos para publicar.
- O tema suporta blocos de código com highlight, tabelas, abas de conteúdo (`=== "Aba"`) e admonitions — exemplos práticos estão em [`docs/index.md`](docs/index.md) e [`docs/aulas/01-introducao.md`](docs/aulas/01-introducao.md).

## 🧱 Stack técnica

- **[Zensical](https://zensical.org)** — gerador de site estático (sucessor do MkDocs + Material for MkDocs), compatível com `mkdocs.yml`.
- **Python 3.10+** — runtime necessário para instalar e rodar o Zensical.
- **GitHub Actions** — build (`develop` e `main`) e deploy (`main`) automatizados.
- **GitHub Pages** — hospedagem do site publicado.

## 📄 Licença

Distribuído sob a licença [MIT](LICENSE).
