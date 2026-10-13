# Exercícios — Aula 05

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem, usando a API que você construiu. Lembre de parar e executar de novo o servidor depois de cada alteração.

## Exercício 1 — Prever os códigos de status

**Objetivo.** Relacionar cada requisição com o código que a API devolve.

**Enunciado.** Com o servidor recém-iniciado e uma editora cadastrada, preveja o código de status de cada requisição.

| Requisição | Código previsto |
| --- | --- |
| `POST /editora`, com um JSON completo | |
| `GET /editora/nao-existe` | |
| `DELETE /editora/nao-existe` | |
| `GET /editoras` | |
| `PUT /editora/SEU_ID` | |

**Como conferir.** Faça cada requisição com `curl -i`, que mostra o código na primeira linha da resposta. Para a última, use `curl -i -X PUT`. Considere resolvido quando você consegue explicar a diferença entre os três casos que parecem erro: o endereço que não existe, o verbo que a rota não aceita e o `id` que não foi encontrado.

??? tip "Dica"
    Olhe para o controller. Para cada requisição, pergunte: existe um método com esse verbo e esse endereço? Se existe, o que ele devolve quando o service não encontra nada?

## Exercício 2 — Emprestar um livro pela API

**Objetivo.** Expor em uma rota um método que a classe já tem.

**Enunciado.** O método `emprestar()` de `Livro` existe desde a Aula 02, mas a API ainda não o usa. Crie a rota `POST /livro/{isbn}/emprestar`, que empresta o livro e devolve o texto que o método produz. Siga o desenho da aula: o controller chama um método novo do `LivroService`, e o service chama o `emprestar()` do livro.

**Como conferir.** Cadastre o livro de ISBN `123`. Depois:

- o primeiro `curl -X POST http://localhost:8080/livro/123/emprestar` responde `Livro emprestado com sucesso`;
- o segundo responde `Livro já está emprestado`;
- `GET /livro/123` mostra `"disponivel":false`.

??? tip "Dica"
    No controller, o método devolve `String` e recebe o ISBN com `@PathVariable`. No service, use o `buscar` que já existe para encontrar o livro.

## Exercício 3 — Cadastrar autores

**Objetivo.** Construir um controller e um service do zero.

**Enunciado.** A classe `Autor` está no projeto, mas não tem rotas. Crie `AutorService` e `AutorController` com duas rotas:

- `POST /autor` cadastra um autor e responde com `201`;
- `GET /autor` lista todos os autores cadastrados.

Como `Autor` não tem identificador, guarde os autores em um `ArrayList`.

**Como conferir.**

```bash
curl -i -X POST http://localhost:8080/autor \
  -H "Content-Type: application/json" \
  -d '{"nome":"Frank Herbert","localNascimento":"Tacoma","idade":65}'
```

A resposta tem o código `201` e o JSON:

```json
{"nome":"Frank Herbert","localNascimento":"Tacoma","idade":65,"livros":[]}
```

Abrir <http://localhost:8080/autor> no navegador mostra uma lista com esse autor.

??? tip "Dica"
    Use `EditoraService` e `EditoraController` como molde. O service fica menor: não precisa de `UUID` nem de `HashMap`. O controller cria o service com `new`, como os outros.
