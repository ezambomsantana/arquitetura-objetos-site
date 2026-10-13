# Pendências para revisão do professor

Este documento reúne o que depende de uma decisão ou confirmação sua antes de o site ser publicado ou ampliado. Ele não é publicado no site. A descrição de cada pasta do repositório está em [`mapeamento-das-aulas.md`](mapeamento-das-aulas.md).

Nada foi alterado no repositório da disciplina. Os possíveis problemas listados na seção 3 estão apenas registrados aqui.

## 1. Decisões sobre a proposta

1. **Publicação.** O site está pronto para o GitHub Pages, mas não foi publicado. As opções são colocá-lo em uma pasta do repositório da disciplina ou em um repositório separado. O `README.md` descreve as duas.
2. **Títulos das aulas.** Os títulos das aulas são descrições minhas, feitas a partir do código. Por favor, corrija os que não corresponderem ao tema da aula.
3. **Numeração.** O site segue as pastas do repositório, que começam em `aula-02`. Houve uma aula 01? Se houve, ela deve aparecer no site, mesmo sem código?
4. **Informações da disciplina.** A página inicial não traz calendário, critérios de avaliação, equipe nem regras, porque nada disso está no repositório. Diga se quer incluir alguma dessas informações e qual é o texto.
5. **Uso de IA.** Este material foi preparado com apoio de uma ferramenta de IA e revisado contra o código real. Se a disciplina tiver uma regra sobre isso, ou se você quiser que o site traga um aviso, é preciso acrescentar.
6. **Ferramenta.** O site de Técnicas de Programação usa MkDocs com o tema `active-handout`, mantido no Insper. Usei MkDocs com o tema Material e um arquivo de estilo que aproxima o visual. O motivo está no `README.md`. O conteúdo em Markdown é compatível com os dois, caso você prefira o tema da referência.

## 1b. Revisão geral do site

Depois de escritas todas as aulas, o site passou por uma revisão de conjunto. O que mudou e depende da sua aprovação:

1. **Seu nome.** A página inicial e o rodapé de todas as páginas trazem "Eduardo Felipe Zambom Santana". O rodapé diz que o material é uma proposta ainda não validada por você. Confirme a grafia e se o nome pode aparecer antes da sua revisão.
2. **Quatro partes.** A página inicial agrupa as aulas em "Objetos em Java" (02 a 04), "APIs com Spring Boot" (05 a 10), "Mais orientação a objetos" (11) e "Banco de dados" (12 a 14). O agrupamento e os nomes são meus.
3. **Menu.** A página "Sequência de aulas" foi retirada; a lista fica só na página inicial. No menu, as aulas aparecem direto, sem o grupo "Aulas".
4. **Quadro "Nesta aula".** Toda aula abre com o mesmo quadro: de onde o aluno parte, com o que termina, as ideias novas e o código pronto. Ele substituiu o aviso "proposta em revisão" que havia em cada página; o aviso continua na página inicial e no rodapé.
5. **Conceitos para quem nunca programou em Java.** Acrescentei explicações curtas onde um termo era usado sem apresentação: `package`, ponto e vírgula e chaves, `import`, `public` e `void`, o que é compilar, requisição HTTP, JSON, código de status, Maven, as opções do `curl`, tabelas e SQL, chave primária. Diga se alguma já é pré-requisito da disciplina e pode sair.
6. **Instalação.** A aula 02 passou a recomendar o JDK 25 para o curso todo, com link para `adoptium.net`, e a citar o IntelliJ como opção. Qual é a orientação oficial de instalação?
7. **Páginas de consulta.** A "Referência rápida" agora cobre o curso inteiro (sintaxe, verbos e códigos HTTP, `curl`, anotações do Spring), e há um "Glossário" com os 58 termos das tabelas de revisão, cada um ligado à sua aula. As duas são complementos meus.

## 2. Aula 02

