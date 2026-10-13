# Aula 05 — Primeira API REST com Spring Boot

!!! abstract "Nesta aula"
    - **Você parte de:** as classes da [Aula 03](../aula-03/material.md), dentro de um projeto novo.
    - **Você termina com:** uma API que cadastra, busca, lista e exclui editoras e livros.
    - **Ideias novas:** API REST, rota, controller e service.
    - **Código pronto:** [`aula-05/biblioteca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-05/biblioteca)

## O que vamos construir

Até aqui, a biblioteca era um programa que rodava, imprimia e terminava. Agora ela vira um **servidor**: fica no ar esperando pedidos pela rede e responde a cada um.

Esse servidor é uma **API REST**. Outro programa manda uma requisição HTTP, como "cadastre esta editora" ou "liste os livros", e recebe a resposta em JSON. Quem cuida da parte de rede é o **Spring Boot**; você escreve só as classes da biblioteca.

Três termos vão aparecer o tempo todo:

| Termo | O que é |
| --- | --- |
| Requisição HTTP | Um pedido ao servidor, formado por um **verbo** e um endereço. `GET` pede dados, `POST` cria e `DELETE` exclui. |
| JSON | Um formato de texto para dados, parecido com um dicionário do Python: `{"nome":"Duna","ano":1965}`. |
| Código de status | Um número que resume a resposta. Os que começam com 2 indicam sucesso; com 4, um problema no pedido; com 5, um erro no servidor. |

## Antes de começar

Você precisa de um JDK 25 ou mais novo e das classes `Livro`, `Editora` e `Autor` que terminou na [Aula 03](../aula-03/material.md).

Para fazer requisições, o roteiro usa o `curl`, que já vem no macOS, no Linux e no Windows recente. Se preferir, use o Postman ou outro cliente HTTP com os mesmos endereços e dados.

## Passo 1 — Criar o projeto

Abra [start.spring.io](https://start.spring.io) e preencha:

| Campo | Valor |
| --- | --- |
| Project | Maven |
| Language | Java |
| Spring Boot | a versão 4 que vier marcada |
| Group | `br.edu.insper` |
| Artifact | `biblioteca` |
| Java | 25 |
| Dependencies | Spring Web |

Clique em **Generate**, descompacte o arquivo e, dentro da pasta `biblioteca`, execute:

```bash
./mvnw spring-boot:run
```

No Windows, use `mvnw.cmd spring-boot:run`. A primeira execução demora, porque baixa o Spring.

O `mvnw` é o **Maven**, a ferramenta que baixa as bibliotecas do projeto, compila e executa. A lista de bibliotecas fica no arquivo `pom.xml`.

Quando aparecer `Started BibliotecaApplication`, abra <http://localhost:8080> no navegador. Você verá uma página de erro 404. É o esperado: o servidor está no ar, mas ainda não sabe responder a nada.

Para parar o servidor, use `Ctrl+C`. **A cada passo, pare e execute de novo** para o código novo valer.

## Passo 2 — Trazer as classes da biblioteca

Copie `Livro.java`, `Editora.java` e `Autor.java` da Aula 03 para a pasta `src/main/java/br/edu/insper/biblioteca`, ao lado de `BibliotecaApplication.java`. O `Main.java` não vem: quem inicia o programa agora é o Spring.

Nos três arquivos, troque a primeira linha por:

```java
package br.edu.insper.biblioteca;
```

Em uma API, cada objeto precisa de um identificador, para que alguém possa pedir "a editora tal" ou "o livro tal". Em `Editora.java`, acrescente dois atributos, receba o CNPJ no construtor e crie os getters e setters:

```java title="Editora.java"
    private String id;
    private String cnpj;
```

```java title="Editora.java"
    public Editora(String cnpj, String nome, String endereco, int anoFundacao) {
        this.cnpj = cnpj;
        this.nome = nome;
        this.endereco = endereco;
        this.anoFundacao = anoFundacao;
        this.livros = new ArrayList<>();
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getCnpj() {
        return cnpj;
    }

    public void setCnpj(String cnpj) {
        this.cnpj = cnpj;
    }
```

Em `Livro.java`, o identificador é o ISBN. Acrescente o atributo, troque o construtor e o `toString`, e crie o getter e o setter:

```java title="Livro.java"
    private String isbn;
```

```java title="Livro.java"
    public Livro(String isbn, String nome, int ano) {
        this.isbn = isbn;
        this.nome = nome;
        this.ano = ano;
        this.disponivel = true;
        this.autores = new ArrayList<>();
    }

    public String getIsbn() {
        return isbn;
    }

    public void setIsbn(String isbn) {
        this.isbn = isbn;
    }

    public String toString() {
        return nome + " " + ano + "  " + editora.getNome();
    }
```

**Execute.** O servidor deve subir sem erros. Nada mudou para quem acessa, porque ainda não há rotas.

## Passo 3 — A primeira rota: cadastrar e buscar uma editora

Vamos dividir o trabalho em duas classes, cada uma com um papel:

- o **service** guarda as editoras e sabe criar, buscar e excluir;
- o **controller** recebe a requisição HTTP e chama o service.

Crie `EditoraService.java`:

```java title="EditoraService.java"
package br.edu.insper.biblioteca;

import java.util.HashMap;
import java.util.UUID;

public class EditoraService {

    private HashMap<String, Editora> editoras = new HashMap<>();

    public Editora criar(Editora editora) {
        editora.setId(UUID.randomUUID().toString());
        editoras.put(editora.getId(), editora);
        return editora;
    }

    public Editora buscar(String id) {
        return editoras.get(id);
    }

}
```

É uma classe Java comum, sem nada de Spring. As editoras ficam em um `HashMap`, em que a chave é o `id`. O `UUID` gera um identificador aleatório para cada editora nova.

Crie `EditoraController.java`:

```java title="EditoraController.java"
package br.edu.insper.biblioteca;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestController
public class EditoraController {

    private EditoraService editoraService = new EditoraService();

    @PostMapping("/editora")
    @ResponseStatus(HttpStatus.CREATED)
    public Editora criarEditora(@RequestBody Editora editora) {
        return editoraService.criar(editora);
    }

    @GetMapping("/editora/{id}")
    public Editora getEditora(@PathVariable String id) {
        return editoraService.buscar(id);
    }

}
```

O controller cria o seu service com `new`, como você sempre fez. As palavras com `@` são **anotações**: avisos para o Spring sobre o que cada coisa é.

| Anotação | O que diz ao Spring |
| --- | --- |
| `@RestController` | "Esta classe atende requisições. Crie um objeto dela." |
| `@PostMapping("/editora")` | "Chame este método quando chegar um `POST` em `/editora`." |
| `@RequestBody` | "Monte o objeto a partir do JSON que veio na requisição." |
| `@PathVariable` | "Pegue este valor do endereço." |
| `@ResponseStatus` | "Responda com este código." `CREATED` é o 201. |

**Execute** e, em outro terminal, cadastre uma editora:

```bash
curl -i -X POST http://localhost:8080/editora \
  -H "Content-Type: application/json" \
  -d '{"cnpj":"1","nome":"Editora do Insper","endereco":"Rua Quata","anoFundacao":1999}'
```

Cada parte do comando tem um papel:

| Parte | O que faz |
| --- | --- |
| `-i` | Mostra o código de status na primeira linha da resposta. |
| `-X POST` | Define o verbo da requisição. |
| `-H "Content-Type: application/json"` | Avisa que os dados enviados estão em JSON. |
| `-d '...'` | São os dados enviados. |

A resposta deve ter `201` na primeira linha e, no fim, a editora com o `id` que o service gerou:

```json
{"cnpj":"1","nome":"Editora do Insper","endereco":"Rua Quata","anoFundacao":1999,"id":"66726c12-fecf-4bd5-9b09-0266ebfa405f"}
```

Você mandou um JSON e o Spring o transformou em um objeto `Editora`. Na volta, transformou o objeto em JSON usando os getters. O seu `id` será diferente do exemplo.

!!! question "Atividade 1 — Experimentar"
    Copie o `id` da resposta e abra `http://localhost:8080/editora/SEU_ID` no navegador. Depois troque o `id` por `abc`. O que muda?

??? success "Confira"
    Com o `id` certo, aparece o JSON da editora. Com `abc`, a página fica em branco: o service devolveu `null` e o Spring respondeu com sucesso e sem conteúdo. Avisar direito que a editora não existe é assunto de uma aula mais à frente.

## Passo 4 — Listar e excluir

Em `EditoraService.java`, acrescente `import java.util.ArrayList;` junto aos outros e estes dois métodos:

```java title="EditoraService.java"
    public boolean excluir(String id) {
        Editora editora = buscar(id);
        if (editora != null) {
            editoras.remove(id);
            return true;
        }
        return false;
    }

    public ArrayList<Editora> listar(String nome) {
        if (nome != null) {

            ArrayList<Editora> response = new ArrayList<>();
            for (Editora editora : editoras.values()) {

                if (editora.getNome().startsWith(nome)) {
                    response.add(editora);
                }
            }
            return response;
        }
        return new ArrayList<>(editoras.values());
    }
```

Em `EditoraController.java`, acrescente `import java.util.ArrayList;` e as duas rotas:

```java title="EditoraController.java"
    @DeleteMapping("/editora/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void deleteEditora(@PathVariable String id) {
        editoraService.excluir(id);
    }

    @GetMapping("/editora")
    public ArrayList<Editora> getEditoras(@RequestParam(required = false) String nome) {
        return editoraService.listar(nome);
    }
```

`@RequestParam` pega um valor que vem depois do `?` no endereço. Como ele é opcional, `/editora` lista tudo e `/editora?nome=Comp` lista só as editoras cujo nome começa com `Comp`.

**Execute**, cadastre duas editoras com o `curl` do passo anterior, mudando o nome da segunda, e abra <http://localhost:8080/editora> no navegador. Para excluir:

```bash
curl -i -X DELETE http://localhost:8080/editora/SEU_ID
```

A resposta é `204`, que significa "deu certo e não há nada para devolver".

!!! question "Atividade 2 — Prever"
    Pare o servidor, execute de novo e abra <http://localhost:8080/editora>. O que aparece?

??? success "Confira"
    `[]`, uma lista vazia. As editoras estavam no `HashMap`, que vive na memória do programa. Quando o servidor para, tudo se perde. Guardar os dados em um banco é assunto de uma aula mais à frente.

## Passo 5 — Os livros

Os livros seguem o mesmo desenho: um service que guarda e um controller que atende. A chave do `HashMap` agora é o ISBN.

Crie `LivroService.java`:

```java title="LivroService.java"
package br.edu.insper.biblioteca;

import java.util.ArrayList;
import java.util.HashMap;

public class LivroService {

    public HashMap<String, Livro> livros = new HashMap<>();

    public Livro criar(Livro livro) {
        livros.put(livro.getIsbn(), livro);
        return livro;
    }

    public Livro buscar(String isbn) {
        return livros.get(isbn);
    }

    public boolean excluir(String isbn) {
        Livro livro = buscar(isbn);
        if (livro != null) {
            livros.remove(isbn);
            return true;
        }
        return false;
    }

    public ArrayList<Livro> listar(String nome) {

        if (nome != null) {

            ArrayList<Livro> response = new ArrayList<>();
            for (Livro livro : livros.values()) {

                if (livro.getNome().startsWith(nome)) {
                    response.add(livro);
                }
            }
            return response;
        }
        return new ArrayList<>(livros.values());
    }
}
```

Crie `LivroController.java`:

```java title="LivroController.java"
package br.edu.insper.biblioteca;


import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;

@RestController
public class LivroController {

    private LivroService livroService = new LivroService();

    @PostMapping("/livro")
    @ResponseStatus(HttpStatus.CREATED)
    public Livro criarLivro(@RequestBody Livro livro) {
        return livroService.criar(livro);
    }

    @GetMapping("/livro/{isbn}")
    public Livro getLivro(@PathVariable String isbn) {
        return livroService.buscar(isbn);
    }

    @DeleteMapping("/livro/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void deleteLivro(@PathVariable String id) {
        livroService.excluir(id);
    }

    @GetMapping("/livro")
    public ArrayList<Livro> getLivros(@RequestParam(required = false) String nome) {
        return livroService.listar(nome);
    }


}
```

**Execute** e cadastre um livro:

```bash
curl -i -X POST http://localhost:8080/livro \
  -H "Content-Type: application/json" \
  -d '{"isbn":"123","nome":"Duna","ano":1965}'
```

A resposta é `201`, com o livro:

```json
{"isbn":"123","nome":"DUNA","ano":1965,"autores":[],"disponivel":true,"editora":null}
```

O JSON mostra o que os getters devolvem. Por isso o nome saiu em maiúsculas, como o `getNome()` da Aula 03 manda.

!!! question "Atividade 3 — Prever"
    Você cadastrou o livro com o nome `Duna`. Qual destes endereços encontra o livro: `/livro?nome=Du` ou `/livro?nome=DU`? Teste no navegador.

??? success "Confira"
    Só `/livro?nome=DU`. O filtro usa `getNome()`, que devolve o nome em maiúsculas.

Repare no fim do JSON: `"editora":null`. O livro foi cadastrado sem editora. Para ligá-lo a uma editora já cadastrada, o `LivroService` vai precisar conversar com o `EditoraService`, e isso traz um problema novo. É o assunto da [Aula 06](../aula-06/material.md).

## O código da aula

O programa que você escreveu é o da aula. A pasta `aula-05` do repositório foi atualizada depois, e hoje já traz as mudanças da aula 06. Para comparar com o que você fez, veja a pasta como ela estava no dia da aula: [`aula-05/biblioteca` em 31/08/2026](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/9c92882/aula-05/biblioteca/src/main/java/br/edu/insper/biblioteca).

As diferenças são sobras que a API não usa: os métodos `imprimirLivros` e `contaLivros`, em `Editora` e em `EditoraService`, e alguns trechos comentados.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| API REST | Um servidor que recebe requisições HTTP e responde em JSON | O projeto inteiro |
| Rota | Um endereço e um verbo que a API sabe atender | `POST /editora`, `GET /livro/{isbn}` |
| Controller | A classe que recebe a requisição | `EditoraController`, `LivroController` |
| Service | A classe que guarda os dados e faz o trabalho | `EditoraService`, `LivroService` |
| Anotação | Um aviso ao Spring sobre o papel de uma classe ou método | `@RestController`, `@GetMapping` |
| Código de status | O número que resume o resultado da requisição | 201, 204, 404 |

Quer praticar? Continue em [Exercícios](exercicios.md).
