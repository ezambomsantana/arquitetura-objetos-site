# Referência rápida de Java

Sintaxe, exemplos e comandos utilizados nas aulas de Arquitetura de Objetos. O número entre parênteses indica a aula em que cada assunto é apresentado.

!!! note "Material complementar"
    Esta referência foi elaborada como apoio aos estudos. Não faz parte do material original da disciplina.

## 1. Fundamentos de Java

### Programa mínimo

Todo programa Java começa com uma classe. A execução é iniciada pelo método `main`.

```java
package br.insper.aula01;

public class Main {

    public static void main(String[] args) {
        System.out.println("Olá");
    }
}
```

O arquivo deve se chamar `Main.java`. A estrutura de pastas acompanha o pacote: `br/insper/aula01`.

### Variáveis e tipos

| Em Python | Em Java |
|---|---|
| `nome = "Ana"` | `String nome = "Ana";` |
| `ano = 2026` | `int ano = 2026;` |
| `aberta = True` | `boolean aberta = true;` |
| `None` | `null` |

Em Java, o tipo é declarado na variável. Uma variável local não pode receber valores de tipos incompatíveis sem conversão. Variáveis locais também precisam ser inicializadas antes de serem utilizadas.

Valores padrão de atributos: `null` para referências a objetos, `0` para tipos numéricos inteiros e `false` para `boolean`.

### Saída e texto

| Em Python | Em Java |
|---|---|
| `print(x)` | `System.out.println(x);` |
| `print()` | `System.out.println();` |
| `f"Ano {ano}"` | `String.format("Ano %d", ano)` |
| `a == b`, para textos | `a.equals(b)` |

Textos usam aspas duplas. Aspas simples representam um literal do tipo `char`, como `'A'`.

Use `==` para comparar valores de tipos primitivos. Para comparar o conteúdo de strings, use `equals()`.

### Condicionais e laços

```java
if (aberta) {
    System.out.println("Aberta");
} else if (emReforma) {
    System.out.println("Em reforma");
} else {
    System.out.println("Fechada");
}

for (Livro livro : lista) {
    System.out.println(livro.nome);
}
```

| Em Python | Em Java |
|---|---|
| `and`, `or`, `not` | `&&`, `||`, `!` |
| `elif` | `else if` |
| `for x in lista:` | `for (Tipo x : lista) { }` |

## 2. Classes e objetos (aula 02)

```java
public class Livro {

    String nome;
    boolean disponivel;

    public Livro(String nome) {
        this.nome = nome;
        this.disponivel = true;
    }

    public String emprestar() {
        this.disponivel = false;
        return "Livro emprestado com sucesso";
    }
}
```

| Em Python | Em Java |
|---|---|
| `def __init__(self, nome):` | `public Livro(String nome) { }` |
| `self.nome` | `this.nome` |
| `livro = Livro("Duna")` | `Livro livro = new Livro("Duna");` |
| `def emprestar(self):` | `public String emprestar() { }` |
| `livro.emprestar()` | `livro.emprestar();` |

- **Classe:** define a estrutura e os comportamentos dos objetos.
- **Objeto:** instância de uma classe.
- **Atributo:** dado armazenado no objeto.
- **Método:** operação definida na classe.
- **Construtor:** inicializa um objeto durante sua criação com `new`.

## 3. Coleções

As classes de coleções precisam ser importadas. Por exemplo:

```java
import java.util.ArrayList;
```

### Lista, conjunto e mapa

| Estrutura | Em Python | Em Java |
|---|---|---|
| Lista vazia | `lista = []` | `ArrayList<Livro> lista = new ArrayList<>();` |
| Adicionar item | `lista.append(x)` | `lista.add(x);` |
| Acessar posição | `lista[0]` | `lista.get(0);` |
| Obter tamanho | `len(lista)` | `lista.size();` |
| Conjunto vazio | `s = set()` | `HashSet<String> s = new HashSet<>();` |
| Adicionar ao conjunto | `s.add(x)` | `s.add(x);` |
| Dicionário vazio | `d = {}` | `HashMap<String, String> d = new HashMap<>();` |
| Adicionar ou atualizar | `d["1"] = "Ana"` | `d.put("1", "Ana");` |
| Consultar chave | `d.get("1")` | `d.get("1");` |
| Obter chaves | `d.keys()` | `d.keySet();` |

