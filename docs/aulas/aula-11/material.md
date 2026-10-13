# Aula 11 — Herança, classes abstratas e interfaces

!!! abstract "Nesta aula"
    - **Você parte de:** do zero. É um programa novo, em Java puro.
    - **Você termina com:** um cadastro de alunos, professores e coordenadores.
    - **Ideias novas:** herança, sobrescrita, classe abstrata e interface.
    - **Código pronto:** [`aula-11/heranca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-11/heranca)

## O que vamos construir

Esta aula deixa a API de lado e volta ao Java puro, com um programa novo e pequeno: o cadastro de pessoas de uma faculdade, com alunos, professores e coordenadores.

Alunos e professores têm muito em comum, como nome e CPF, e algumas diferenças. O programa serve para aprender três formas de lidar com isso:

- **herança**: uma classe aproveita os atributos e métodos de outra;
- **classe abstrata**: uma classe que existe só para ser herdada, e obriga as filhas a ter certos métodos;
- **interface**: um contrato que classes diferentes podem cumprir.

## Antes de começar

Você usa o mesmo JDK das outras aulas. Crie a pasta do projeto, como na [Aula 02](../aula-02/material.md):

```bash
mkdir -p heranca/src/main/java/org/example
cd heranca
```

Todos os arquivos ficam em `src/main/java/org/example` e começam com `package org.example;`. Sempre que o roteiro disser **execute**:

```bash
javac -d target/classes src/main/java/org/example/*.java
java -cp target/classes org.example.Main
```

## Passo 1 — Herança: o que alunos e professores têm em comum

Se `Aluno` e `Professor` fossem escritas separadas, as duas repetiriam CPF, nome, endereço e data de nascimento, com todos os getters e setters. Em vez disso, o que é comum vai para uma classe só.

Crie `Pessoa.java`:

```java title="Pessoa.java"
package org.example;

import java.time.LocalDate;

public class Pessoa {

    private String cpf;
    private String nome;
    private String endereco;
    private LocalDate dataNascimento;

}
```

Escreva um getter e um setter para os quatro atributos.

Agora `Aluno.java`, só com o que é próprio do aluno:

```java title="Aluno.java"
package org.example;

public class Aluno extends Pessoa {

    private String ra;
    private String curso;
    private int anoIngresso;
    private int semestreIngresso;

}
```

E `Professor.java`:

```java title="Professor.java"
package org.example;

public class Professor extends Pessoa {

    private String departamento;
    private Double salario;

}
```

Escreva os getters e setters das duas classes.

`extends Pessoa` quer dizer que a classe **herda** de `Pessoa`: todo aluno é uma pessoa, e tem tudo o que uma pessoa tem. `Pessoa` é a classe mãe, ou superclasse; `Aluno` e `Professor` são as filhas, ou subclasses.

Crie `Main.java`:

```java title="Main.java"
package org.example;

public class Main {
    public static void main(String[] args) {

        Professor professor = new Professor();
        professor.setNome("Carla");
        professor.setCpf("111");
        professor.setDepartamento("Computação");

        Aluno aluno = new Aluno();
        aluno.setNome("Bruno");
        aluno.setCpf("222");
        aluno.setRa("RA123");
        aluno.setCurso("Engenharia");

        System.out.println(aluno.getNome());
        System.out.println(aluno.getCurso());
        System.out.println(professor.getNome());

    }
}
```

**Execute.** Aparecem `Bruno`, `Engenharia` e `Carla`. A classe `Aluno` não tem `setNome` nem `getNome` escritos nela, mas o aluno os tem, porque herdou de `Pessoa`.

!!! question "Atividade 1 — Experimentar"
    Dentro da classe `Aluno`, crie um método que tente usar o atributo `nome` diretamente, como `System.out.println(nome);`. Compile.

??? success "Confira"
    O compilador responde `nome has private access in Pessoa`. O aluno tem um nome, mas `private` continua valendo: só a própria classe `Pessoa` mexe no atributo. A filha usa `getNome()`, como qualquer outra classe. Apague o método de teste, e as três linhas de `println` do `main`.

## Passo 2 — Sobrescrita: a filha faz do seu jeito

A faculdade tem salas que podem ser reservadas. Para reservar, a pessoa apresenta um documento. Para a maioria, é o CPF; para o aluno, é o RA.

Em `Pessoa.java`, acrescente o comportamento padrão:

```java title="Pessoa.java"
    public String getDocumento() {
        return cpf;
    }
```

Em `Aluno.java`, acrescente uma versão própria do mesmo método:

```java title="Aluno.java"
    @Override
    public String getDocumento() {
        return ra;
    }
```

Quando a filha escreve um método com o mesmo nome e os mesmos parâmetros de um método da mãe, a versão da filha passa a valer para os objetos dela. Isso é **sobrescrever**. O `@Override` avisa o compilador da intenção, e ele reclama se o nome estiver errado.

Crie `Sala.java`:

```java title="Sala.java"
package org.example;

public class Sala {


    private String nomeSala;
    private boolean disponivel = true;
    private String cpf;

    public void reservar(Pessoa p) {
        if (disponivel) {
            disponivel = false;
            cpf = p.getDocumento();

        }
    }

    public void finalizarReserva() {
        disponivel = true;
    }

    public void imprimirSala() {
        System.out.println(nomeSala);
        System.out.println(disponivel);
        System.out.println(cpf);
    }

}
```

Antes do `imprimirSala`, escreva os getters e setters de `nomeSala`, `disponivel` e `cpf`.

Repare no parâmetro de `reservar`: ele é do tipo `Pessoa`. Como todo aluno é uma pessoa, e todo professor também, o método aceita os dois.

No fim do `main`, acrescente:

```java title="Main.java"
        Sala sala = new Sala();
        sala.setNomeSala("311");
        sala.reservar(aluno);
        sala.imprimirSala();
```

!!! question "Atividade 2 — Prever"
    A sala guarda o documento de quem reservou. O que aparece na terceira linha impressa: `222` ou `RA123`? E se você trocar `reservar(aluno)` por `reservar(professor)`?

??? success "Confira"
    Com o aluno, `RA123`. Com o professor, `111`.

    O método `reservar` só sabe que recebeu uma `Pessoa` e chama `getDocumento()`. Qual versão é executada depende do objeto que chegou: a do `Aluno`, se for um aluno; a da `Pessoa`, se for um professor. A mesma chamada com resultados diferentes conforme o objeto chama-se **polimorfismo**. Volte para `reservar(aluno)` antes de continuar.

## Passo 3 — Classe abstrata: uma classe que só existe para ser herdada

Queremos que toda pessoa saiba se apresentar, mas cada tipo se apresenta de um jeito. E não faz sentido existir uma "pessoa" que não seja aluno nem professor.

Em `Pessoa.java`, acrescente `abstract` na declaração da classe e um método sem corpo:

```java title="Pessoa.java"
public abstract class Pessoa {
```

```java title="Pessoa.java"
    public abstract void apresentacao();
```

Uma classe **abstrata** não pode ser usada com `new`. Um método abstrato não tem corpo: ele só diz "toda filha precisa ter este método".

!!! question "Atividade 3 — Observar"
    Compile agora, sem mexer em mais nada. O que o compilador diz?

??? success "Confira"
    `Aluno is not abstract and does not override abstract method apresentacao() in Pessoa`, e o mesmo para `Professor`. O compilador está cobrando a promessa: as filhas ainda não têm o método.

Cumpra a promessa. Em `Aluno.java`:

```java title="Aluno.java"
    public void apresentacao() {
        System.out.println(getNome());
        System.out.println(curso);
    }
```

Em `Professor.java`:

```java title="Professor.java"
    public void apresentacao() {
        System.out.println(getNome());
        System.out.println(departamento);
    }
```

A herança pode ter mais de um nível. Crie `Coordenador.java`:

```java title="Coordenador.java"
package org.example;

public class Coordenador extends Professor {
}
```

A classe não tem nada escrito, e mesmo assim um coordenador tem tudo o que um professor tem, que por sua vez tem tudo o que uma pessoa tem.

No `main`, acrescente `import java.util.ArrayList;` e `import java.util.List;` depois da linha `package`. Depois do bloco do aluno, crie um coordenador e uma lista de pessoas:

```java title="Main.java"
        Coordenador coordenador = new Coordenador();
        coordenador.setNome("Ana");
        coordenador.setCpf("333");
        coordenador.setDepartamento("Computação");

        List<Pessoa> pessoas = new ArrayList<>();
        pessoas.add(professor);
        pessoas.add(aluno);
        pessoas.add(coordenador);

        for (Pessoa p : pessoas) {
            p.apresentacao();
        }
```

**Execute.** Antes das linhas da sala, aparecem:

```text
Carla
Computação
Bruno
Engenharia
Ana
Computação
```

A lista é de `Pessoa`, e guarda três objetos de classes diferentes. O `for` chama `apresentacao()` em cada um sem saber o que é. O coordenador se apresentou como professor, porque herdou o método.

## Passo 4 — Interface: um contrato

Na faculdade, professores e alunos fazem avaliações. Avaliar não é algo que toda pessoa faz, então não cabe em `Pessoa`. É uma **capacidade** que algumas classes têm.

Crie `Avaliar.java`:

```java title="Avaliar.java"
package org.example;

public interface Avaliar {

    public void avaliar(Pessoa p);

}
```

Uma **interface** é um contrato: uma lista de métodos, sem corpo. A classe que assina o contrato, com `implements`, é obrigada a ter esses métodos.

Assine o contrato nas duas classes. Em `Aluno.java` e em `Professor.java`, mude a declaração e acrescente o método:

```java title="Aluno.java"
public class Aluno extends Pessoa implements Avaliar {
```

```java title="Professor.java"
public class Professor extends Pessoa implements Avaliar {
```

```java title="Aluno.java e Professor.java"
    @Override
    public void avaliar(Pessoa p) {

    }
```

No código da aula, o corpo do método fica vazio. Preenchê-lo é o [Exercício 1](exercicios.md#exercicio-1-dar-corpo-a-avaliacao).

No `main`, depois do bloco do coordenador, acrescente:

```java title="Main.java"
        List<Avaliar> avaliadores = new ArrayList<>();
        avaliadores.add(professor);
        avaliadores.add(aluno);

        for (Avaliar a : avaliadores) {
            a.avaliar(coordenador);
        }
```

**Execute.** A saída não muda, porque `avaliar` ainda não faz nada. O que importa é que compila: a lista é de `Avaliar` e aceita qualquer objeto cuja classe tenha assinado o contrato.

Você já usou uma interface sem saber. Na Aula 09, `UsuarioService implements UserDetailsService` era isso: o Spring Security aceita qualquer classe que cumpra aquele contrato. E `List`, que você acabou de usar, também é uma interface; `ArrayList` é uma das classes que a cumprem.

!!! question "Atividade 4 — Prever"
    `Coordenador` não tem `implements Avaliar` escrito. A linha `avaliadores.add(coordenador);` compila?

??? success "Confira"
    Compila. `Coordenador` herda de `Professor`, e isso inclui o contrato que o professor assinou.

## O código da aula

As classes `Pessoa`, `Aluno`, `Professor`, `Coordenador`, `Sala` e a interface `Avaliar` que você escreveu são as da aula. Compare com os arquivos da pasta [`aula-11/heranca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-11/heranca/src/main/java/org/example) no repositório.

O `Main.java` é diferente, e por um motivo: **o `main` do repositório não compila**. Ele parece um rascunho feito durante a aula, com dois problemas:

- chama `a.avaliar()` sem passar a pessoa que o método exige;
- chama `sala.reservar(p)` depois do `for`, onde a variável `p` não existe mais.

O `main` deste roteiro faz as mesmas três coisas, na mesma ordem das do repositório, e corrige esses dois pontos. Ele também dá nome às pessoas, para a saída fazer sentido.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| Herança | Uma classe recebe os atributos e métodos de outra | `Aluno extends Pessoa` |
| Sobrescrita | A filha substitui um método da mãe | `getDocumento()` em `Aluno` |
| Polimorfismo | A mesma chamada age conforme o objeto | `p.getDocumento()` em `Sala.reservar` |
| Classe abstrata | Classe que não pode ser criada com `new` | `Pessoa` |
| Método abstrato | Método sem corpo, que as filhas são obrigadas a ter | `apresentacao()` |
| Interface | Contrato de métodos que qualquer classe pode assinar | `Avaliar` |

Quer praticar? Continue em [Exercícios](exercicios.md).
