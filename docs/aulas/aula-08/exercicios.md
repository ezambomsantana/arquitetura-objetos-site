# Exercícios — Aula 08

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem, usando a API que você construiu. Teste pelo Swagger, em <http://localhost:8080/swagger-ui.html>.

## Exercício 1 — Prever

**Objetivo.** Seguir o caminho de uma exceção até a resposta.

**Enunciado.** Sem executar, preveja o código de status de cada requisição e diga qual classe decidiu esse código.

| Requisição | Código | Quem decidiu |
| --- | --- | --- |
| `GET /editora/abc` | | |
| `DELETE /editora/abc` | | |
| `POST /editora`, com o JSON sem o campo `nome` | | |
| `GET /livro/999`, sem nenhum livro cadastrado | | |

**Como conferir.** Faça as quatro requisições. Considere resolvido quando você consegue explicar por que as duas últimas não respondem `404`, embora também sejam casos de algo que falta.

??? tip "Dica"
    O tratador só conhece uma exceção. Veja, em cada caso, se alguma exceção é lançada e de que tipo ela é.

## Exercício 2 — Nome inválido com o código certo

**Objetivo.** Criar uma exceção própria e o tratador dela.

**Enunciado.** Cadastrar uma editora sem nome responde `500`. Faça a API responder `400`, com a mensagem no corpo.

1. Crie a exceção `CampoInvalidoException`, no pacote `utils`, no mesmo formato de `EditoraNaoEncontradaException`.
2. Em `EditoraService.criar`, lance essa exceção no lugar da `RuntimeException`.
3. Em `EditoraErrorHandler`, acrescente um método que trate `CampoInvalidoException` e responda com `400`.

**Como conferir.** `POST /editora` com um JSON sem `nome` responde com o código `400` e um corpo como este:

```json
{"date":"2026-10-13T13:20:00.000000","message":"Nome da editora não pode ser null ou vazio","statusCode":400}
```

`GET /editora/abc` continua respondendo `404`.

??? tip "Dica"
    O método novo do tratador é uma cópia do que já existe, com três trocas: a exceção, o `HttpStatus` e o número em `setStatusCode`. O código 400 é `HttpStatus.BAD_REQUEST`.

## Exercício 3 — Livro não encontrado

**Objetivo.** Repetir o desenho do tratamento de erros em outro pacote.

**Enunciado.** Buscar um livro que não existe ainda responde `200`, vazio. Faça com os livros o que a aula fez com as editoras:

1. crie `LivroNaoEncontradoException`, no pacote `livro`;
2. faça `LivroService.buscar` lançar essa exceção quando não encontrar o livro, e simplifique `excluir` como foi feito em `EditoraService`;
3. crie `LivroErrorHandler`, no pacote `livro`, respondendo `404`.

**Como conferir.**

- `GET /livro/999` responde `404`, com a mensagem `Livro não encontrado`;
- `DELETE /livro/999` também responde `404`;
- cadastrar, buscar e excluir um livro que existe continuam funcionando.

??? tip "Dica"
    São três arquivos novos e dois métodos alterados. Abra os arquivos correspondentes de editora ao lado e siga o mesmo formato, lembrando do `import` de `ErrorResponseDTO`.
