# Mapeamento preliminar das aulas

Este documento descreve o que foi encontrado em cada pasta do repositório [`arquitetura-objetos-2026-2`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2), como base para escrever os roteiros do site. Ele não é publicado no site.

- **Versão analisada:** branch `main` no commit `5e2f957`, de 12/10/2026.
- **Método:** leitura de todos os arquivos Java, `pom.xml`, `application.properties` e Markdown de cada pasta; comparação entre pastas consecutivas com `diff`; consulta ao histórico Git. Os projetos em Java puro das aulas 02, 03, 04 e 11 foram compilados, e os três primeiros foram executados. Os projetos Spring Boot não foram executados.
- **Como ler:** "Observado" é o que está nos arquivos. "Interpretação" é uma leitura minha e pode estar errada. As dúvidas de cada aula estão reunidas em [`pendencias-para-o-professor.md`](pendencias-para-o-professor.md).

## Visão geral

| Aula | Projeto | Tecnologia | Assunto observado | Parte de |
| --- | --- | --- | --- | --- |
| 02 | `aula` | Java 23, sem dependências | Classe, objeto, construtores, método, `ArrayList`, `HashSet`, `HashMap` | — |
| 03 | `aula` | Java 23 | Encapsulamento, relacionamentos entre classes | Aula 02 |
| 04 | `aula`, `exercicios-maven` | Java 23; Java 11 nos exercícios | Classes de serviço; lista de exercícios de OO com resolução | Aula 03 |
| 05 | `biblioteca` | Spring Boot 4.1.1, Java 25 | Primeira API REST, controllers, services, injeção de dependência | Aula 04 |
| 06 | `biblioteca` | Spring Boot 4.1.1, Java 25 | Injeção de dependência; livro ligado à editora | Aula 05 |
| 07 | só `exercicios.md` | — | Lista de exercícios de APIs REST | Exercícios da aula 04 |
| 08 | `biblioteca` | + springdoc 3.1.0 | Swagger, DTO, exceções e tratamento de erros, pacotes por domínio | Aula 06 |
| 09 | `biblioteca` | + Spring Security | Autenticação Basic, cadastro de usuários, BCrypt | Aula 08 |
| 10 | `equipamento`, `execicio.md` | Spring Boot 4.1.1, Java 25 | Exercício de API completa, com resolução | Aulas 05 a 09 |
| 11 | `heranca` | Java 23, sem dependências | Herança, classe abstrata, interface, polimorfismo | Aulas 02 a 04 |
| 12 | `turismo` | + Spring Data JPA, H2 | Entidades, repositórios, paginação, relação um-para-muitos | Projeto novo |
| 13 | `turismo` | + Security, springdoc, driver PostgreSQL | Relação muitos-para-muitos, usuários no banco, Swagger com autenticação | Aula 12 |
| 14 | `turismo` | igual à aula 13 | Roteiro em README: JWT, validação, tratamento de erros | Aula 13 |

Não existe pasta `aula-01`. O site preserva a numeração do repositório.

## Aula 02

**Arquivos.** `aula-02/aula/pom.xml`, `Livro.java`, `Main.java`, no pacote `br.insper.aula01`. Um único commit, `b4795ef`, de 15/08/2026, com a mensagem "add aula-01".

**Observado.**

- `Livro`: quatro atributos sem modificador de acesso (`nome`, `autor`, `ano`, `disponivel`), um construtor sem parâmetros que define `nome = "Teste"`, um construtor com três parâmetros que também define `disponivel = true`, e o método `emprestar()`, que devolve um texto e altera `disponivel`.
- `Main`: variáveis `String`, `int` e `boolean`; `String.format`; três livros criados pelos dois construtores; acesso direto a atributos (`livro2.nome = "Duna"`); método estático `buscaLivro` com `for` e `equals`; `ArrayList<Livro>`; `HashSet<String>` de autores; `HashMap<String, String>` de usuários percorrido por `keySet()`; `if`/`else`.
- Ao executar, o programa imprime o cabeçalho e para com `NullPointerException` na linha 34, `buscaLivro(null, null)`.
- A linha 79, `livro1.length()`, está comentada. Sem o comentário, ela não compila.

**Interpretação.** Aula de transição de Python para Java e de introdução a classes. A chamada com `null` e a linha comentada parecem demonstrações feitas em aula.

**Situação no site.** Roteiro e exercícios escritos. O roteiro constrói o programa do zero e deixa a chamada com `null` para o fim, como experimento.

## Aula 03

