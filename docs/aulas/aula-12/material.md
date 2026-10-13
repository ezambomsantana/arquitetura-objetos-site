# Aula 12 — Banco de dados com Spring Data JPA

!!! abstract "Nesta aula"
    - **Você parte de:** do zero. É um projeto novo, a API de turismo.
    - **Você termina com:** uma API que guarda destinos em um banco de dados.
    - **Ideias novas:** entidade, repository, paginação e chave estrangeira.
    - **Código pronto:** [`aula-12/turismo`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-12/turismo)

## O que vamos construir

Na API da biblioteca, tudo ficava em `HashMap` e se perdia quando o servidor parava. Nesta aula começa um projeto novo, uma API de **turismo**, em que os dados vão para um **banco de dados** e continuam lá entre uma execução e outra.

Você vai escrever muito pouco código de banco. Quem faz a ponte entre os objetos Java e as tabelas é o **Spring Data JPA**: você descreve a classe, e ele cria a tabela e as consultas.

## Antes de começar

O projeto é novo, criado do zero, com o mesmo JDK 25 das aulas anteriores. Os testes usam o `curl` e o navegador.

## Passo 1 — Criar o projeto com um banco de dados

Abra [start.spring.io](https://start.spring.io) e preencha:

| Campo | Valor |
| --- | --- |
| Project | Maven |
| Language | Java |
| Group | `br.insper` |
| Artifact | `turismo` |
| Java | 25 |
| Dependencies | Spring Web, Spring Data JPA, H2 Database |

O **H2** é um banco de dados pequeno, que roda dentro do próprio programa e guarda tudo em um arquivo. Não é preciso instalar nada.

Gere, descompacte e abra `src/main/resources/application.properties`. Deixe o arquivo assim:

```properties title="application.properties"
spring.application.name=turismo

spring.datasource.url=jdbc:h2:file:./data/turismo

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true


spring.jpa.hibernate.ddl-auto=update
```

| Linha | O que faz |
| --- | --- |
| `spring.datasource.url` | Diz onde fica o banco: no arquivo `data/turismo`, dentro da pasta do projeto. |
| `spring.h2.console...` | Liga uma página para olhar o banco pelo navegador. |
| `spring.jpa.show-sql` e `format_sql` | Mostram no terminal os comandos SQL que o programa executa. |
| `spring.jpa.hibernate.ddl-auto=update` | Cria e atualiza as tabelas sozinho, a partir das classes. |

**Execute** com `./mvnw spring-boot:run` e abra <http://localhost:8080/h2-console>. Na tela de login:

- em **JDBC URL**, escreva `jdbc:h2:file:./data/turismo`;
- **apague o `sa`** do campo **User Name** e deixe a senha em branco.

Clique em **Connect**. Com o `sa` que vem preenchido, o login é recusado: este banco foi criado sem nome de usuário.

Você verá o banco vazio, sem tabelas. Deixe essa aba aberta.

## Passo 2 — A primeira entidade

Um banco de dados guarda as informações em **tabelas**, com linhas e colunas, como uma planilha. A linguagem para conversar com ele é o **SQL**. Você não vai escrever SQL nesta aula: o Spring escreve por você e mostra no terminal o que escreveu.

Crie a pasta `destino`, dentro de `src/main/java/br/insper/turismo`, com quatro arquivos.

A classe de dados:

```java title="Destino.java"
package br.insper.turismo.destino;

import jakarta.persistence.*;

@Entity
public class Destino {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    @Column(nullable = false)
    private String nome;
    @Column(nullable = false)
    private String pais;

}
```

Escreva um getter e um setter para os três atributos.

É uma classe comum, com quatro anotações que a ligam ao banco:

| Anotação | O que diz |
| --- | --- |
| `@Entity` | "Esta classe é uma tabela." Cada objeto vira uma linha, e cada atributo, uma coluna. |
| `@Id` | "Este atributo é a chave primária": o valor que identifica cada linha, sem repetição. |
| `@GeneratedValue` | "O banco gera o valor sozinho: 1, 2, 3..." Substitui o `UUID` da biblioteca. |
| `@Column(nullable = false)` | "Esta coluna não pode ficar vazia." |

O acesso ao banco:

```java title="DestinoRepository.java"
package br.insper.turismo.destino;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface DestinoRepository extends JpaRepository<Destino, Integer> {

}
```

Isso é o **repository**: a parte da aplicação que fala com o banco. Repare que é uma interface vazia. Você não escreve o código que salva e busca: ao herdar de `JpaRepository`, a interface já ganha métodos como `save` e `findAll`, e o Spring fornece a implementação. Os dois tipos entre `<` e `>` são a classe guardada e o tipo da chave.

O service, que agora usa o repository no lugar do `HashMap`:

```java title="DestinoService.java"
package br.insper.turismo.destino;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class DestinoService {

    @Autowired
    private DestinoRepository destinoRepository;

    public Destino salvar(Destino destino) {
        return destinoRepository.save(destino);
    }

    public List<Destino> list() {
        return destinoRepository.findAll();
    }


}
```

E o controller:

```java title="DestinoController.java"
package br.insper.turismo.destino;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
public class DestinoController {

    @Autowired
    private DestinoService destinoService;

    @GetMapping("/destinos")
    public List<Destino> list() {
        return destinoService.list();
    }

    @PostMapping("/destinos")
    public Destino save(@RequestBody Destino destino) {
        return destinoService.salvar(destino);
    }
}
```

A aplicação tem agora três camadas: o controller recebe a requisição, o service decide o que fazer e o repository fala com o banco.

**Execute** e olhe o terminal: aparece um `create table destino`. O Spring leu a classe e criou a tabela. Cadastre dois destinos:

```bash
curl -X POST http://localhost:8080/destinos \
  -H "Content-Type: application/json" \
  -d '{"nome":"Paris","pais":"França"}'

curl -X POST http://localhost:8080/destinos \
  -H "Content-Type: application/json" \
  -d '{"nome":"Rio de Janeiro","pais":"Brasil"}'
```

Cada resposta traz o destino com o `id` que o banco gerou, `1` e `2`. No terminal, aparece o `insert` de cada um. No console do H2, clique na tabela `DESTINO` e em **Run**: as duas linhas estão lá.

!!! question "Atividade 1 — Prever"
    Pare o servidor, execute de novo e abra <http://localhost:8080/destinos>. Na Aula 05, o mesmo teste mostrava uma lista vazia. E agora?

??? success "Confira"
    Os dois destinos continuam lá. Eles estão no arquivo da pasta `data`, e não na memória do programa. O `ddl-auto=update` também não recria a tabela: no terminal, não aparece outro `create table`.

!!! question "Atividade 2 — Experimentar"
    Cadastre um destino sem o país, mandando só `{"nome":"Sem país"}`. O que a API responde?

??? success "Confira"
    Um erro `500`. Quem recusou foi o banco, por causa do `nullable = false`. A regra existe e protege os dados, mas a resposta ainda é genérica. Transformá-la em um `400` com mensagem se faz como na Aula 08.

## Passo 3 — Filtro e paginação

Com um banco, a lista pode ficar grande. Vamos filtrar por país e devolver os resultados aos poucos.

Em `DestinoRepository.java`, acrescente os dois `import` e um método:

```java title="DestinoRepository.java"
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
```

```java title="DestinoRepository.java"
    Page<Destino> findByPais(String pais, Pageable pageable);
```

O método não tem corpo, e não precisa. O Spring lê o **nome** dele: `findByPais` quer dizer "busque onde o país for igual ao valor recebido", e a consulta é montada a partir disso.

`Pageable` é o pedido de página, com o número e o tamanho. `Page` é a resposta: os itens daquela página, mais os totais.

Em `DestinoService.java`, acrescente os mesmos dois `import` e troque o método `list`:

```java title="DestinoService.java"
    public Page<Destino> list(String pais, Pageable pageable) {
        if (pais != null) {
            return destinoRepository.findByPais(pais, pageable);
        }
        return destinoRepository.findAll(pageable);
    }
```

Em `DestinoController.java`, acrescente os dois `import` e troque a rota de listagem:

```java title="DestinoController.java"
    @GetMapping("/destinos")
    public Page<Destino> list(@RequestParam(required = false) String pais,
                              Pageable pageable) {
        return destinoService.list(pais, pageable);
    }
```

O parâmetro `Pageable` não tem anotação. O Spring o preenche com o que vier no endereço em `page`, `size` e `sort`.

**Execute**, cadastre mais dois destinos do Brasil, `Salvador` e `Manaus`, e abra <http://localhost:8080/destinos?size=2>. A resposta mudou de formato:

```json
{"content":[{"id":1,"nome":"Paris","pais":"França"},{"id":2,"nome":"Rio de Janeiro","pais":"Brasil"}],
 "first":true,"last":false,"number":0,"size":2,"totalElements":4,"totalPages":2, ...}
```

Os destinos estão em `content`, e em volta vêm as informações da página: são 4 destinos no total, em 2 páginas, e esta é a página 0.

Experimente também:

| Endereço | O que devolve |
| --- | --- |
| `/destinos?size=2&page=1` | A segunda página: Salvador e Manaus. A contagem começa em 0. |
| `/destinos?pais=Brasil` | Só os três destinos do Brasil. |
| `/destinos?sort=nome` | Todos, em ordem alfabética. |

O terminal mostra um aviso sobre `PageImpl` a cada listagem. Ele não impede nada e pode ser ignorado nesta aula.

## Passo 4 — Uma segunda tabela, ligada à primeira

Um destino tem atrações: Paris tem a Torre Eiffel, o Rio tem o Cristo. É o mesmo relacionamento **um para muitos** de editora e livros, da Aula 03. Agora ele também precisa existir no banco.

Crie a pasta `atracao`, com dois arquivos:

```java title="Atracao.java"
package br.insper.turismo.atracao;

import br.insper.turismo.destino.Destino;
import jakarta.persistence.*;

@Entity
public class Atracao {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    @Column(nullable = false)
    private String nome;
    private String descricao;

    @ManyToOne
    @JoinColumn(name = "id_destino")
    private Destino destino;

}
```

Escreva os getters e setters dos quatro atributos.

```java title="AtracaoRepository.java"
package br.insper.turismo.atracao;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface AtracaoRepository extends JpaRepository<Atracao, Integer> {
}
```

Do outro lado, em `Destino.java`, acrescente `import br.insper.turismo.atracao.Atracao;` e `import java.util.List;`, o atributo abaixo e o getter e o setter dele:

```java title="Destino.java"
    @OneToMany(mappedBy = "destino")
    private List<Atracao> atracoes;
```

No Java, a ligação continua igual à da Aula 03: a atração aponta para o seu destino, e o destino tem a lista das suas atrações. As anotações explicam ao banco como guardar isso:

| Anotação | O que diz |
| --- | --- |
| `@ManyToOne` | "Muitas atrações para um destino." |
| `@JoinColumn(name = "id_destino")` | "Guarde a ligação em uma coluna chamada `id_destino`." |
| `@OneToMany(mappedBy = "destino")` | "Um destino para muitas atrações. A ligação já está guardada do outro lado, no atributo `destino`." |

!!! question "Atividade 3 — Prever"
    A classe `Atracao` tem um atributo do tipo `Destino`. Na tabela, a coluna correspondente guarda o quê? Execute e confira no console do H2, clicando na tabela `ATRACAO`.

??? success "Confira"
    A tabela `ATRACAO` tem as colunas `ID`, `NOME`, `DESCRICAO` e `ID_DESTINO`. Um banco não guarda um objeto dentro de outro: guarda o `id` do destino. Essa coluna é uma **chave estrangeira**, e no terminal aparece o comando que a criou, com `foreign key (id_destino) references destino`.

A API ainda não tem rotas para cadastrar atrações. Elas chegam na próxima aula.

## O código da aula

O programa que você escreveu é o da aula. Compare com os arquivos da pasta [`aula-12/turismo`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-12/turismo/src/main/java/br/insper/turismo) no repositório. A única diferença são três `import` que o repositório tem e não usa.

A pasta do repositório traz também uma pasta `data`, com o banco do professor. O seu banco é o que está na sua pasta `data`, com os destinos que você cadastrou.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| Persistência | Guardar os dados fora da memória do programa | O arquivo em `data/` |
| Entidade | Uma classe que corresponde a uma tabela | `Destino`, `Atracao` |
| Repository | A camada que fala com o banco | `DestinoRepository` |
| Método derivado | Consulta criada a partir do nome do método | `findByPais` |
| Paginação | Devolver os resultados em partes | `Pageable` e `Page` |
| Chave estrangeira | Coluna que guarda o `id` de uma linha de outra tabela | `id_destino` |

Quer praticar? Continue em [Exercícios](exercicios.md).