Tudo o que está nas duas páginas da aula 02 é proposta. Os pontos abaixo são os que mais dependem da sua leitura.

1. **Formato e foco.** O roteiro constrói o programa da aula do zero, em seis passos, e termina com o código do repositório. O foco das explicações é classes e objetos; as coleções e o método `buscaLivro` entram nos dois últimos passos, com pouca explicação. A ordem de construção é uma escolha minha: primeiro o `main`, depois a classe só com atributos e o `livro2`, depois os construtores, o método e as coleções. Confirme se o formato e a ordem correspondem ao que foi feito em aula.
2. **A chamada `buscaLivro(null, null)`.** Na linha 34 de `Main.java`, ela interrompe o programa com `NullPointerException`, e nada depois dela é executado. O roteiro deixa essa linha para o final, como um experimento: o aluno a acrescenta, vê o erro e a comenta. Foi uma demonstração feita em aula? Prefere que o roteiro trate a linha de outra forma?
3. **`livro2` nasce indisponível.** O construtor sem parâmetros não define `disponivel`, que fica `false`. O roteiro mostra isso na Atividade 1, e o Exercício 1 volta ao assunto, sem afirmar que é intencional. É?
4. **A linha 79 comentada**, `livro1.length()`. O roteiro só a cita como diferença em relação ao código construído. Ela teve algum uso em aula que valha registrar?
5. **Nome do pacote e do artefato.** A pasta é `aula-02`, mas o pacote é `br.insper.aula01`, o `artifactId` é `aula-01` e o commit diz "add aula-01". O roteiro usa os nomes como estão. Há algo a explicar aos alunos?
6. **Versão do Java.** O `pom.xml` pede Java 23, e as aulas com Spring pedem Java 25. O roteiro manda instalar o JDK 25 logo na aula 02, para servir ao curso todo. Qual versão a disciplina recomenda instalar?
7. **Forma de executar.** O roteiro ensina o terminal com `javac` e cita a IDE em uma frase. O aluno cria as pastas à mão, sem `pom.xml`. Os comandos de terminal foram testados; a execução pela IDE não. Se as aulas usam IntelliJ, vale detalhar esse caminho?
8. **Comparações com Python.** O roteiro e a página "Referência rápida" supõem que os alunos vêm de Python. A aula 08 do repositório faz a mesma comparação, mas confirme se vale para a turma.
9. **Exercícios.** As três atividades da página "Exercícios" são complementares e estão identificadas assim. Não têm gabarito publicado. Os critérios de conferência foram testados com uma resolução minha, que não está no site. Diga se quer manter, trocar ou publicar resoluções.
10. **Ponte para a aula 03.** A aula 03 abre com a motivação do encapsulamento: qualquer parte do programa pode escrever `livro1.disponivel = true` e desfazer um empréstimo. Confirme se essa é a motivação que você usa em aula.

## 2b. Aula 03

1. **Ordem de construção.** O roteiro parte do código da aula 02 e chega ao da aula 03 em quatro passos: atributos `private` com getters e setters; regras em `getNome` e `setNome`; a classe `Editora`; a classe `Autor`. Essa ordem é uma reconstrução minha, porque o repositório só tem o código final. Foi esse o caminho em aula?
2. **O autor some e volta.** Para o código compilar a cada passo, o roteiro manda remover o atributo `autor` e o trecho do `HashSet` no Passo 1 e os traz de volta no Passo 4, já com a classe `Autor`. Diga se prefere outra sequência.
3. **`getNome` em maiúsculas.** Com essa regra, `buscaLivro(lista, "Duna")` passa a devolver `false`. O roteiro usa isso como atividade. É um efeito intencional?
4. **`toString` usa o atributo, não o getter.** Por isso a lista da editora mostra `Duna 1965` e o `getNome` mostra `DUNA`. O Exercício 1 explora a diferença, sem afirmar que é intencional.
5. **Getters e setters de `Editora` e `Autor`.** O roteiro pede que o aluno os escreva, com a resposta em um bloco recolhido no caso de `Editora`. Se as aulas usam a geração automática da IDE, vale mencionar?
6. **Exercícios.** São três, complementares. O terceiro pede o método `contaLivros()` em `Editora`, que aparece no código da aula 04. Diga se prefere não antecipar.