**Arquivos.** `aula-03/aula`: `Livro.java`, `Autor.java`, `Editora.java`, `Main.java`. Commits de 18/08/2026.

**Observado.**

- Os atributos de `Livro` passam a ser `private`, com getters e setters. Alguns têm regra: `setNome` ignora nomes com menos de três caracteres e `getNome` devolve o nome em maiúsculas.
- `Livro` deixa de ter `autor` como texto e ganha `Editora editora` e `ArrayList<Autor> autores`.
- `Editora` guarda uma lista de livros e tem `addLivro`, que ignora `null`. `Autor` guarda uma lista de livros.
- `Livro` ganha `toString()`.
- O `main` liga os objetos nos dois sentidos: `livro1.setEditora(editora)` e `editora.addLivro(livro1)`.
- O programa executa até o fim. A chamada `buscaLivro(null, null)` da aula 02 não está mais presente.

**Relação com a anterior.** Mesmo projeto e mesmo pacote da aula 02, evoluído.

**Interpretação.** Encapsulamento e relacionamentos um-para-muitos (editora e livros) e muitos-para-muitos (livros e autores).

**Situação no site.** Roteiro e exercícios escritos.

## Aula 04

**Arquivos.** `aula-04/aula`, com `EditoraService.java` e `LivroService.java` novos; `aula-04/exercicios.md`; `aula-04/exercicios-maven`, com 13 classes, `README.md` e `build.sh`.

**Observado.**

- `Livro` ganha `isbn` e `Editora` ganha `cnpj`, usados como identificadores.
- `EditoraService` e `LivroService` guardam os objetos em um `HashMap` e oferecem `criar`, `buscar` e `excluir`. O `main` passa a usar os serviços em vez de criar os objetos diretamente.
- `exercicios.md`, criado em 20/08/2026: lista com dois exercícios de modelagem, Campeonato de Futebol e Loja Virtual, sobre relacionamentos 1 para 1, 1 para N e N para N.
- `exercicios-maven`, adicionado em 03/09/2026 no commit "add resolucao": implementação dos dois exercícios, em pacotes `exercicio1` e `exercicio2`. O `pom.xml` desse projeto usa Java 11.

**Relação com a anterior.** O projeto `aula` continua o da aula 03.

**Interpretação.** Separação entre classes de modelo e classes de serviço, preparando o formato usado depois com Spring.

**Situação no site.** Tratada como aula estúdio, por indicação do aluno que preparou a proposta: a lista de exercícios foi reproduzida, com um roteiro de apoio. O projeto `aula-04/aula` não é citado no site.

## Aula 05

**Arquivos.** `aula-05/biblioteca`: `BibliotecaApplication`, `Livro`, `Autor`, `Editora`, `EditoraController`, `EditoraService`, `LivroController`, `LivroService`, todos em um único pacote. Commits de 27/08, 31/08 e 06/09/2026.

**Observado.**

- Projeto Spring Boot 4.1.1 com `spring-boot-starter-webmvc` e Java 25.
- Rotas para `/editora` e `/livro`: `POST`, `GET` por identificador, `DELETE` e `GET` de listagem com filtro opcional `?nome=`.
- Anotações `@RestController`, `@Service`, `@RequestBody`, `@PathVariable`, `@RequestParam` e `@ResponseStatus`.
- `Editora` ganha um `id` gerado com `UUID`.
- No commit de 06/09, `new EditoraService()` no controller foi trocado por `@Autowired`. Em `EditoraService` há um comentário `//Singleton` e uma implementação manual de singleton comentada.

**Relação com a anterior.** Mesmas classes de modelo e os mesmos serviços da aula 04, agora expostos por HTTP.

**Executado.** O projeto foi empacotado e executado. Todas as rotas respondem. `POST /livro` com `"editora":{"id":"..."}` devolve 400, porque o construtor de `Editora` recebe um `int` que não vem no JSON; com a propriedade que está no projeto da aula 06, devolve 201.

**A pasta foi sobrescrita.** Em 31/08 (commit `9c92882`), data da aula, os controllers criavam os services com `new`, os services não tinham anotação e `LivroService.criar` não ligava o livro à editora. O commit de 06/09 trouxe para a pasta o estado da aula 06.

**Situação no site.** Roteiro e exercícios escritos sobre a versão de 31/08. O roteiro parte de um projeto gerado no Spring Initializr e das classes da aula 03.

## Aula 06

**Arquivos.** `aula-06/biblioteca`, mesma estrutura da aula 05. Commits de 01/09/2026.

