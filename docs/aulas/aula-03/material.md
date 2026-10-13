# Aula 03 — Encapsulamento e relacionamentos

!!! abstract "Nesta aula"
    - **Você parte de:** o programa da [Aula 02](../aula-02/material.md).
    - **Você termina com:** livros com os dados protegidos, ligados a uma editora e a autores.
    - **Ideias novas:** encapsulamento, getter e setter, relacionamentos entre classes.
    - **Código pronto:** [`aula-03/aula`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-03/aula)

## O que vamos construir

Vamos continuar a biblioteca da [Aula 02](../aula-02/material.md). O programa vai ganhar duas melhorias:

- **encapsulamento**: os dados de um livro ficam protegidos, e só a própria classe decide como eles mudam;
- **relacionamentos**: um livro passa a ter uma editora e autores, que também são objetos.

## Antes de começar

Parta do programa que você terminou na Aula 02, sem a linha `buscaLivro(null, null);`. Se não o tiver, copie [`Livro.java` e `Main.java` da pasta `aula-02`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-02/aula/src/main/java/br/insper/aula01) e apague essa linha.

Os comandos para executar continuam os mesmos:

```bash
javac -d target/classes src/main/java/br/insper/aula01/*.java
java -cp target/classes br.insper.aula01.Main
```

## Passo 1 — Proteger os atributos

Hoje qualquer parte do programa pode escrever `livro1.disponivel = true` e desfazer um empréstimo sem passar pelo método `emprestar`. A classe não tem controle sobre os próprios dados.

A palavra `private` resolve isso: um atributo `private` só pode ser usado dentro da própria classe. Em `Livro.java`, troque os atributos e o segundo construtor por:

```java title="Livro.java"
    private String nome;
    private int ano;
    private boolean disponivel;

    public Livro() {
        nome = "Teste";
    }

    public Livro(String nome, int ano) {
        this.nome = nome;
        this.ano = ano;
        this.disponivel = true;
    }
```

O atributo `autor` saiu, e o construtor não o recebe mais. O autor vai voltar no Passo 4, como uma classe própria.

Com os atributos fechados, quem está de fora precisa de métodos para ler e alterar os dados. Depois do método `emprestar`, acrescente:

```java title="Livro.java"
    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public int getAno() {
        return ano;
    }

    public void setAno(int ano) {
        this.ano = ano;
    }

    public boolean isDisponivel() {
        return disponivel;
    }
```

Um método que devolve um atributo é chamado de **getter**, e um que altera é um **setter**. `public` é o contrário de `private`: qualquer classe pode chamar o método. `void` indica que o método não devolve nada. Repare que `disponivel` não tem setter: a única forma de mudá-lo é o método `emprestar`.

Agora o `Main.java` precisa se adaptar, porque ele usava os atributos diretamente. Faça três ajustes:

1. Em `buscaLivro` e no `for` que imprime os livros, troque `l.nome` por `l.getNome()`.
2. Crie os três livros pelo construtor. As quatro linhas do `livro2` viram uma só:

    ```java title="Main.java"
            Livro livro1 = new Livro("1984", 1949);

            System.out.println("");
            livro1.emprestar();

            Livro livro2 = new Livro("Duna", 1965);

            Livro livro3 = new Livro("Neuromancer", 1984);
    ```

3. Apague o trecho do `HashSet<String> autores`, com o `for` dele. Ele volta no Passo 4.

**Execute.** A saída deve ser a mesma do fim da Aula 02.

!!! question "Atividade 1 — Experimentar"
    Depois da criação do `livro1`, escreva `livro1.disponivel = true;` e tente compilar. O que o compilador responde?

??? success "Confira"
    `disponivel has private access in Livro`. O programa nem chega a existir: o compilador impede o acesso. Apague a linha.

Esconder os dados e oferecer métodos para usá-los chama-se **encapsulamento**.

## Passo 2 — Métodos que impõem regras

Se getter e setter só repassam o valor, pouca coisa mudou. A vantagem aparece quando a classe usa esses métodos para impor regras. Em `Livro.java`, troque `getNome` e `setNome` por:

```java title="Livro.java"
    public String getNome() {
        return this.nome.toUpperCase();
    }

    public void setNome(String nome) {
        if (nome.length() < 3) {
            return;
        }
        this.nome = nome;
    }
```