## 2c. Aula 04

1. **Aula estúdio.** O site apresenta a aula 04 como aula estúdio, com a sua lista de exercícios reproduzida na íntegra. Só os níveis de título foram ajustados. Confirme se pode ser publicada assim.
2. **O projeto `aula-04/aula`.** A pasta também tem a biblioteca com `EditoraService` e `LivroService`, em commits de 25/08. O site não trata desse código. Ele foi feito em qual aula? Se for a base da aula 05, o roteiro dela pode começar por ele.
3. **Resolução.** O fim do roteiro aponta para `exercicios-maven` e pede que o aluno tente antes. Prefere que a resolução não seja indicada?
4. **Artilheiro.** O teste do Exercício 1 pede o artilheiro, mas as classes listadas não registram quem fez cada gol, e a resolução não o mostra. O roteiro avisa o aluno de que é uma decisão de modelagem dele. Há uma solução esperada?
5. **Orientações do roteiro.** A ordem sugerida das classes e as três dicas recolhidas são minhas.

## 2d. Aulas 05 e 06

1. **A pasta `aula-05` não é mais a da aula 05.** Ela foi atualizada em 06/09 e hoje é quase igual à da aula 06. Para separar as duas aulas, recuperei a versão de 31/08 (commit `9c92882`) e a tomei como o código da aula 05. O roteiro da aula 05 aponta para esse commit. Confirme se a divisão abaixo corresponde ao que foi dado.
    - **Aula 05:** projeto no Spring Initializr, classes da biblioteca com identificadores, rotas de editora e de livro. Os controllers criam os services com `new`, e o livro é cadastrado sem editora.
    - **Aula 06:** ligação do livro com a editora, o problema de haver dois `EditoraService`, `@Service` e `@Autowired`, e a propriedade do Jackson.
2. **Ponto de partida da aula 05.** O roteiro gera o projeto no Spring Initializr (Maven, Java 25, Spring Web, grupo `br.edu.insper`, artefato `biblioteca`). O `pom.xml` gerado hoje é igual ao do repositório.
3. **As classes de serviço da pasta `aula-04/aula`.** O roteiro da aula 05 cria `EditoraService` e `LivroService` direto, sem passar pela versão em Java puro dessa pasta. Diga se quer um passo para isso.
4. **Ordem dos passos da aula 06.** É uma reconstrução minha: primeiro o aluno tenta `new EditoraService()` dentro do `LivroService` e recebe 400; corrige com a propriedade; recebe 201 com corpo vazio, porque há dois services; por fim troca por `@Service` e `@Autowired`. No histórico, a propriedade entrou em um commit depois das anotações. Foi essa a sequência em aula?
5. **Singleton.** O roteiro da aula 06 explica o problema e a solução do Spring, e só cita o singleton manual que está em comentário no código. Quer que o aluno escreva esse singleton antes de usar `@Service`?
6. **Sobras.** Os roteiros não constroem `imprimirLivros` e `contaLivros`, nem os trechos comentados. Eles são listados ao aluno como diferenças.
7. **Como testar.** Os roteiros usam `curl` e o navegador, e citam o Postman. Qual ferramenta as aulas usam?
8. **Exercícios.** São três por aula, complementares. O Exercício 2 da aula 06 cria a rota `GET /editora/{id}/conta-livros`; a aula 08 tem uma rota parecida, `/editora/conta-livros`.

## 2e. Aula 07