**Observado.** Em relação à versão atual da aula 05, há duas diferenças: `Autor` não tem o atributo `cpf`, e `application.properties` tem a linha `spring.jackson.deserialization.fail-on-null-for-primitives=false`. Como a pasta da aula 05 foi alterada em 06/09, depois da criação da aula 06, o histórico não permite dizer com segurança o que foi desenvolvido em cada uma. A propriedade do Jackson é o que faz o cadastro de livros funcionar enviando só o `id` da editora, como verificado na aula 05.

**O que o histórico mostra.** Comparando o primeiro commit da aula 06 com a aula 05 como estava em 31/08, a aula 06 trouxe: `@Service` nos dois services, `@Autowired` nos controllers e em `LivroService`, um singleton manual deixado em comentário, a ligação entre livro e editora em `LivroService.criar` e, em um segundo commit, a propriedade do Jackson.

**Situação no site.** Roteiro e exercícios escritos, com esse conteúdo.

## Aula 07

**Arquivos.** Apenas `aula-07/exercicios.md`, de 03/09/2026.

**Observado.** Lista "APIs REST com Spring Boot (Continuação)": transformar os domínios Campeonato de Futebol e Loja Virtual, da lista da aula 04, em APIs REST com camadas `Controller`, `Service` e `Model`. Lista os endpoints esperados e traz orientações de entrega. Não há resolução no repositório.

**Situação no site.** Tratada como aula estúdio, no formato da aula 04: a lista foi reproduzida, com um roteiro de apoio.

## Aula 08

**Arquivos.** `aula-08/biblioteca`, com `README.md` intitulado "Aula 08 - 08/09/2026". Commits de 06/09 e 08/09/2026.

**Observado.**

- O README tem três seções: adicionar Swagger com `springdoc-openapi-starter-webmvc-ui` 3.1.0, DTOs para rotas que não devolvem o modelo, e tratamento de erros com exceções próprias.
- O código foi reorganizado em pacotes por domínio: `editora` (com `controller`, `dto`, `exception`, `model` e `service`), `livro`, `autor` e `utils`.
- Novos: `EditoraLivrosDTO` e a rota `GET /editora/conta-livros`; `EditoraNaoEncontradaException`; `EditoraErrorHandler` com `@ControllerAdvice` e `@ExceptionHandler`; `ErrorResponseDTO`.
- `EditoraService.buscar` passa a lançar exceção quando a editora não existe.
- Os nomes usados nos exemplos do README (`LivroNotFoundException`, `EditoraLivroDTO`, `setCode`) são diferentes dos nomes no código (`EditoraNaoEncontradaException`, `EditoraLivrosDTO`, `setStatusCode`).

**Relação com a anterior.** Continua o projeto `biblioteca` das aulas 05 e 06.

**Executado.** O projeto foi empacotado e executado. A página do Swagger abre em `/swagger-ui.html`; `GET /editora/conta-livros` devolve a lista de DTOs; buscar ou excluir uma editora inexistente responde 404 com o `ErrorResponseDTO`; cadastrar uma editora sem nome responde 500, porque a `RuntimeException` da validação não tem tratador.

**Situação no site.** Roteiro e exercícios escritos, partindo do código da aula 06.

## Aula 09

**Arquivos.** `aula-09/biblioteca`, com `README.md`. Commits de 10/09/2026.

**Observado.**

- Dependência `spring-boot-starter-security`.
- `SecurityConfig`: CSRF desativado, `POST /usuarios` e `GET /usuarios` liberados, demais rotas autenticadas, autenticação HTTP Basic, `BCryptPasswordEncoder`.
- Pacote `usuario`: `CreateUserDTO`, `UsuarioController` com `POST /usuarios`, e `UsuarioService`, que implementa `UserDetailsService` e guarda os usuários em memória.
- O README traz o código de `SecurityConfig` e de `UsuarioService` e termina com três exercícios: rota de recuperação de senha, rota de listagem de usuários e liberar o Swagger no `SecurityConfig`.
- O título do README é "Aula 08 - 08/09/2026", igual ao da aula 08.

**Relação com a anterior.** Projeto da aula 08 com segurança acrescentada.

**Executado.** O projeto foi construído em etapas e executado. Só com a dependência, todas as rotas respondem 401 e o Spring gera um usuário `user` com senha no log. Com o código final, `POST /usuarios` responde 200 e devolve a senha codificada; as demais rotas respondem 401 sem credenciais e 200 com elas; `GET /usuarios` responde 401, embora esteja liberado, porque não há rota e a página de erro é protegida; o Swagger fica bloqueado.

**Situação no site.** Roteiro escrito, partindo do código da aula 08. A página de exercícios traz os três exercícios do README, com orientações complementares.

