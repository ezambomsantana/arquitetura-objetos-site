# Aula 06 — Injeção de dependência

!!! abstract "Nesta aula"
    - **Você parte de:** a API da [Aula 05](../aula-05/material.md).
    - **Você termina com:** cada livro ligado à sua editora.
    - **Ideias novas:** injeção de dependência, `@Service` e `@Autowired`.
    - **Código pronto:** [`aula-06/biblioteca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-06/biblioteca)

## O que vamos construir

Na [Aula 05](../aula-05/material.md), os livros eram cadastrados sem editora. Hoje vamos ligar cada livro a uma editora que já existe na API.

Para isso, o `LivroService` precisa perguntar ao `EditoraService` qual é a editora. Parece simples, mas a primeira tentativa vai falhar de um jeito curioso, e o conserto é a ideia central da aula: a **injeção de dependência**.

## Antes de começar

Parta da API que você terminou na Aula 05. Continue usando `./mvnw spring-boot:run` para executar e o `curl` para testar, parando e executando de novo a cada passo.

## Passo 1 — Ligar o livro à editora, primeira tentativa

Quem cadastra um livro vai mandar junto o `id` da editora. O service procura essa editora e faz a mesma ligação da Aula 03.

Em `LivroService.java`, acrescente um atributo, antes do `HashMap`, e troque o método `criar`:

```java title="LivroService.java"
    private EditoraService editoraService = new EditoraService();
```

```java title="LivroService.java"
    public Livro criar(Livro livro) {

        Editora editora = editoraService.buscar(livro.getEditora().getId());

        if (editora == null) {
            return null;
        }

        livro.setEditora(editora);
        editora.addLivro(livro);
        livros.put(livro.getIsbn(), livro);
        return livro;
    }
```

**Execute.** Cadastre uma editora e copie o `id` da resposta:

```bash
curl -i -X POST http://localhost:8080/editora \
  -H "Content-Type: application/json" \
  -d '{"cnpj":"1","nome":"Editora do Insper","endereco":"Rua Quata","anoFundacao":1999}'
```

Agora cadastre um livro dessa editora, colocando o `id` no lugar indicado:

```bash
curl -i -X POST http://localhost:8080/livro \
  -H "Content-Type: application/json" \
  -d '{"isbn":"123","nome":"Duna","ano":1965,"editora":{"id":"SEU_ID"}}'
```

A resposta é um erro `400`. O JSON só traz o `id` da editora, e o Spring não consegue montar um objeto `Editora` sem um número para `anoFundacao`, que é um `int`.

## Passo 2 — Aceitar o JSON incompleto

A correção é uma linha de configuração. Abra `src/main/resources/application.properties` e acrescente:

```properties title="application.properties"
spring.jackson.deserialization.fail-on-null-for-primitives=false
```

Ela diz ao Spring para usar `0` quando faltar um número.

**Execute**, cadastre a editora de novo e depois o livro, com os mesmos dois comandos. Como o servidor reiniciou, o `id` da editora é outro.

Agora a resposta do livro é `201`, mas vem **vazia**. E abrir <http://localhost:8080/livro> no navegador mostra `[]`: o livro não foi salvo.

!!! question "Atividade 1 — Explicar"
    Abra <http://localhost:8080/editora>: a editora está lá. Mesmo assim, o `LivroService` não a encontrou, e o `criar` devolveu `null`. Por quê? Procure no código quantas vezes aparece `new EditoraService()`.

??? success "Confira"
    Aparece duas vezes: uma no `EditoraController` e outra no `LivroService`. São **dois objetos diferentes**, e cada um tem o próprio `HashMap`. A editora foi guardada no objeto do controller. O `LivroService` procurou no dele, que está vazio.

    É o que você viu na Aula 02: cada objeto guarda os próprios valores.

## Passo 3 — Um service só, entregue pelo Spring

O programa precisa de um único `EditoraService`, usado por todo mundo. Em vez de cada classe criar o seu com `new`, vamos pedir ao Spring que crie um e o entregue a quem precisar.

São duas anotações:

| Anotação | O que diz ao Spring |
| --- | --- |
| `@Service` | "Crie um objeto desta classe e guarde-o." |
| `@Autowired` | "Coloque aqui o objeto que você guardou." |

Marque os dois services. Em `EditoraService.java` e em `LivroService.java`, acrescente o `import` e a anotação acima da classe:

```java title="EditoraService.java"
import org.springframework.stereotype.Service;

@Service
public class EditoraService {
```

```java title="LivroService.java"
import org.springframework.stereotype.Service;

@Service
public class LivroService {
```

Agora troque os três `new` por `@Autowired`. Nos três arquivos, acrescente também `import org.springframework.beans.factory.annotation.Autowired;`.

```java title="EditoraController.java"
    @Autowired
    private EditoraService editoraService;
```

```java title="LivroController.java"
    @Autowired
    private LivroService livroService;
```

```java title="LivroService.java"
    @Autowired
    private EditoraService editoraService;
```

**Execute**, cadastre a editora e o livro mais uma vez. Agora a resposta é `201` com o livro completo:

```json
{"isbn":"123","nome":"DUNA","ano":1965,"autores":[],"disponivel":true,"editora":{"cnpj":"1","nome":"Editora do Insper","endereco":"Rua Quata","anoFundacao":1999,"id":"f6d8b4e4-980a-47a4-b5bd-ebe6e58b1337"}}
```

O livro voltou com a editora inteira dentro dele, embora você só tenha mandado o `id`. O service encontrou a editora verdadeira e fez a ligação.

O que mudou: não há mais nenhum `new EditoraService()` no código. O Spring cria um objeto de cada classe marcada com `@Service` e entrega esse mesmo objeto em todo lugar que tem `@Autowired`. Uma classe receber pronto aquilo de que depende, em vez de criar, chama-se **injeção de dependência**.

!!! question "Atividade 2 — Prever"
    O `EditoraController` e o `LivroService` têm, cada um, um atributo `editoraService`. Agora eles apontam para o mesmo objeto ou para objetos diferentes? Como o teste que você acabou de fazer comprova isso?

??? success "Confira"
    Para o mesmo objeto. A editora foi cadastrada pelo controller e encontrada pelo `LivroService`. Isso só é possível se os dois usam o mesmo `HashMap`.

## O código da aula

O programa que você escreveu é o da aula. Compare com os arquivos da pasta [`aula-06/biblioteca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-06/biblioteca/src/main/java/br/edu/insper/biblioteca) no repositório.

Em `EditoraService`, o repositório tem um trecho comentado com o nome **Singleton**. É outra forma de garantir um único objeto de uma classe, escrita à mão, sem o Spring. Com `@Service`, o Spring faz esse trabalho.

As outras diferenças são sobras que a API não usa: os métodos `imprimirLivros` e `contaLivros`, em `Editora`, e mais um trecho comentado em `EditoraService`.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| Dependência | Um objeto de que outro precisa para trabalhar | `LivroService` depende de `EditoraService` |
| Injeção de dependência | A classe recebe a dependência pronta, em vez de criar | Os atributos com `@Autowired` |
| `@Service` | Pede ao Spring que crie e guarde um objeto da classe | `EditoraService`, `LivroService` |
| `@Autowired` | Pede ao Spring que entregue o objeto guardado | Nos dois controllers e no `LivroService` |
| Singleton | Uma classe com um único objeto no programa inteiro | O `EditoraService` compartilhado |

Quer praticar? Continue em [Exercícios](exercicios.md).