1. **Aula estúdio.** O site apresenta a aula 07 como aula estúdio, com a sua lista reproduzida na íntegra, inclusive as orientações de entrega, que citam o BlackBoard. Só os níveis de título foram ajustados. Confirme se pode ser publicada assim e se as orientações de entrega continuam valendo.
2. **Validações antes do tratamento de erros.** A lista pede validações, mas devolver erro ao cliente só aparece na aula 08. O roteiro orienta o aluno a recusar devolvendo `null`, como o `LivroService.criar` da aula 06. Prefere outra orientação?
3. **`PATCH`.** Duas rotas usam `PATCH`, que não aparece nos códigos das aulas anteriores. O roteiro explica em uma dica. Foi apresentado em aula?
4. **Paginação.** A introdução do Exercício 2 fala em paginação, mas nenhuma rota a pede. O roteiro não toca no assunto.
5. **Referência circular no JSON.** A resolução da aula 04 tem `Time.getJogadores()` e `Jogador.getTime()`. Quem a usar como modelo vai ter uma resposta JSON em laço. O roteiro avisa em uma dica e sugere tirar o getter de um dos lados. Reproduzi o problema na biblioteca da aula 06, acrescentando um `getLivros()` em `Editora`: a resposta se repete até o limite de 500 níveis do conversor de JSON. Há uma solução que você prefira ensinar?
6. **Orientações do roteiro.** A ordem sugerida e as cinco dicas são minhas. Não há resolução no repositório para comparar.

## 2f. Aula 08

1. **Ordem dos passos.** O roteiro segue o seu README (Swagger, DTO, erros) e acrescenta a reorganização em pacotes como segundo passo, antes de criar as classes novas. No histórico, os pacotes aparecem só no último commit. Em que momento da aula a reorganização foi feita?
2. **Mover arquivos à mão.** O roteiro dá a tabela de pastas e de `import` e avisa que a IDE faz isso sozinha. Se a turma usa IntelliJ, vale trocar a tabela pela instrução de refatorar?
3. **`contaLivros`.** A rota do DTO depende de `Editora.contaLivros()`, que vem do código da pasta `aula-04/aula` e que os roteiros anteriores não construíam. O Passo 3 pede que o aluno o acrescente.
4. **Erro em duas etapas.** No Passo 4, o aluno primeiro lança a exceção e vê um 500, e só depois cria o tratador e vê o 404. Foi assim em aula?
5. **Validação do nome.** `EditoraService.criar` lança `RuntimeException`, que não tem tratador, e a API responde 500. O roteiro constrói igual, aponta o problema no fim e o resolve no Exercício 2, com uma `CampoInvalidoException` como a da aula 10. Era intencional deixar para depois?
6. **`Object` em `getEditora`.** No repositório, o método do controller devolve `Object`. O roteiro mantém `Editora` e lista isso como diferença. Há um motivo para o `Object`?
7. **Seu README.** O roteiro cita o README da pasta, mas não o reproduz. Os nomes usados nos exemplos dele são diferentes dos do código, como já registrado na seção 3.
8. **Exercícios.** São três, complementares: prever códigos, tratar o nome inválido com 400 e repetir o tratamento de erros para livros.

## 2g. Aula 09

1. **Ordem dos passos.** Reconstrução minha: primeiro só a dependência, para o aluno ver a API trancada e o usuário gerado pelo Spring; depois o `SecurityConfig`; depois o pacote `usuario`. Foi esse o caminho em aula?
2. **Nível de explicação.** O roteiro explica `SecurityConfig` como uma lista de regras, sem entrar em filtros, lambdas ou CSRF. `@Bean` e `implements` aparecem com uma frase cada. `implements` só é tratado como assunto na aula 11. Diga se quer mais ou menos detalhe aqui.
3. **CSRF.** O código desativa o CSRF e o roteiro não comenta. Quer uma frase sobre isso?
4. **Seus exercícios.** A página de exercícios reproduz os três enunciados do README e acrescenta objetivo, forma de conferir e dica. Para "recuperação de senha", que o enunciado não detalha, propus uma interpretação: uma rota aberta que recebe e-mail e senha nova. Qual era a rota esperada?
5. **Senha na resposta.** `POST /usuarios` devolve o `CreateUserDTO` com a senha codificada. O roteiro constrói igual e aponta isso no fim como ponto para pensar. É assunto de uma aula posterior?
6. **Swagger bloqueado.** A partir desta aula o roteiro volta ao `curl`, porque o Swagger exige autenticação até o aluno fazer o seu Exercício 3.