Imports correspondentes:

```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.HashMap;
```

Em Java, `ArrayList`, `HashSet` e `HashMap` são implementações comuns de listas, conjuntos e mapas. Um conjunto não mantém elementos duplicados; um mapa associa chaves a valores.

## 4. Encapsulamento (aula 03)

```java
public class Livro {

    private String nome;

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }
}
```

| Palavra | Significado |
|---|---|
| `private` | Restringe o acesso direto ao membro à própria classe. |
| `public` | Permite o acesso conforme as regras de visibilidade do Java. |
| `void` | Indica que o método não retorna um valor. |
| `this` | Referencia o objeto atual. |
| `return` | Devolve um resultado ou encerra a execução do método. |

Getters e setters são métodos usados para consultar e alterar atributos. Um setter pode validar o valor antes de armazená-lo.

## 5. Herança e interfaces (aula 11)

| Sintaxe | Significado |
|---|---|
| `class Aluno extends Pessoa` | `Aluno` herda de `Pessoa`. |
| `abstract class Pessoa` | Define uma classe que não pode ser instanciada diretamente. |
| `abstract void apresentacao();` | Declara um método abstrato, sem implementação. |
| `interface Avaliar` | Define um contrato que classes podem implementar. |
| `class Aluno implements Avaliar` | Indica que `Aluno` implementa a interface `Avaliar`. |
| `@Override` | Indica que um método sobrescreve um método herdado ou implementa um método da interface. |

Em uma classe concreta, os métodos abstratos herdados precisam ser implementados. Uma interface também pode conter métodos `default`, `static` e, conforme a versão do Java, outros recursos além de métodos abstratos.

## 6. Executar os programas

### Java puro

Nos projetos que utilizam a estrutura convencional do Maven, compile os arquivos Java:

```bash
javac -d target/classes src/main/java/PACOTE/*.java
```

Depois, execute a classe principal:

```bash
java -cp target/classes PACOTE.Main
```

Substitua `PACOTE` pelo pacote real do projeto. O comando de compilação acima pressupõe que os arquivos necessários estejam no diretório indicado e que as dependências externas não exijam uma configuração adicional de classpath.

### Spring Boot

Na raiz do projeto, execute:

```bash
./mvnw spring-boot:run
```

No Windows, use:

```bat
mvnw.cmd spring-boot:run
```

Esses comandos pressupõem que o projeto contenha o Maven Wrapper.

### Endereços locais úteis

Com a aplicação em execução, os endereços abaixo podem estar disponíveis, dependendo da configuração do projeto.

| Endereço | Finalidade |
|---|---|
| `http://localhost:8080/swagger-ui.html` | Interface da documentação da API (aula 08). |
| `http://localhost:8080/h2-console` | Console do banco H2 (aula 12). |

A rota da documentação pode variar conforme a versão e a configuração do Springdoc. O console H2 precisa estar habilitado na aplicação.

## 7. Requisições HTTP (aula 05)

### Métodos

| Método | Uso comum |
|---|---|
| `GET` | Consultar ou listar recursos. |
| `POST` | Criar um recurso ou executar uma operação. |
| `PUT` | Substituir a representação de um recurso. |
| `PATCH` | Alterar parcialmente um recurso. |
| `DELETE` | Excluir um recurso. |

### Códigos de status

| Código | Significado |
|---|---|
| `200` | Requisição concluída com sucesso. |
| `201` | Recurso criado. |
| `204` | Sucesso sem conteúdo na resposta. |
| `400` | Requisição inválida. |
| `401` | Autenticação necessária ou inválida. |
| `403` | Acesso proibido. |
| `404` | Recurso não encontrado. |
| `405` | Método HTTP não permitido para o recurso. |
| `500` | Erro interno do servidor. |