Agora o nome é sempre devolvido em maiúsculas, e a classe recusa nomes com menos de três letras. O `return` sozinho encerra o método sem alterar nada.

No `main`, depois da criação do `livro2`, acrescente:

```java title="Main.java"
        System.out.println(livro2.getNome());
```

**Execute.** Aparece `DUNA`, e a lista de livros também sai em maiúsculas. O `Main` não precisou mudar para isso: a regra está em um lugar só, dentro da classe.

!!! question "Atividade 2 — Prever"
    Acrescente, só para testar, esta linha no fim do `main`. O que ela imprime?

    ```java
    System.out.println(buscaLivro(lista, "Duna"));
    ```

??? success "Confira"
    `false`. A busca compara com `getNome()`, que agora devolve `"DUNA"`. Buscar por `"DUNA"` daria `true`. Uma regra dentro da classe afeta todo mundo que usa o método. Apague a linha de teste.

## Passo 3 — Um livro tem uma editora

Uma editora tem nome, endereço, ano de fundação e uma lista dos livros que publicou. É uma coisa com dados e comportamento próprios, então ganha uma classe.

Crie o arquivo `Editora.java`, na mesma pasta:

```java title="Editora.java"
package br.insper.aula01;

import java.util.ArrayList;

public class Editora {

    private String nome;
    private String endereco;
    private int anoFundacao;

    private ArrayList<Livro> livros;

    public Editora(String nome, String endereco, int anoFundacao) {
        this.nome = nome;
        this.endereco = endereco;
        this.anoFundacao = anoFundacao;
        this.livros = new ArrayList<>();
    }

    public void addLivro(Livro livro) {
        if (livro == null  || livro.getNome() == null) {
            return;
        }
        this.livros.add(livro);
    }

    public void printLivros() {
        System.out.println(livros);
    }

}
```

Entre o construtor e o `addLivro`, escreva você um getter e um setter para `nome`, `endereco` e `anoFundacao`, no mesmo formato dos de `Livro` no Passo 1.

??? success "Confira: os getters e setters de `Editora`"
    ```java
        public String getNome() {
            return nome;
        }

        public void setNome(String nome) {
            this.nome = nome;
        }

        public String getEndereco() {
            return endereco;
        }

        public void setEndereco(String endereco) {
            this.endereco = endereco;
        }

        public int getAnoFundacao() {
            return anoFundacao;
        }

        public void setAnoFundacao(int anoFundacao) {
            this.anoFundacao = anoFundacao;
        }
    ```

A lista `livros` é um atributo como os outros, só que guarda objetos de outra classe. E não há um `setLivros`: a única porta de entrada é `addLivro`, que recusa `null`.

Agora o livro precisa saber qual é a sua editora. Em `Livro.java`, acrescente o atributo depois de `disponivel`:

```java title="Livro.java"
    private Editora editora;
```

E, no fim da classe, estes três métodos:

```java title="Livro.java"
    public Editora getEditora() {
        return editora;
    }

    public void setEditora(Editora editora) {
        this.editora = editora;
    }

    public String toString() {
        return nome + " " + ano;
    }
```

O `toString` diz como o objeto vira texto quando é impresso. Sem ele, imprimir um livro mostraria algo como `br.insper.aula01.Livro@251a69d7`.

No `main`, troque o trecho que cria os três livros por este:

```java title="Main.java"
        Editora editora = new Editora("Editora do Insper",
                "Rua Quata", 2000);

        editora.addLivro(null);

        editora.printLivros();

        Livro livro1 = new Livro("1984", 1949);
        livro1.setEditora(editora);
        editora.addLivro(livro1);

        editora.printLivros();

        System.out.println("");
        livro1.emprestar();

        Livro livro2 = new Livro("Duna",  1965);
        livro2.setEditora(editora);

        editora.addLivro(livro2);

        System.out.println(livro2.getNome());
        editora.printLivros();

        Livro livro3 = new Livro("Neuromancer", 1984);
        livro3.setEditora(editora);
        editora.addLivro(livro3);
```

A ligação é feita nos dois sentidos: `setEditora` conta ao livro qual é a editora dele, e `addLivro` conta à editora que ela tem mais um livro. Uma editora tem vários livros e cada livro tem uma editora. Esse é um relacionamento **um para muitos**.

