# Site de apoio — Arquitetura de Objetos

Site estático com roteiros de estudo para os códigos das aulas de Arquitetura de Objetos, do professor Eduardo Felipe Zambom Santana. Os códigos ficam no repositório [`arquitetura-objetos-2026-2`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2); este projeto contém apenas o site.

**Situação:** proposta em revisão. Há roteiros para todas as aulas com pasta no repositório, da 02 à 14.

## O que há aqui

| Caminho | Conteúdo |
| --- | --- |
| `docs/index.md` | A página inicial, com a lista de aulas. |
| `docs/aulas/aula-NN/` | Uma pasta por aula, com `material.md` e `exercicios.md`. |
| `docs/referencia/` | A referência rápida e o glossário. |
| `docs/assets/css/extra.css` | O único arquivo de estilo. |
| `mkdocs.yml` | Configuração do site e menu de navegação. |
| `modelos/aula-XX/` | Modelo em Markdown para novas aulas. Não é publicado. |
| `revisao/` | Mapeamento das aulas e pendências para o professor. Não é publicado. |
| `.github/workflows/publicar-site.yml` | Publicação automática no GitHub Pages. |

## Ver o site no computador

É preciso ter Python 3.9 ou mais novo.

```bash
python3 -m venv .venv
source .venv/bin/activate        # no Windows: .venv\Scripts\activate
pip install -r requirements.txt
mkdocs serve
```

Abra o endereço que o comando mostrar, <http://127.0.0.1:8000/arquitetura-objetos-site/>. A página recarrega sozinha quando um arquivo é salvo.

## Gerar o site

```bash
mkdocs build --strict
```

O resultado fica na pasta `site/`. A opção `--strict` faz o comando falhar se houver link interno quebrado ou página fora do menu.

## Editar uma aula

Cada página é um arquivo Markdown em `docs/`. Edite o arquivo, salve e confira no navegador. Os recursos usados nos roteiros são:

````markdown
```java title="Main.java" linenums="10"
// trecho de código, com o número da primeira linha
```

!!! question "Atividade 1 — Prever"
    Texto da atividade.

??? success "Confira depois de tentar"
    Bloco recolhido, que o aluno abre com um clique.

??? tip "Dica"
    Outra forma de bloco recolhido.
````

## Acrescentar uma aula

1. Copie `modelos/aula-XX` para `docs/aulas/aula-NN`, com o número da pasta no repositório de códigos.
2. Preencha os dois arquivos. Os comentários do modelo explicam cada seção.
3. Em `mkdocs.yml`, acrescente a aula ao menu, na seção `nav`, seguindo o formato das outras.
4. Em `docs/index.md`, acrescente a linha da aula na tabela da parte correspondente.
5. Copie para `docs/referencia/glossario.md` as linhas novas da tabela de revisão da aula.
6. Execute `mkdocs build --strict` para conferir.

Todo roteiro segue o mesmo formato: o quadro "Nesta aula", a motivação, os passos em que o aluno constrói o código, a comparação com o código do repositório e a tabela de revisão.

## Publicar no GitHub Pages

O site ainda não foi publicado. O workflow em `.github/workflows/publicar-site.yml` gera e publica o site a cada `push` na branch `main`. Ele nunca foi executado, então a primeira publicação deve ser acompanhada.

**Opção A: repositório só para o site.**

1. Crie um repositório e envie o conteúdo desta pasta para a raiz dele.
2. No GitHub, em *Settings > Pages > Build and deployment*, escolha *Source: GitHub Actions*.
3. Ajuste `site_url` em `mkdocs.yml` para `https://USUARIO.github.io/NOME-DO-REPOSITORIO/`.

**Opção B: dentro do repositório dos códigos.**

1. Copie esta pasta para dentro do repositório, por exemplo como `site-disciplina/`. Os projetos das aulas não são alterados.
2. Mova `.github/workflows/publicar-site.yml` para `.github/workflows/` na **raiz** do repositório. O GitHub só executa workflows que estão lá.
3. No arquivo movido, troque `PASTA_DO_SITE: .` por `PASTA_DO_SITE: site-disciplina`.
4. Faça os passos 2 e 3 da opção A.

O site usa apenas caminhos relativos, então funciona em qualquer subcaminho. O valor de `site_url` é usado no mapa do site e no endereço da prévia local.

Os links para os arquivos de código apontam para `github.com/ezambomsantana/arquitetura-objetos-2026-2`. Se os códigos mudarem de repositório, substitua esse endereço nos arquivos de `docs/` com a busca do editor.

## Escolha da ferramenta

O site de referência, [Técnicas de Programação](https://insper.github.io/TecProg/), é feito com MkDocs e o tema `active-handout`, do Insper. Este projeto usa o mesmo gerador, MkDocs, com o tema [Material](https://squidfunk.github.io/mkdocs-material/) e um arquivo de estilo que reproduz as fontes, as cores e a organização da referência.

O tema da referência foi instalado e avaliado. Ele não foi adotado porque, na versão usada pela referência, carrega cinco scripts de terceiros em todas as páginas, marca as páginas como estando em inglês, não tem campo de busca e não tem licença declarada. O Material traz busca, navegação por teclado e links de anterior e próximo sem código adicional.

O conteúdo em Markdown usa as mesmas extensões da referência, então pode ser levado para o `active-handout` se essa for a preferência.

As versões em `requirements.txt` estão fixadas. Não atualize o MkDocs para a versão 2: ela não é compatível com o tema.