## 8. Testar a API com `curl`

Os exemplos abaixo pressupõem uma API local disponível em `localhost:8080`.

### Consultar recursos

```bash
curl -i http://localhost:8080/editora
```

### Criar um recurso com JSON

```bash
curl -i -X POST http://localhost:8080/editora \
  -H "Content-Type: application/json" \
  -d '{"cnpj":"1","nome":"Editora do Insper","endereco":"Rua Quata","anoFundacao":1999}'
```

### Excluir um recurso

```bash
curl -i -X DELETE http://localhost:8080/editora/ID
```

Substitua `ID` pelo identificador do recurso.

### Autenticar com usuário e senha (aula 09)

```bash
curl -i -u ana@insper.edu.br:123 http://localhost:8080/editora
```

Use credenciais válidas para a aplicação. O exemplo demonstra o envio de credenciais HTTP Basic; para tráfego real, utilize HTTPS.

### Enviar um token (aula 14)

```bash
curl -i -H "Authorization: Bearer TOKEN" http://localhost:8080/destinos
```

Substitua `TOKEN` por um token válido emitido pelo sistema.

## 9. Anotações do Spring

As anotações abaixo identificam componentes, conectam requisições a métodos e configuram recursos da aplicação.

### Rotas e requisições (aula 05)

| Anotação | Papel |
|---|---|
| `@RestController` | Define um componente que recebe requisições e retorna dados, geralmente em JSON. |
| `@GetMapping`, `@PostMapping`, `@PutMapping`, `@PatchMapping`, `@DeleteMapping` | Associam métodos a verbos HTTP e caminhos. |
| `@RequestBody` | Converte o corpo da requisição, normalmente JSON, em um objeto Java. |
| `@PathVariable` | Obtém um valor presente no caminho da URL, como `{id}`. |
| `@RequestParam` | Obtém um parâmetro da URL, geralmente após `?`. |
| `@ResponseStatus` | Define o código de status HTTP de uma resposta. |

### Dependências e configuração (aulas 06 e 09)

| Anotação | Papel |
|---|---|
| `@Service` | Marca uma classe como componente da camada de serviço. |
| `@Autowired` | Solicita ao Spring a injeção de uma dependência. |
| `@Configuration` | Marca uma classe que declara configurações do Spring. |
| `@Bean` | Registra no contêiner do Spring o objeto retornado pelo método anotado. |

### Tratamento de erros e validação (aulas 08 e 14)

| Anotação | Papel |
|---|---|
| `@ControllerAdvice` | Permite aplicar tratamento compartilhado a controllers. |
| `@RestControllerAdvice` | Combina o tratamento compartilhado com respostas normalmente serializadas no corpo HTTP. |
| `@ExceptionHandler` | Associa um método ao tratamento de determinados tipos de exceção. |
| `@Valid` | Aciona a validação do objeto recebido, conforme as restrições definidas. |
| `@NotNull` | Exige que o valor não seja nulo. |
| `@NotEmpty` | Exige que o valor não seja nulo nem vazio; em textos, listas e coleções, o efeito depende do tipo validado. |

### Banco de dados e relacionamentos (aulas 12 e 13)

| Anotação | Papel |
|---|---|
| `@Entity` | Marca uma classe como entidade persistente. |
| `@Id` | Identifica a chave primária da entidade. |
| `@GeneratedValue` | Configura a geração do identificador. |
| `@Column` | Configura propriedades de uma coluna, como `nullable`. |
| `@Repository` | Identifica um componente de acesso a dados. |
| `@ManyToOne` | Representa um relacionamento muitos para um. |
| `@OneToMany` | Representa um relacionamento um para muitos. |
| `@ManyToMany` | Representa um relacionamento muitos para muitos. |
| `@JoinColumn` | Configura a coluna usada para estabelecer uma associação. |
| `@JoinTable` | Configura a tabela usada para estabelecer uma associação. |
| `@JsonIgnore` | Configura a exclusão de uma propriedade de determinadas operações JSON com Jackson. |

