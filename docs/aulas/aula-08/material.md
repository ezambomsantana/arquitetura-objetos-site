# Aula 08 — Documentação, DTOs e tratamento de erros

!!! abstract "Nesta aula"
    - **Você parte de:** a API da [Aula 06](../aula-06/material.md).
    - **Você termina com:** uma API documentada, organizada em pacotes e com mensagens de erro claras.
    - **Ideias novas:** Swagger, pacote, DTO e exceção.
    - **Código pronto:** [`aula-08/biblioteca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-08/biblioteca)

## O que vamos construir

A API da biblioteca funciona, mas ainda é difícil de usar e de manter. Nesta aula ela ganha quatro melhorias:

- uma página de **documentação**, gerada sozinha, onde dá para testar as rotas pelo navegador;
- os arquivos organizados em **pacotes**;
- uma rota que devolve um dado que não é nenhuma das classes da biblioteca, usando um **DTO**;
- respostas de **erro** com o código certo e uma mensagem.

## Antes de começar

Parta da API que você terminou na [Aula 06](../aula-06/material.md). Continue usando `./mvnw spring-boot:run` e reiniciando o servidor a cada passo.

## Passo 1 — Documentação com Swagger

Até aqui, para saber quais rotas existem, alguém precisava ler os controllers. O Swagger lê os controllers por você e monta uma página com todas as rotas.

Abra o `pom.xml` e, dentro de `<dependencies>`, acrescente:

```xml title="pom.xml"
		<dependency>
			<groupId>org.springdoc</groupId>
			<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
			<version>3.1.0</version>
		</dependency>
```

Uma **dependência** é uma biblioteca que o Maven baixa e junta ao projeto. Você não escreve nenhuma linha de Java neste passo.

**Execute** e abra <http://localhost:8080/swagger-ui.html>.

!!! question "Atividade 1 — Experimentar"
    Na página, abra `POST /editora`, clique em **Try it out**, preencha o JSON de uma editora e clique em **Execute**. Depois faça o mesmo em `GET /editora`.

??? success "Confira"
    A página mostra o código `201` e a editora criada, com o `id`. No `GET`, a editora aparece na lista. É o mesmo que o `curl` fazia. Daqui em diante, você pode testar tudo por essa página.

## Passo 2 — Organizar em pacotes

Todos os arquivos estão na mesma pasta, e a aula vai criar mais cinco. Vamos separar por assunto. Dentro de `br/edu/insper/biblioteca`, crie as pastas e mova os arquivos:

| Arquivo | Vai para a pasta |
| --- | --- |
| `Autor.java` | `autor` |
| `Editora.java` | `editora/model` |
| `EditoraService.java` | `editora/service` |
| `EditoraController.java` | `editora/controller` |
| `Livro.java`, `LivroService.java`, `LivroController.java` | `livro` |

`BibliotecaApplication.java` fica onde está.

Em Java, a pasta de um arquivo é o seu **pacote**, e isso traz duas obrigações:

1. A primeira linha do arquivo precisa dizer o pacote. Em `Editora.java`, por exemplo, ela passa a ser `package br.edu.insper.biblioteca.editora.model;`.
2. Uma classe que usa outra de um pacote diferente precisa de um `import`.

Em uma IDE, mover o arquivo com a opção de refatorar faz as duas coisas sozinha. À mão, ajuste a linha `package` dos sete arquivos e acrescente estes `import`:

| Arquivo | `import` a acrescentar |
| --- | --- |
| `Autor.java` | `br.edu.insper.biblioteca.livro.Livro` |
| `Editora.java` | `br.edu.insper.biblioteca.livro.Livro` |
| `EditoraService.java` | `br.edu.insper.biblioteca.editora.model.Editora` |
| `EditoraController.java` | `br.edu.insper.biblioteca.editora.model.Editora` e `br.edu.insper.biblioteca.editora.service.EditoraService` |
| `Livro.java` | `br.edu.insper.biblioteca.autor.Autor` e `br.edu.insper.biblioteca.editora.model.Editora` |
| `LivroService.java` | `br.edu.insper.biblioteca.editora.model.Editora` e `br.edu.insper.biblioteca.editora.service.EditoraService` |

**Execute.** A API deve funcionar exatamente como antes. Se a compilação reclamar de `cannot find symbol`, falta um `import` no arquivo indicado.

## Passo 3 — Uma resposta sob medida: o DTO

Queremos uma rota que diga quantos livros cada editora tem. A resposta é uma lista de pares "nome da editora, quantidade". Isso não é uma `Editora` nem um `Livro`.

Para respostas assim, cria-se uma classe só para transportar os dados: um **DTO**, sigla de *Data Transfer Object*.

Primeiro, a editora precisa saber contar. Em `Editora.java`, acrescente o método abaixo, se você ainda não o tem dos exercícios anteriores:

```java title="Editora.java"
    public int contaLivros() {
        return livros.size();
    }
```

Crie a pasta `editora/dto` e, nela, o arquivo `EditoraLivrosDTO.java`:

```java title="EditoraLivrosDTO.java"
package br.edu.insper.biblioteca.editora.dto;

public class EditoraLivrosDTO {

    private String nome;
    private int numeroLivros;

    public EditoraLivrosDTO(String nome, int numeroLivros) {
        this.nome = nome;
        this.numeroLivros = numeroLivros;
    }

}
```

Depois do construtor, escreva um getter e um setter para `nome` e para `numeroLivros`. Eles são necessários: o Spring monta o JSON a partir dos getters.

Em `EditoraService.java`, acrescente `import br.edu.insper.biblioteca.editora.dto.EditoraLivrosDTO;` e o método:

```java title="EditoraService.java"
    public ArrayList<EditoraLivrosDTO> getNumeroLivrosEditora() {
        ArrayList<EditoraLivrosDTO> editorasLivro = new ArrayList<>();
        for (Editora editora : editoras.values()) {
            EditoraLivrosDTO livros =
                    new EditoraLivrosDTO(editora.getNome(),editora.contaLivros());
            editorasLivro.add(livros);
        }
        return editorasLivro;
    }
```

Em `EditoraController.java`, acrescente o mesmo `import` e a rota:

```java title="EditoraController.java"
    @GetMapping("/editora/conta-livros")
    public ArrayList<EditoraLivrosDTO> getContaLivros() {
        return editoraService.getNumeroLivrosEditora();
    }
```

**Execute.** Pelo Swagger, cadastre duas editoras e um livro ligado a uma delas. Depois chame `GET /editora/conta-livros`:

```json
[{"nome":"Companhia das Letras","numeroLivros":0},{"nome":"Editora do Insper","numeroLivros":1}]
```

A resposta tem só os dois campos do DTO. O CNPJ, o endereço e o `id` das editoras ficaram de fora, porque o DTO não os tem.

## Passo 4 — Erros com código e mensagem

Peça `GET /editora/abc` no Swagger. A resposta é `200`, vazia. O código diz "deu certo", mas a editora não existe. Quem usa a API não tem como saber o que aconteceu.

O conserto tem duas partes: o service **avisa** que algo deu errado, e uma classe separada **transforma** o aviso em uma resposta.

**O aviso.** Em Java, um erro é sinalizado lançando uma **exceção**, com `throw`. É o `raise` do Python. Crie a pasta `editora/exception` e, nela, uma exceção própria:

```java title="EditoraNaoEncontradaException.java"
package br.edu.insper.biblioteca.editora.exception;

public class EditoraNaoEncontradaException extends RuntimeException {

    public EditoraNaoEncontradaException(String message) {
        super(message);
    }

}
```

`extends RuntimeException` quer dizer que a classe é um tipo de exceção do Java, e `super(message)` guarda a mensagem.

Em `EditoraService.java`, acrescente `import br.edu.insper.biblioteca.editora.exception.EditoraNaoEncontradaException;` e troque os métodos `buscar` e `excluir` por:

```java title="EditoraService.java"
    public Editora buscar(String id) {
        Editora editora = editoras.get(id);
        if (editora == null) {
            throw new EditoraNaoEncontradaException("Editora não encontrada");
        }
        return editora;
    }

    public void excluir(String id) {
        buscar(id);
        editoras.remove(id);
    }
```

O `throw` interrompe o método na hora. Por isso `excluir` ficou curto: se a editora não existir, `buscar` lança a exceção e a linha do `remove` nem é executada.

No começo do método `criar`, acrescente também uma validação:

```java title="EditoraService.java"
        if (editora.getNome() == null || editora.getNome().isEmpty()) {
            throw new RuntimeException("Nome da editora não pode ser null ou vazio");
        }
```

!!! question "Atividade 2 — Observar"
    Execute e peça `GET /editora/abc` de novo. Qual é o código agora?

??? success "Confira"
    `500`, "Internal Server Error". A exceção saiu do service, passou pelo controller e ninguém a tratou. O Spring respondeu com um erro genérico. Já não é um `200` enganoso, mas o código ainda está errado e a mensagem não aparece.

**A resposta.** Falta dizer ao Spring o que responder quando essa exceção acontecer. Primeiro, um DTO para o corpo do erro. Crie a pasta `utils` e, nela:

```java title="ErrorResponseDTO.java"
package br.edu.insper.biblioteca.utils;

import java.time.LocalDateTime;

public class ErrorResponseDTO {

    private String message;
    private int statusCode;
    private LocalDateTime date;

}
```

Escreva um getter e um setter para os três atributos.

Agora o tratador. Na pasta `editora/exception`, crie:

```java title="EditoraErrorHandler.java"
package br.edu.insper.biblioteca.editora.exception;

import br.edu.insper.biblioteca.utils.ErrorResponseDTO;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.ResponseStatus;

import java.time.LocalDateTime;

@ControllerAdvice
public class EditoraErrorHandler {

    @ExceptionHandler(EditoraNaoEncontradaException.class)
    @ResponseBody
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ErrorResponseDTO handleRuntimeException(EditoraNaoEncontradaException re) {

        ErrorResponseDTO errorResponseDTO = new ErrorResponseDTO();
        errorResponseDTO.setDate(LocalDateTime.now());
        errorResponseDTO.setMessage(re.getMessage());
        errorResponseDTO.setStatusCode(404);
        return errorResponseDTO;

    }

}
```

| Anotação | O que diz ao Spring |
| --- | --- |
| `@ControllerAdvice` | "Esta classe cuida de erros de todos os controllers." |
| `@ExceptionHandler(...)` | "Chame este método quando esta exceção acontecer." |
| `@ResponseStatus(HttpStatus.NOT_FOUND)` | "Responda com o código 404." |
| `@ResponseBody` | "O que o método devolve é o corpo da resposta." |

**Execute** e peça `GET /editora/abc`:

```json
{"date":"2026-10-13T13:10:47.185563","message":"Editora não encontrada","statusCode":404}
```

Agora o código é `404` e a mensagem diz o que houve.

!!! question "Atividade 3 — Prever"
    Você só mexeu em classes de editora. O que acontece ao cadastrar um **livro** com o `id` de uma editora que não existe? Na Aula 06, a resposta era um `201` vazio.

??? success "Confira"
    `404`, com a mensagem `Editora não encontrada`. O `LivroService` chama `editoraService.buscar`, que agora lança a exceção. Ela atravessa o `LivroService` e o `LivroController` até chegar ao tratador. Nenhum dos dois precisou mudar.

## O código da aula

O programa que você escreveu é o da aula. Compare com os arquivos da pasta [`aula-08/biblioteca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-08/biblioteca/src/main/java/br/edu/insper/biblioteca) no repositório. As diferenças são pequenas:

- no repositório, o método `getEditora` do controller declara que devolve `Object` em vez de `Editora`;
- há sobras que a API não usa: o método `imprimirLivros`, um trecho comentado e dois `import`.

Um ponto fica em aberto: cadastrar uma editora sem nome responde `500`, porque a validação lança uma `RuntimeException` comum e não há tratador para ela. O [Exercício 2](exercicios.md#exercicio-2-nome-invalido-com-o-codigo-certo) resolve isso.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| Dependência do Maven | Uma biblioteca que o Maven junta ao projeto | O Swagger, no `pom.xml` |
| Pacote | A pasta de uma classe, que entra no nome completo dela | `editora.model`, `livro` |
| DTO | Uma classe só para transportar dados de uma resposta | `EditoraLivrosDTO`, `ErrorResponseDTO` |
| Exceção | Um aviso de erro que interrompe o método | `EditoraNaoEncontradaException` |
| `throw` | Lança a exceção | Em `EditoraService.buscar` |
| Tratador de erros | A classe que transforma exceções em respostas | `EditoraErrorHandler` |

Quer praticar? Continue em [Exercícios](exercicios.md).