## 2h. Aula 10

1. **Aula estúdio.** O site apresenta a aula 10 como aula estúdio, com o seu enunciado reproduzido na íntegra. O roteiro de apoio não constrói o código: sugere uma ordem de trabalho e dá seis dicas. Os commits da resolução são todos de 19/09, em cinco etapas. A resolução foi feita por você em aula, passo a passo? Nesse caso, prefere um roteiro que construa a resolução, como nas outras aulas?
2. **Resolução indicada.** O roteiro aponta para `aula-10/equipamento` e para a coleção do Postman, pedindo que o aluno tente antes. Confirme se pode.
3. **Equipamento nasce indisponível.** Na resolução, `addEquipamento` não define `disponivel`, e um equipamento cadastrado sem esse campo no JSON não pode ser locado. A coleção do Postman envia o campo. O roteiro avisa o aluno em uma dica, comparando com o `livro2` da aula 02. É o comportamento esperado?
4. **Códigos de resposta.** Na resolução, os cadastros respondem 200, e não 201 como na biblioteca. O roteiro não exige um nem outro.
5. **Swagger.** O enunciado não pede, e a resolução não inclui a dependência.

## 2i. Aula 11

1. **O `Main` do repositório não compila**, como já registrado na seção 3. O roteiro constrói um `main` que faz as mesmas três coisas, na mesma ordem (lista de avaliadores, lista de pessoas, reserva de sala), com duas correções: `a.avaliar(coordenador)` no lugar de `a.avaliar()`, e `sala.reservar(aluno)` no lugar de `sala.reservar(p)`. Ele também cria as pessoas em variáveis e dá nome a elas, para a saída não ser `null`. O roteiro diz ao aluno que o `main` do repositório não compila e parece um rascunho. Prefere corrigir o arquivo no repositório? Qual era a versão final em aula?
2. **Ordem dos passos.** Reconstrução minha: herança com `Pessoa` ainda concreta; sobrescrita de `getDocumento` e a classe `Sala`; `Pessoa` abstrata com `apresentacao` e a classe `Coordenador`; por fim a interface `Avaliar`. Foi esse o caminho?
3. **`avaliar` vazio.** Nas duas classes o método não tem corpo. O roteiro constrói igual, e o Exercício 1 pede que o aluno o preencha. Era para ficar vazio?
4. **Atributo `cpf` em `Sala`.** Ele guarda o documento de quem reservou, que para um aluno é o RA. O roteiro usa isso na atividade de polimorfismo e não comenta o nome do atributo.
5. **Ligação com a aula 09.** O roteiro lembra que `UsuarioService implements UserDetailsService` já era uma interface, e que `List` também é. Confirme se a comparação é adequada.

## 2j. Aula 12

