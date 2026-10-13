# Exercícios — Aula 12

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem, usando a API que você construiu, com os quatro destinos cadastrados: Paris, Rio de Janeiro, Salvador e Manaus.

## Exercício 1 — Prever a paginação

**Objetivo.** Ler os campos de uma resposta paginada.

**Enunciado.** Sem executar, preveja a resposta de `GET /destinos?size=3&page=1`.

| Campo | Valor previsto |
| --- | --- |
| Destinos em `content` | |
| `number` | |
| `totalPages` | |
| `totalElements` | |
| `last` | |

**Como conferir.** Abra o endereço no navegador e compare. Depois responda: o que `GET /destinos?size=3&page=2` devolve, e por que não é um erro?

??? tip "Dica"
    São quatro destinos, em páginas de três. A primeira página é a de número 0.

## Exercício 2 — Buscar por parte do nome

**Objetivo.** Criar uma consulta só com o nome de um método.

**Enunciado.** Crie a rota `GET /destinos/busca?nome=...`, que devolve uma lista com os destinos cujo nome contém o texto recebido. No repository, o método se chama `findByNomeContaining`, recebe uma `String` e devolve `List<Destino>`. Você não escreve o corpo dele.

**Como conferir.**

- `/destinos/busca?nome=Rio` devolve um destino, o Rio de Janeiro;
- `/destinos/busca?nome=an` devolve dois: Rio de Janeiro e Manaus;
- `/destinos/busca?nome=rio`, com letra minúscula, devolve uma lista vazia.

Olhe no terminal o SQL que foi executado e encontre a parte que corresponde ao `Containing`.

??? tip "Dica"
    São três acréscimos: a linha do método no repository, um método no service que o chama e a rota no controller, com `@RequestParam String nome`.

## Exercício 3 — Cadastrar atrações e descobrir um problema

**Objetivo.** Usar o relacionamento entre as tabelas e reconhecer uma referência circular.

**Enunciado.** Crie `AtracaoService` e `AtracaoController`, com duas rotas: `POST /atracoes`, que salva uma atração, e `GET /atracoes`, que lista todas. O destino da atração vem no JSON, só com o `id`:

```bash
curl -X POST http://localhost:8080/atracoes \
  -H "Content-Type: application/json" \
  -d '{"nome":"Torre Eiffel","descricao":"Torre de ferro","destino":{"id":1}}'
```

**Como conferir.**

- O cadastro responde com a atração e o `id` gerado. No console do H2, a linha da tabela `ATRACAO` tem `1` na coluna `ID_DESTINO`.
- Agora abra `/atracoes` no navegador. A resposta vem enorme e repetitiva. O mesmo passa a acontecer com `/destinos`.

Explique o que está acontecendo. Você não precisa consertar: é o primeiro assunto da próxima aula.

??? tip "Dica"
    O Spring monta o JSON seguindo os getters. Comece por uma atração e veja aonde `getDestino()` leva, e depois aonde `getAtracoes()` leva. A dica "Uma resposta que nunca termina", do [roteiro da Aula 07](../aula-07/material.md), descreve a mesma situação.
