# Exercícios — Aula 02

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem. Use o programa que você construiu no roteiro, sem a linha `buscaLivro(null, null);`. Escreva seus testes no `main`, logo depois da criação do `livro3`.

## Exercício 1 — Acompanhar os objetos

**Objetivo.** Dizer o valor de cada atributo depois de cada linha.

**Enunciado.** Considere o trecho abaixo, que usa os dois construtores de `Livro`:

```java linenums="1"
Livro a = new Livro("Duna", "Frank Herbert", 1965);
Livro b = new Livro();
b.autor = "Frank Herbert";
a.emprestar();
```

Sem executar, preencha a tabela com o estado final dos dois objetos.

| Objeto | `nome` | `autor` | `ano` | `disponivel` |
| --- | --- | --- | --- | --- |
| `a` | | | | |
| `b` | | | | |

**Como conferir.** Cole o trecho no `main` e imprima cada atributo, por exemplo `System.out.println(b.ano);`. Sua tabela está certa se todas as células coincidem com a saída.

??? tip "Dica"
    Para cada atributo de `b`, pergunte: alguma linha deu um valor a ele? Se nenhuma deu, o Java usa um valor padrão: `null` para `String`, `0` para `int` e `false` para `boolean`.

## Exercício 2 — Devolver um livro

**Objetivo.** Acrescentar um método à classe.

**Enunciado.** Na classe `Livro`, crie o método `devolver()`, que faz o caminho inverso de `emprestar()`:

- se o livro estiver emprestado, ele volta a ficar disponível e o método devolve `Livro devolvido com sucesso`;
- se não estiver emprestado, nada muda e o método devolve `Livro não estava emprestado`.

**Como conferir.** Execute no `main`:

```java
Livro livro = new Livro("1984", "George Orwell", 1949);
System.out.println(livro.devolver());
System.out.println(livro.emprestar());
System.out.println(livro.devolver());
System.out.println(livro.emprestar());
```

A saída esperada é:

```text
Livro não estava emprestado
Livro emprestado com sucesso
Livro devolvido com sucesso
Livro emprestado com sucesso
```

??? tip "Dica"
    O método tem o mesmo formato de `emprestar()`. Em Java, a negação de uma condição se escreve com `!`, como em `!this.disponivel`.

## Exercício 3 — Um livro que se descreve

**Objetivo.** Escrever um método que usa vários atributos do objeto.

**Enunciado.** Na classe `Livro`, crie o método `descricao()`, que devolve um texto com o nome, o autor e o ano do livro, neste formato:

```text
1984 (George Orwell, 1949)
```

**Como conferir.**

- `System.out.println(livro1.descricao());` imprime exatamente a linha acima.
- `System.out.println(livro3.descricao());` imprime `Neuromancer (William Gibson, 1984)`, sem que você tenha mudado nada além da classe.
- Crie um livro com `new Livro()` e imprima a descrição dele. Você consegue explicar cada parte do que apareceu?

??? tip "Dica"
    O método devolve uma `String` e não recebe parâmetros: tudo o que ele precisa está em `this`. Textos são unidos com `+`, como em `this.nome + " ("`.