## Aula 10

**Arquivos.** `aula-10/execicio.md`, de 13/09/2026; `aula-10/equipamento`, de 19/09/2026, com coleção do Postman.

**Observado.**

- Enunciado: API REST de locação de equipamentos com `Cliente`, `Equipamento` e `Locacao`; CRUD completo; exclusão lógica com atributo `deleted`; escolha automática de um equipamento disponível ao criar a locação; campos obrigatórios; todas as rotas autenticadas, exceto a listagem de equipamentos.
- Projeto `equipamento`: pacotes `cliente`, `equipamento`, `locacao`, `usuario` e `common`. Cada domínio tem modelo, controller, service, exceção de "não encontrado" e um tratador de erros. Em `common` há `CampoInvalidoException`, `GlobalErrorHandler`, `ErrorResponseDTO` e `SecurityConfig`.
- A classe que trata `ClienteNaoEncontradoException` se chama `EditoraErrorHandler`.
- As mensagens de commit indicam desenvolvimento em partes: "add execicio parte 1", "aula-10", "finaliza exercicio".

**Executado.** A resolução foi empacotada e executada. Atende aos requisitos testados: só `GET /equipamentos` responde sem autenticação; campos faltando respondem 400 com mensagem; registros excluídos somem da listagem e respondem 404 na busca; a locação escolhe um equipamento disponível e o marca como indisponível. Um equipamento cadastrado sem o campo `disponivel` nasce indisponível.

**Interpretação.** Exercício integrador das aulas 05 a 09. Os commits da resolução são todos de 19/09, em cinco etapas ao longo do dia.

**Situação no site.** Tratada como aula estúdio: o enunciado foi reproduzido, com um roteiro de apoio que aponta para a resolução.

## Aula 11

**Arquivos.** `aula-11/heranca`: `Pessoa`, `Aluno`, `Professor`, `Coordenador`, `Avaliar`, `Sala`, `Main`, no pacote `org.example`. Commit de 29/09/2026.

**Observado.**

- `Pessoa` é `abstract`, com o método abstrato `apresentacao()` e o método concreto `getDocumento()`, que devolve o CPF.
- `Aluno` e `Professor` estendem `Pessoa` e implementam a interface `Avaliar`. `Aluno` sobrescreve `getDocumento()` para devolver o RA. `Coordenador` estende `Professor` e não tem corpo.
- `Sala.reservar(Pessoa p)` chama `p.getDocumento()`, o que funciona para qualquer subclasse.
- O `main` monta uma `List<Avaliar>` e uma `List<Pessoa>` e percorre as duas.
- **O projeto não compila.** Há dois erros em `Main.java`: na linha 18, `a.avaliar()` é chamado sem o argumento `Pessoa` que a interface exige; na linha 34, `sala.reservar(p)` usa a variável `p` fora do laço em que ela foi declarada.

**Relação com as anteriores.** Projeto independente, em Java puro, que retoma orientação a objetos depois do bloco de APIs.

**Situação no site.** Roteiro e exercícios escritos. As seis classes construídas são iguais às do repositório, a não ser por um `import` sem uso em `Aluno` e em `Professor`. O `Main` do roteiro é uma versão que compila.

## Aula 12

**Arquivos.** `aula-12/turismo`: pacotes `destino` e `atracao`. Commit de 05/10/2026.

**Observado.**

- Dependências novas: `spring-boot-starter-data-jpa`, `h2` e `spring-boot-h2console`. Banco H2 em arquivo, em `./data/turismo`, com console em `/h2-console` e `ddl-auto=update`.
- `Destino` e `Atracao` são entidades (`@Entity`, `@Id`, `@GeneratedValue`, `@Column`), ligadas por `@OneToMany(mappedBy = "destino")` e `@ManyToOne` com `@JoinColumn`.
- `DestinoRepository` estende `JpaRepository` e declara `findByPais(String, Pageable)`.
- `GET /destinos` aceita filtro por país e paginação com `Pageable`, devolvendo `Page<Destino>`.
- `Atracao` tem entidade e repositório, sem service nem controller.
- Os arquivos do banco H2 estão versionados em `data/`.

**Relação com as anteriores.** Projeto novo. A estrutura em camadas é a das aulas 05 a 10, e o `HashMap` em memória dá lugar ao banco de dados.

**Executado.** O projeto foi construído em etapas a partir do Spring Initializr, cujo `pom.xml` gerado hoje é igual ao do repositório. Os dados persistem entre execuções; o filtro, a paginação e a ordenação funcionam; a tabela `atracao` é criada com a chave estrangeira `id_destino`. O banco H2 é criado com nome de usuário vazio, e o console recusa o `sa` que vem preenchido.