!!! question "Atividade 3 — Prever"
    Antes de executar: o que o primeiro `editora.printLivros()` imprime, logo depois de `editora.addLivro(null)`? E o segundo?

??? success "Confira"
    ```text
    []
    [1984 1949]
    ```

    O primeiro mostra a lista vazia: `addLivro` recusou o `null`. O segundo já mostra o `livro1`, escrito do jeito que o `toString` definiu. Mais abaixo na saída aparece `[1984 1949, Duna 1965]`.

## Passo 4 — Um livro tem autores

Falta trazer o autor de volta. Um livro pode ter vários autores, e um autor pode ter vários livros.

Crie o arquivo `Autor.java`:

```java title="Autor.java"
package br.insper.aula01;

import java.util.ArrayList;

public class Autor {

    private String nome;
    private String localNascimento;
    private int idade;

    private ArrayList<Livro> livros;

    public Autor(String nome, String localNascimento, int idade) {
        this.nome = nome;
        this.localNascimento = localNascimento;
        this.idade = idade;
        livros = new ArrayList<>();
    }

    public ArrayList<Livro> getLivros() {
        return livros;
    }

}
```

Entre o construtor e o `getLivros`, escreva um getter e um setter para `nome`, `localNascimento` e `idade`.

Em `Livro.java`, são quatro acréscimos:

```java title="Livro.java"
import java.util.ArrayList;                        // depois da linha package

    private ArrayList<Autor> autores;              // depois do atributo editora

        this.autores = new ArrayList<>();          // última linha do construtor com parâmetros

    public ArrayList<Autor> getAutores() {         // no fim da classe
        return autores;
    }
```

No `main`, três acréscimos. Depois de `editora.addLivro(null);`:

```java title="Main.java"
        Autor autor = new Autor("Joao", "Sao Paulo", 30);
```

Depois de `editora.addLivro(livro1);`:

```java title="Main.java"
        livro1.getAutores().add(autor);
        autor.getLivros().add(livro1);
```

E, depois do `for` que imprime os livros, o trecho dos autores volta. Agora ele passa por cada livro e, dentro dele, por cada autor:

```java title="Main.java"
        HashSet<String> autores = new HashSet<>();
        for (Livro l : lista) {
            for (Autor a :l.getAutores()) {
                autores.add(a.getNome());
            }
        }
```

De novo a ligação tem dois sentidos: o livro guarda o autor e o autor guarda o livro. Como os dois lados têm uma lista, o relacionamento é **muitos para muitos**.

**Execute.** A saída completa deve ser:

```text
Biblioteca do Insper
Ano 2026

[]
[1984 1949]

DUNA
[1984 1949, Duna 1965]
===Livros===
1984
DUNA
NEUROMANCER
Ana
Bruno
Carla
Biblioteca esta aberta
false
3
```

!!! question "Atividade 4 — Prever"
    Acrescente no fim do `main`: `System.out.println(autor.getLivros());`. O que aparece?

??? success "Confira"
    `[1984 1949]`. Você nunca imprimiu o `livro1` diretamente: chegou até ele a partir do autor. Objetos ligados permitem navegar de um para o outro. Apague a linha de teste.

## O código da aula

O programa que você escreveu é o da aula. Compare com os quatro arquivos do repositório: [`Livro.java`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-03/aula/src/main/java/br/insper/aula01/Livro.java), [`Editora.java`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-03/aula/src/main/java/br/insper/aula01/Editora.java), [`Autor.java`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-03/aula/src/main/java/br/insper/aula01/Autor.java) e [`Main.java`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-03/aula/src/main/java/br/insper/aula01/Main.java).

As diferenças são só de espaçamento e uma linha comentada no fim do `main`, que já existia na aula anterior.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| `private` | O atributo só pode ser usado dentro da própria classe | Todos os atributos de `Livro`, `Editora` e `Autor` |
| Getter e setter | Métodos para ler e alterar um atributo | `getAno()`, `setAno()` |
| Encapsulamento | A classe esconde os dados e controla como eles mudam | `setNome` recusa nomes curtos |
| `toString` | Define o texto que representa o objeto | `Livro` |
| Um para muitos | Um objeto ligado a vários de outra classe | `Editora` e seus livros |
| Muitos para muitos | Os dois lados guardam uma lista do outro | `Livro` e `Autor` |

Quer praticar? Continue em [Exercícios](exercicios.md).