1. **Ordem dos passos.** Reconstrução minha: projeto e `application.properties`; `Destino` com lista simples; filtro e paginação; `Atracao` e o relacionamento. O aluno começa com `List<Destino>` e só depois troca por `Page`, para ver primeiro a persistência funcionando.
2. **Login no console do H2.** Com a configuração da aula, o banco é criado com nome de usuário vazio. O console vem com `sa` preenchido e recusa o login. O roteiro manda apagar o `sa`. Verifiquei abrindo o arquivo do banco com a ferramenta de linha de comando do H2; não testei a tela do console em um navegador. Em aula o login foi feito assim, ou há uma linha `spring.datasource.username` que não está no repositório?
3. **Referência circular à espera.** `Destino.getAtracoes()` e `Atracao.getDestino()` existem os dois. Como a aula 12 não tem rota para criar atrações, o problema não aparece; mas basta existir uma atração para `GET /destinos` responder em laço. O Exercício 3 leva o aluno a descobrir isso, e a aula 13 conserta com `@JsonIgnore`. Era essa a intenção?
4. **Aviso de `PageImpl`.** Devolver `Page` direto do controller gera um aviso no log a cada listagem. O roteiro diz ao aluno que pode ignorar. Quer tratar do assunto?
5. **Pasta `data` versionada.** O roteiro avisa que o repositório traz o banco do professor e que o do aluno é outro.
6. **Sem tratamento de erros.** Cadastrar um destino sem país responde 500. O roteiro mostra isso em uma atividade e remete à aula 08.

## 2k. Aula 13

1. **Três assuntos em um roteiro.** Os commits da pasta são de 05/10, 06/10 e 08/10, o que sugere mais de um encontro. O roteiro junta tudo em três passos: rotas de atração com `@JsonIgnore`; visitantes e o muitos-para-muitos; segurança, Swagger e usuários no banco. É a página mais longa do site. Prefere dividir em duas aulas? Como foi a divisão real?
2. **Ordem.** No histórico, a segurança e o Swagger já estão no primeiro commit, antes de `Visitante`. No roteiro eles vêm por último, para o aluno testar os relacionamentos sem precisar se autenticar.
3. **O laço no JSON.** O Passo 1 faz o aluno ver a resposta em laço antes de aplicar `@JsonIgnore`. O commit "fix relacao" indica que isso também apareceu em aula. Confirme.
4. **`Optional`.** `findById(...).orElseThrow(...)` aparece pela primeira vez e é explicado como "uma caixa que pode estar vazia", sem nomear `Optional` nem falar de lambdas. Diga se quer mais detalhe.
5. **Erros.** `buscarPorId`, `getById` e a associação de visitante lançam `RuntimeException`, e a API responde 500. O roteiro constrói igual, e o Exercício 2 propõe o 404.
6. **PostgreSQL.** O roteiro descreve o driver e as três linhas comentadas, sem pedir que o aluno os acrescente, porque não havia um banco para testar. A conexão foi demonstrada em aula? Os alunos precisam criar uma conta em algum serviço?
7. **Console do H2.** Com a segurança ligada, a resposta do console traz `X-Frame-Options: DENY`. O roteiro avisa que o console deixa de funcionar direito e que a aula 14 corrige. Não vi a tela em um navegador; a afirmação vem do cabeçalho.
8. **Nome do atributo `atracaos`**, em `Visitante`. Aparece assim no JSON. O roteiro mantém.

## 2l. Aula 14

