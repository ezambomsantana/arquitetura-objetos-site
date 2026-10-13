# Exercícios — Aula 06

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem, usando a API que você construiu. Lembre de parar e executar de novo o servidor depois de cada alteração.

## Exercício 1 — Contar os objetos

**Objetivo.** Ver, na prática, quantos objetos de um service existem.

**Enunciado.** Em `EditoraService`, acrescente um construtor que avisa quando um objeto é criado:

```java
    public EditoraService() {
        System.out.println("EditoraService criado");
    }
```

1. Execute o servidor e conte quantas vezes a mensagem aparece no terminal.
2. Em `LivroService`, volte temporariamente à versão do Passo 1: apague o `@Autowired` do atributo e escreva `= new EditoraService()`. Preveja quantas vezes a mensagem vai aparecer, e execute.

**Como conferir.** Sua previsão do item 2 coincide com o que apareceu, e você consegue dizer quem criou cada objeto. Desfaça a alteração do item 2 antes de seguir.

??? tip "Dica"
    No item 2, um dos objetos é criado pelo Spring e o outro pelo seu código. Procure o `@Service` e o `new`.

## Exercício 2 — Quantos livros a editora tem

**Objetivo.** Criar uma rota que usa a ligação entre livro e editora.

**Enunciado.** Crie a rota `GET /editora/{id}/conta-livros`, que devolve a quantidade de livros da editora. O método que conta fica na classe `Editora`, com o nome `contaLivros()`. Se você fez o Exercício 3 da Aula 03, ele já existe.

**Como conferir.** Cadastre duas editoras e um livro ligado à primeira. Depois, no navegador:

- `/editora/ID_DA_PRIMEIRA/conta-livros` mostra `1`;
- `/editora/ID_DA_SEGUNDA/conta-livros` mostra `0`.

Pense também: este exercício daria o resultado certo com o código do Passo 2 do roteiro, antes do `@Autowired`?

??? tip "Dica"
    No controller, o método devolve `int`. Ele busca a editora pelo service e chama `contaLivros()` no objeto encontrado.

## Exercício 3 — Prever

**Objetivo.** Ler o método `criar` e prever a resposta da API.

**Enunciado.** Sem executar, responda: o que a API devolve se você cadastrar um livro com o `id` de uma editora que não existe?

```bash
curl -i -X POST http://localhost:8080/livro \
  -H "Content-Type: application/json" \
  -d '{"isbn":"999","nome":"Fantasma","ano":2000,"editora":{"id":"nao-existe"}}'
```

Escreva o código de status, o que vem no corpo da resposta e se o livro fica salvo.

**Como conferir.** Execute o comando e depois abra `/livro/999` no navegador. Considere resolvido quando você consegue apontar, no método `criar`, a linha responsável por cada uma das três coisas que previu, e dizer por que o código de status engana quem usa a API.

??? tip "Dica"
    O código de status vem da anotação `@ResponseStatus` do controller, que não sabe o que o service decidiu.
