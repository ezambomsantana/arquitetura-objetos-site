# Exercícios — Aula 03

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem. Use o programa que você construiu no roteiro e escreva seus testes no fim do `main`.

## Exercício 1 — Prever

**Objetivo.** Prever o efeito das regras de `getNome`, `setNome` e `toString`.

**Enunciado.** Sem executar, escreva o que cada `println` imprime.

```java linenums="1"
Livro l = new Livro("Duna", 1965);
l.setNome("It");
System.out.println(l.getNome());
l.setNome("Solaris");
System.out.println(l.getNome());
System.out.println(l);
```

**Como conferir.** Cole o trecho no fim do `main` e execute. Considere resolvido quando as três linhas coincidem e você consegue explicar por que a última tem letras minúsculas.

??? tip "Dica"
    Releia os três métodos em `Livro.java`. Dois deles têm uma regra; o terceiro usa o atributo diretamente.

## Exercício 2 — Uma regra para o ano

**Objetivo.** Usar um setter para proteger um atributo.

**Enunciado.** Altere `setAno`, na classe `Livro`, para que ele ignore anos negativos. Se o valor recebido for menor que zero, o ano do livro não muda.

**Como conferir.** Execute no fim do `main`:

```java
Livro k = new Livro("1984", 1949);
k.setAno(-5);
System.out.println(k.getAno());
k.setAno(1950);
System.out.println(k.getAno());
```

A saída esperada é `1949` e depois `1950`. O restante do programa deve continuar imprimindo o mesmo de antes.

??? tip "Dica"
    O formato é o mesmo de `setNome`: um `if` com um `return` sozinho antes da atribuição.

## Exercício 3 — Quantos livros a editora tem

**Objetivo.** Acrescentar um método a uma classe que guarda uma lista.

**Enunciado.** Na classe `Editora`, crie o método `contaLivros()`, que devolve a quantidade de livros da editora.

**Como conferir.**

- No fim do `main`, `System.out.println(editora.contaLivros());` imprime `3`.
- Crie uma segunda editora, chame `addLivro(null)` nela e imprima `contaLivros()`. O resultado é `0`.
- Você não criou nenhum getter para a lista `livros`.

??? tip "Dica"
    O método devolve um `int` e não recebe parâmetros. A lista já sabe o próprio tamanho: lembre do `lista.size()` do `main`.