**Situação no site.** Roteiro e exercícios escritos.

## Aula 13

**Arquivos.** `aula-13/turismo`: acrescenta os pacotes `visitante`, `usuario` e `common`, além de `AtracaoController`, `AtracaoService` e uma coleção do Postman. Commits de 05/10 a 08/10/2026.

**Observado.**

- `Visitante` e `Atracao` ligados por `@ManyToMany`, com `@JoinTable` do lado de `Atracao`. A rota `POST /visitantes/{idVisitante}/atracoes/{idAtracao}` cria a associação.
- `@JsonIgnore` nas listas de `Destino` e `Atracao`.
- Segurança como na aula 09, mas com os usuários no banco: entidade `User` na tabela `tb_user`, `UsuarioRepository.findByEmail`, senha com BCrypt. No commit `a935eea`, `CreateUserDTO` foi renomeado para `User`.
- `OpenApiConfig` declara autenticação Basic para o Swagger, e o `SecurityConfig` libera as rotas do Swagger e do console H2.
- No commit "add-conexao-postgres", de 08/10, o `pom.xml` ganha o driver do PostgreSQL e o `application.properties` ganha três linhas comentadas de conexão, com marcadores no lugar dos valores.

**Relação com a anterior.** Continua o projeto `turismo`. O primeiro commit, de 05/10, é a aula 12 com as rotas de atração, a segurança e o Swagger; os de 06/10 trazem `Visitante` e os `@JsonIgnore`; os de 08/10, os usuários no banco e o PostgreSQL.

**Executado.** O projeto foi construído em etapas a partir do código da aula 12. Sem `@JsonIgnore`, cadastrar uma atração já devolve a resposta em laço; com ele, as respostas ficam normais. A tabela `atracao_visitante` é criada, e a rota de associação a preenche. Com a segurança, as rotas respondem 401 sem credenciais, o Swagger abre sem autenticação e os usuários continuam válidos depois de reiniciar. O console do H2 responde com `X-Frame-Options: DENY`.

**Situação no site.** Roteiro e exercícios escritos. O driver e a configuração do PostgreSQL são descritos, mas não construídos nem testados.

## Aula 14

**Arquivos.** `aula-14/turismo`, com `README.md` de cerca de 360 linhas. Commits de 12/10/2026.

**Observado.**

- O README anuncia três mudanças: autenticação com JWT, uso de DTOs para requisição e resposta, e melhoria da validação e do tratamento de erros. As seções escritas cobrem JWT, em oito passos com a biblioteca JJWT 0.12.3, e validação com `spring-boot-starter-validation`, `@Valid` e um `GlobalExceptionHandler`. Não há seção sobre DTOs além dos de login.
- O código Java é o da aula 13 com uma diferença: o `SecurityConfig` ganha a configuração `frameOptions.sameOrigin()`. As classes descritas no README, como `JwtService` e `JwtAuthenticationFilter`, não estão no projeto, e o `pom.xml` não tem as dependências de JWT nem de validação.
- O `application.properties` tem, em uma linha comentada, o endereço de um servidor de banco de dados externo. Usuário e senha continuam como marcadores.

**Interpretação.** O código parece ser o ponto de partida da aula, e o README, o roteiro a ser seguido durante ela.

**Executado.** Segui o README sobre o código da aula 13 e o resultado compila e funciona: o login devolve um token JWT, as rotas protegidas aceitam o token e recusam HTTP Basic, e a validação de `Visitante` devolve as mensagens. Três comportamentos observados: sem token, a API responde 403, e não 401; o login com senha errada também responde 403; o `GlobalExceptionHandler` do README responde com HTTP 200 e `"status":400` no corpo, porque não tem `@ResponseStatus`.

**Situação no site.** Roteiro e exercícios escritos. O roteiro segue o README, define os pacotes e os `import`, cria o `ErrorResponseDTO` que o README não mostra e acrescenta `@ResponseStatus` ao tratador.

## Sequências de projeto

Para os roteiros futuros, vale tratar as aulas em três sequências, porque cada pasta é uma fotografia de um projeto que evolui:

1. **Biblioteca em Java puro:** aulas 02, 03 e 04.
2. **Biblioteca como API:** aulas 05, 06, 08 e 09, com os exercícios das aulas 07 e 10.
3. **Turismo com banco de dados:** aulas 12, 13 e 14.

A aula 11 fica fora das sequências.
