# Exercícios — Aula 14

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem, usando a API que você construiu. As requisições protegidas precisam do cabeçalho `Authorization: Bearer SEU_TOKEN`.

## Exercício 1 — Prever

**Objetivo.** Entender o que a API responde em cada situação de autenticação.

**Enunciado.** Sem executar, preveja o código de status de cada requisição.

| Requisição | Código previsto |
| --- | --- |
| `POST /login`, com e-mail e senha corretos | |
| `GET /destinos`, com o token recebido | |
| `GET /destinos`, sem token | |
| `POST /login`, com a senha errada | |

**Como conferir.** Faça as quatro requisições com `curl -i`. A última merece atenção: leia o método `login` do `UsuarioService`, veja o que ele faz quando a senha não confere e olhe o terminal do servidor. Considere resolvido quando você consegue explicar por que quem errou a senha recebe o mesmo código de quem não mandou token.

??? tip "Dica"
    O `login` lança uma `RuntimeException`, e nenhuma classe do projeto a trata. O Spring tenta então mostrar a página de erro padrão, e essa página também é protegida pelas regras do `SecurityConfig`.

## Exercício 2 — Validar os destinos

**Objetivo.** Aplicar a validação a outra entidade.

**Enunciado.** Faça com `Destino` o que a aula fez com `Visitante`: o nome e o país não podem ser vazios, cada um com a sua mensagem.

**Como conferir.** Cadastrar um destino só com o nome, `{"nome":"Sem país"}`, responde com o código `400` e um corpo como este, com a mensagem que você escolheu:

```json
{"errors":["País não pode ser vazio"],"message":"Erro de validação","status":400}
```

Na Aula 12, o mesmo teste respondia `500`. Cadastrar um destino completo continua funcionando.

??? tip "Dica"
    São três alterações, e nenhuma classe nova: as anotações nos dois atributos de `Destino`, os `import` delas e o `@Valid` no método de cadastro do `DestinoController`. O tratador que você já escreveu serve para qualquer entidade.

## Exercício 3 — Uma resposta sem senha

**Objetivo.** Usar um DTO de resposta para não expor um dado.

**Enunciado.** Desde a Aula 09, o cadastro de usuário devolve a senha embaralhada. Crie um DTO de resposta, com o `id` e o e-mail, e faça `POST /usuarios` devolvê-lo no lugar da entidade `User`.

**Como conferir.** O cadastro responde assim, sem o campo `senha`:

```json
{"id":1,"email":"ana@insper.edu.br"}
```

O login com a usuária cadastrada continua funcionando.

Para pensar: o README do professor fala em usar DTOs tanto para as requisições quanto para as respostas. Em quais outras rotas da API você faria essa separação, e por quê?

??? tip "Dica"
    O service continua devolvendo o `User` salvo. Quem monta o DTO é o controller, com os dois valores tirados do objeto que o service devolveu.