1. **O produto final não está no repositório.** A pasta tem o código da aula 13 com a linha do `frameOptions`, e o README com as mudanças. O roteiro segue o README e diz isso ao aluno. Implementei o README inteiro e ele funciona. Pretende publicar o código final na pasta?
2. **Pacotes e `import`.** O README não diz onde as classes ficam. Coloquei `JwtService`, `JwtAuthenticationFilter`, `ErrorResponseDTO` e `GlobalExceptionHandler` em `common`, e `LoginRequest` e `LoginResponse` em `usuario`.
3. **`ErrorResponseDTO`.** O README usa a classe, com `getErrors()`, `setStatus` e `setMessage`, mas não a mostra. Criei uma com `message`, `status` e uma lista `errors`.
4. **Código HTTP da validação.** O `GlobalExceptionHandler` do README não tem `@ResponseStatus`, e a API responde 200 com `"status":400` no corpo. O roteiro constrói como no README, faz o aluno notar a diferença em uma atividade e acrescenta `@ResponseStatus(HttpStatus.BAD_REQUEST)`. Era intencional?
5. **403 no lugar de 401.** Com o HTTP Basic desligado e sem um ponto de entrada configurado, as requisições sem token respondem 403. O login com senha errada também, porque a `RuntimeException` vira 500 e a página de erro é protegida. O roteiro explica o primeiro caso, e o Exercício 1 leva o aluno ao segundo. Quer que o roteiro configure a resposta 401?
6. **Token inválido.** O filtro chama `extractUsername` antes de `isTokenValid`. Um token malformado ou com assinatura errada lança exceção dentro do filtro, que aparece no log. Para o cliente, o resultado é 403.
7. **JWT em dois passos.** O README apresenta oito itens de uma vez. O roteiro os divide: primeiro o login que emite o token, com a API ainda em Basic; depois o filtro que o aceita.
8. **Chave secreta.** O roteiro usa uma chave de exemplo diferente da do README e repete o seu aviso de que ela não deve ir para o repositório. A Atividade 1 sugere colar o token em `jwt.io` para ver o conteúdo; é um token de teste, mas diga se prefere outra forma.
9. **DTOs.** O README anuncia DTOs de requisição e de resposta e não os descreve. O roteiro registra isso, e o Exercício 3 cria um DTO de resposta para o cadastro de usuário.
10. **Título do passo 8 do README**, "Configuração do Swagger (application.properties)", que mostra uma classe Java, como já registrado na seção 3.

## 3. Observações sobre os códigos originais

Registradas para sua avaliação. Nenhuma foi corrigida.

| Onde | Observação |
| --- | --- |
| `aula-02`, `Main.java`, linha 34 | `buscaLivro(null, null)` lança `NullPointerException`. Ver o item 2 da seção 2. |
| `aula-02`, `Livro.java`, linha 3 | `import java.util.ArrayList` sem uso. |
| `aula-03`, `Livro.java` | O construtor sem parâmetros não cria a lista `autores`. Um livro criado com `new Livro()` teria `getAutores()` igual a `null`, e o laço dos autores no `main` falharia. O `main` não usa esse construtor. |
| `aula-03`, `Editora.java`, `addLivro` | O teste `livro.getNome() == null` não chega a proteger: se o nome for `null`, `getNome()` falha antes, em `toUpperCase()`. |
| `aula-04`, `exercicios.md` | O atributo de `Partida` está escrito `golVisitate`. A cópia no site mantém a grafia original. |
| `aula-07`, `exercicios.md` | Há um erro de digitação em "cleitnes". A cópia no site mantém a grafia original. |
| `aula-08`, `README.md` | Os exemplos usam `LivroNotFoundException`, `EditoraLivroDTO` e `setCode`; o código usa `EditoraNaoEncontradaException`, `EditoraLivrosDTO` e `setStatusCode`. |
| `aula-09`, `README.md` | O título é "Aula 08 - 08/09/2026", igual ao da aula 08. Um bloco de código está marcado com a linguagem `service`. |
| `aula-09`, `SecurityConfig.java` | Libera `GET /usuarios`, mas o controller só tem `POST /usuarios`. Pode ser parte do exercício 2 do README. Como está, `GET /usuarios` responde 401: a rota não existe, e a página de erro do Spring é protegida. Pelo mesmo motivo, um erro em uma rota aberta aparece para o cliente como 401. |
| `aula-10` | O enunciado se chama `execicio.md` e tem um erro de digitação em "Poode"; a cópia no site mantém a grafia. A classe `EditoraErrorHandler`, no pacote `cliente`, trata `ClienteNaoEncontradoException`. |
| `aula-11`, `Main.java`, linhas 18 e 34 | O projeto não compila: `a.avaliar()` sem argumento e uso de `p` fora do laço. |
| `aula-12` a `aula-14`, pasta `data/` | Os arquivos do banco H2 estão versionados. |
| `aula-14`, `README.md` | Anuncia DTOs de requisição e resposta, mas não há seção sobre eles. O passo 8 se chama "Configuração do Swagger (application.properties)" e mostra uma classe Java. |
| `aula-14`, código | As classes e dependências descritas no README não estão no projeto. |
| Projetos com Spring | O arquivo `mvnw` está versionado, mas a pasta `.mvn/` é ignorada pelo `.gitignore` da raiz. Testei na aula 05: o `mvnw` do repositório falha, por falta de `.mvn/wrapper/maven-wrapper.properties`. Quem clona precisa ter o Maven instalado ou abrir o projeto em uma IDE. |
| `aula-05`, rotas de busca | `GET /editora/{id}` e `GET /livro/{isbn}` com identificador inexistente respondem 200 sem corpo. Na versão atual, `POST /livro` sem o campo `editora` responde 500, e com uma editora inexistente responde 201 sem corpo e sem salvar. A aula 08 trata parte disso. |

