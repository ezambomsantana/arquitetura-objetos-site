# Aula 02 — Classes e objetos

!!! abstract "Nesta aula"
    - **Você parte de:** do zero. É o primeiro programa do curso.
    - **Você termina com:** um programa de biblioteca que cria livros e os empresta.
    - **Ideias novas:** classe, objeto, atributo, construtor e método.
    - **Código pronto:** [`aula-02/aula`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-02/aula)

## O que vamos construir

Vamos escrever, do zero, o começo de um sistema de biblioteca. No caminho, você vai aprender duas ideias:

- uma **classe** é um molde: diz quais dados um livro tem e o que ele sabe fazer;
- um **objeto** é um livro de verdade, criado a partir desse molde.

## Antes de começar

**Instale o Java.** Você precisa de um JDK, o kit que compila e executa programas Java. A versão 25 serve para todas as aulas do curso. Baixe em [adoptium.net](https://adoptium.net/) e confira no terminal:

```bash
java -version
```

**Escolha onde escrever.** Qualquer editor de código serve, ou uma IDE como o [IntelliJ](https://www.jetbrains.com/idea/download/).

**Crie a pasta do projeto**, com a pasta onde o código vai ficar:

```bash
mkdir -p aula/src/main/java/br/insper/aula01
cd aula
```

Os dois arquivos da aula, `Main.java` e `Livro.java`, vão ser criados dentro de `src/main/java/br/insper/aula01`.

Sempre que o roteiro disser **execute**, rode estes dois comandos na pasta `aula`:

```bash
javac -d target/classes src/main/java/br/insper/aula01/*.java
java -cp target/classes br.insper.aula01.Main
```

O primeiro **compila**: confere o código e o traduz para um formato que o computador executa. Se houver um erro, ele para e mostra a linha. O segundo **executa** o programa. Em uma IDE, o botão de executar faz as duas coisas.

## Passo 1 — Um programa que roda

Crie o arquivo `Main.java`:

```java title="Main.java"
package br.insper.aula01;

public class Main {

    public static void main(String[] args) {

        String nomeBiblioteca = "Biblioteca do Insper";
        int anoAtual = 2026;
        boolean aberta = true;

        System.out.println(nomeBiblioteca);
        System.out.println(String.format("Ano %s", anoAtual));
        System.out.println();

    }
}
```

**Execute.** Devem aparecer `Biblioteca do Insper` e `Ano 2026`.

O que há de novo para quem vem do Python:

- **`package`** diz em que pasta o arquivo está. Todo arquivo desta aula começa com essa linha.
- **Todo código fica dentro de uma classe**, e o programa começa pelo `main`. A linha do `main` é sempre igual; por enquanto, copie.
- **Cada variável tem um tipo**, declarado na criação: `String` é texto, `int` é número inteiro e `boolean` é verdadeiro ou falso.
- **Cada instrução termina com ponto e vírgula**, e os blocos ficam entre chaves. A indentação ajuda a ler, mas quem delimita o bloco são as chaves.
- **`System.out.println`** é o `print`.

## Passo 2 — A classe `Livro`

A biblioteca precisa de livros. Um livro tem nome, autor, ano e pode estar disponível ou não. Em vez de quatro variáveis soltas para cada livro, vamos criar um molde.

Crie o arquivo `Livro.java`, na mesma pasta:

```java title="Livro.java"
package br.insper.aula01;

public class Livro {

    String nome;
    String autor;
    int ano;
    boolean disponivel;

}
```

Isso é uma **classe**. As quatro linhas são os **atributos**: os dados que todo livro guarda. A classe ainda não é nenhum livro. Para ter um livro de verdade, criamos um **objeto** com `new`.

No `main`, depois do último `println`, acrescente:

```java title="Main.java"
        Livro livro2 = new Livro();
        livro2.nome = "Duna";
        livro2.autor = "Frank Herbert";
        livro2.ano = 1965;
```

`livro2` é um objeto da classe `Livro`. O ponto dá acesso aos atributos dele. O nome é `livro2` porque logo vamos criar um `livro1` acima dele.

!!! question "Atividade 1 — Prever"
    Acrescente, só para testar, estas duas linhas depois do `livro2`. O que você espera ver na segunda?

    ```java
    System.out.println(livro2.nome);
    System.out.println(livro2.disponivel);
    ```

??? success "Confira"
    ```text
    Duna
    false
    ```

    Ninguém deu valor a `disponivel`, então o Java usou o valor padrão de um `boolean`, que é `false`. Apague as duas linhas de teste antes de continuar.

## Passo 3 — Construtores

Preencher atributo por atributo dá trabalho. Um **construtor** é um bloco da classe que é executado na hora do `new` e já deixa o objeto pronto.

Em `Livro.java`, depois dos atributos, acrescente:

```java title="Livro.java"
    public Livro() {
        nome = "Teste";
    }

    public Livro(String nome, String autor, int ano) {
        this.nome = nome;
        this.autor = autor;
        this.ano = ano;
        this.disponivel = true;
    }
```

São dois construtores, e o Java escolhe pelo que vem entre parênteses no `new`. O primeiro, sem parâmetros, mantém o `new Livro()` do `livro2` funcionando. O segundo recebe os dados e os guarda no objeto.

`this` é o objeto que está sendo criado, como o `self` do Python. `this.nome = nome` quer dizer "o nome deste livro recebe o nome que veio no `new`".

Agora, no `main`, crie mais dois livros: `livro1` **acima** do `livro2` e `livro3` **abaixo**.

```java title="Main.java"
        Livro livro1 = new Livro("1984", "George Orwell", 1949);
```

```java title="Main.java"
        Livro livro3 = new Livro("Neuromancer", "William Gibson", 1984);
```

**Execute.** A saída ainda é a mesma, mas agora existem três objetos. Uma classe, três livros, cada um com os próprios valores.

## Passo 4 — Um método

Um livro também sabe fazer coisas. Vamos ensinar o livro a ser emprestado.

Em `Livro.java`, depois dos construtores, acrescente:

```java title="Livro.java"
    public String emprestar() {
        if (this.disponivel) {
            this.disponivel = false;
            return "Livro emprestado com sucesso";
        } else {
            return "Livro já está emprestado";
        }
    }
```

Isso é um **método**: uma função que pertence à classe e é chamada em um objeto. Ele olha o `disponivel` **daquele** livro, muda o valor se for o caso e devolve um texto.

No `main`, logo depois da linha que cria o `livro1`, acrescente:

```java title="Main.java"
        System.out.println("");
        livro1.emprestar();
```

!!! question "Atividade 2 — Prever"
    Acrescente, só para testar, estas três linhas depois do `livro3`. Antes de executar, escreva o que cada uma imprime.

    ```java
    System.out.println(livro1.emprestar());
    System.out.println(livro3.emprestar());
    System.out.println(livro3.emprestar());
    ```

??? success "Confira"
    ```text
    Livro já está emprestado
    Livro emprestado com sucesso
    Livro já está emprestado
    ```

    `livro1` já tinha sido emprestado na linha que você acabou de escrever. `livro3` estava disponível na primeira chamada e deixou de estar na segunda. Emprestar um livro não afeta o outro: cada objeto guarda os próprios valores. Apague as três linhas de teste antes de continuar.

## Passo 5 — Uma lista de livros

Com três livros, vale guardá-los juntos. No topo de `Main.java`, depois da linha `package`, acrescente:

```java title="Main.java"
import java.util.ArrayList;
```

O `import` avisa que o arquivo vai usar uma classe que já vem com o Java, como o `import` do Python.

No `main`, depois do `livro3`:

```java title="Main.java"
        ArrayList<Livro> lista = new ArrayList<>();
        lista.add(livro1);
        lista.add(livro2);
        lista.add(livro3);

        System.out.println("===Livros===");
        for (Livro l : lista) {
            System.out.println(l.nome);
        }
```

**Execute.** Depois do cabeçalho devem aparecer `===Livros===`, `1984`, `Duna` e `Neuromancer`.

`ArrayList<Livro>` é uma lista que só aceita livros, parecida com a `list` do Python. O `for` passa por cada objeto da lista.

## Passo 6 — Completando o programa

Falta o restante da biblioteca: os autores, os usuários e uma busca. Troque a linha de `import` por estas três:

```java title="Main.java"
import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
```

Dentro da classe `Main`, **antes** do `main`, acrescente um método de busca:

```java title="Main.java"
    public static boolean buscaLivro(ArrayList<Livro> livros, String nomeLivro) {
        for (Livro l : livros) {
            if (l.nome.equals(nomeLivro)) {
                return true;
            }
        }
        return false;
    }
```

E no fim do `main`, depois do `for` que imprime os livros:

```java title="Main.java"
        HashSet<String> autores = new HashSet<>();
        for (Livro l : lista) {
            autores.add(l.autor);
        }

        HashMap<String, String> usuarios = new HashMap<>();
        usuarios.put("1", "Ana");
        usuarios.put("2", "Bruno");
        usuarios.put("3", "Carla");

        for (String chave : usuarios.keySet()) {
            String valor = usuarios.get(chave);
            System.out.println(valor);
        }

        if (aberta) {
            System.out.println("Biblioteca esta aberta");
        } else {
            System.out.println("Biblioteca esta fechada");
        }

        System.out.println(buscaLivro(lista, "Teste"));

        System.out.println(lista.size());
```

**Execute.** A saída completa deve ser:

```text
Biblioteca do Insper
Ano 2026


===Livros===
1984
Duna
Neuromancer
Ana
Bruno
Carla
Biblioteca esta aberta
false
3
```

O que entrou de novo:

| Trecho | O que é | Em Python |
| --- | --- | --- |
| `HashSet<String>` | Conjunto: não guarda repetidos | `set` |
| `HashMap<String, String>` | Associa uma chave a um valor | `dict` |
| `buscaLivro` | Método `static`: pertence à classe `Main` e é chamado sem `new` | uma função comum |

!!! question "Atividade 3 — Explicar"
    A busca por `"Teste"` imprimiu `false`. Mas o `livro2` foi criado pelo construtor que define `nome = "Teste"`, e ele está na lista. Por que a busca não o encontra?

??? success "Confira"
    Porque, logo depois de criá-lo, você escreveu `livro2.nome = "Duna"`. A lista guarda o próprio objeto, com os valores que ele tem naquele momento.

## O código da aula

O programa que você escreveu é o da aula. Compare com [`Livro.java`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-02/aula/src/main/java/br/insper/aula01/Livro.java) e [`Main.java`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-02/aula/src/main/java/br/insper/aula01/Main.java) no repositório. Há uma diferença que vale experimentar: lá, depois de `livro1.emprestar();`, existe esta linha:

```java title="Main.java"
        buscaLivro(null, null);
```

!!! question "Atividade 4 — Experimentar"
    Acrescente essa linha no mesmo lugar e execute. O que acontece com o programa?

??? success "Confira"
    O programa imprime o cabeçalho e para com um `NullPointerException`. `null` significa "nenhum objeto", como o `None` do Python. A busca recebeu `null` no lugar da lista e não teve o que percorrer. A mensagem de erro mostra o caminho: o problema aconteceu dentro de `buscaLivro`, chamado pelo `main`.

    Se quiser que o programa volte a rodar até o fim, coloque `//` no início da linha.

As outras diferenças são detalhes: o arquivo do repositório tem mais linhas em branco, uma linha comentada no fim do `main` e um `import` em `Livro.java` que não chega a ser usado.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| Classe | O molde: define os dados e o comportamento | `Livro` |
| Objeto | Um exemplar criado a partir do molde | `livro1`, `livro2`, `livro3` |
| Atributo | Um dado que cada objeto guarda | `nome`, `autor`, `ano`, `disponivel` |
| Construtor | O bloco que prepara o objeto na hora do `new` | `Livro()` e `Livro(String, String, int)` |
| Método | Uma função da classe, chamada em um objeto | `emprestar()` |

Quer praticar? Continue em [Exercícios](exercicios.md).