### Dados sensíveis

Nenhuma credencial foi reproduzida no site nem nos documentos de revisão. Dois pontos para sua atenção:

- **`aula-14/turismo/src/main/resources/application.properties`** tem, em uma linha comentada, o endereço real de um servidor de banco de dados na nuvem. Usuário e senha estão como marcadores, tanto na versão atual quanto no histórico. Se o endereço não deveria ser público, vale trocá-lo por um marcador, como na aula 13.
- **As coleções do Postman** das aulas 10, 13 e 14 trazem e-mails e senhas de exemplo nos corpos das requisições e na configuração de autenticação. Parecem dados de teste. Confirme que nenhum é uma senha real.

## 4. Dúvidas gerais

- **Datas.** O README da aula 08 está datado de 08/09/2026. Quer que os roteiros mostrem as datas das aulas?
- **Avaliações e projetos.** Há enunciados de APS, projetos ou simulados que devam entrar no site?

## 5. O que foi e o que não foi verificado

**Verificado.**

- O site é gerado sem avisos com `mkdocs build --strict`.
- Os links internos, as âncoras e os links para o GitHub foram testados em todas as páginas.
- O site foi servido localmente em um subcaminho igual ao do GitHub Pages, e as páginas foram vistas em largura de computador e de celular.
- O projeto da aula 02 foi compilado e executado com Maven 3.10.0 e com `javac`, em JDK 26. As saídas reproduzidas no roteiro são as obtidas nessas execuções.
- Cada passo do roteiro foi montado e executado em sequência, e as saídas citadas são as obtidas. O código final construído é igual ao do repositório, a não ser por linhas em branco, pela linha 79 comentada e por um `import` sem uso em `Livro.java`.
- Todas as respostas das atividades e todos os critérios de conferência dos exercícios foram executados.
- O mesmo foi feito para a aula 03, partindo do código final da aula 02. O resultado é igual ao do repositório, a não ser por espaçamento e pela linha comentada no fim do `main`.

**Não verificado.**

- A publicação no GitHub Pages. O arquivo de workflow está escrito, mas nunca foi executado.
- A compilação da aula 02 com um JDK 23 exato e a execução por uma IDE.
- A conexão com PostgreSQL da aula 13. Para as aulas 12, 13 e 14, cada passo do roteiro foi construído e testado, e o código final foi comparado com o do repositório. A resolução da aula 10 foi executada e testada contra o enunciado. Para as aulas 05, 06, 08 e 09, cada passo dos roteiros foi construído, empacotado e testado com requisições reais, e o código final de cada uma foi comparado com o do repositório.
- A navegação com leitor de tela. A navegação por teclado e o contraste dependem do tema Material; as cores que alterei foram escolhidas para manter contraste de pelo menos 4,5:1, mas não passei uma ferramenta de auditoria.
- A aparência em Safari e Firefox. As capturas foram feitas no Chrome.
