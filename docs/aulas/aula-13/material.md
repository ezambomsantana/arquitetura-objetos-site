# Aula 13 — Mais relacionamentos e usuários no banco

!!! abstract "Nesta aula"
    - **Você parte de:** a API da [Aula 12](../aula-12/material.md).
    - **Você termina com:** atrações e visitantes ligados no banco, e a API protegida por usuário e senha.
    - **Ideias novas:** `@JsonIgnore`, muitos para muitos e tabela de ligação.
    - **Código pronto:** [`aula-13/turismo`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-13/turismo)

## O que vamos construir

A API de turismo da [Aula 12](../aula-12/material.md) guarda destinos no banco e já tem a tabela de atrações, mas sem rotas. Nesta aula ela cresce em três frentes:

- as **atrações** ganham rotas, e aparece um problema ao transformar objetos ligados em JSON;
- entram os **visitantes**, com um relacionamento **muitos para muitos**;
- a API volta a ser **protegida**, como a biblioteca da Aula 09, agora com os usuários guardados no banco.

## Antes de começar

Parta da API que você terminou na Aula 12. O banco continua o mesmo: os destinos que você cadastrou ainda estão no arquivo da pasta `data`.

Se você fez o Exercício 3 da Aula 12, já tem um `AtracaoService` e um `AtracaoController`. Troque-os pelos do Passo 1.

## Passo 1 — Rotas para as atrações

Para cadastrar uma atração, o service precisa encontrar o destino dela no banco. Em `DestinoService.java`, acrescente:

```java title="DestinoService.java"
    public Destino buscarPorId(int id) {
        return destinoRepository
                .findById(id)
                .orElseThrow(() -> new RuntimeException("Destino não encontrado"));
    }
```

`findById` não devolve um `Destino` direto, porque o destino pode não existir. Ele devolve uma caixa que pode estar cheia ou vazia. `orElseThrow` abre a caixa: se houver um destino, devolve; se não, lança a exceção indicada.

Na pasta `atracao`, crie o service:

```java title="AtracaoService.java"
package br.insper.turismo.atracao;

import br.insper.turismo.destino.Destino;
import br.insper.turismo.destino.DestinoService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;

@Service
public class AtracaoService {

    @Autowired
    private AtracaoRepository atracaoRepository;

    @Autowired
    private DestinoService destinoService;

    public Atracao salvar(Atracao atracao) {
        Destino destino = destinoService.buscarPorId(atracao.getDestino().getId());

        atracao.setDestino(destino);

        return atracaoRepository.save(atracao);
    }

    public Page<Atracao> list(String pais, Pageable pageable) {
        return atracaoRepository.findAll(pageable);
    }

}
```

É o mesmo desenho do `LivroService` da Aula 06: o JSON traz só o `id` do destino, e o service troca esse destino incompleto pelo verdadeiro, buscado no banco.

E o controller:

```java title="AtracaoController.java"
package br.insper.turismo.atracao;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.web.bind.annotation.*;

@RestController
public class AtracaoController {

    @Autowired
    private AtracaoService atracaoService;

    @GetMapping("/atracoes")
    public Page<Atracao> list(@RequestParam(required = false) String pais,
                              Pageable pageable) {
        return atracaoService.list(pais, pageable);
    }

    @PostMapping("/atracoes")
    public Atracao save(@RequestBody Atracao atracao) {
        return atracaoService.salvar(atracao);
    }
}
```

**Execute** e cadastre uma atração para o destino de `id` 1:

```bash
curl -X POST http://localhost:8080/atracoes \
  -H "Content-Type: application/json" \
  -d '{"nome":"Torre Eiffel","descricao":"Torre de ferro","destino":{"id":1}}'
```

!!! question "Atividade 1 — Observar e explicar"
    A resposta vem enorme, repetindo os mesmos dados centenas de vezes. Olhe o começo dela e tente dizer por quê. Abrir `/destinos` no navegador mostra o mesmo problema.

??? success "Confira"
    O Spring monta o JSON seguindo os getters. A atração tem um destino; o destino tem uma lista de atrações; cada atração da lista tem o seu destino, que tem a sua lista... A ida e a volta não acabam, e o conversor só para ao atingir um limite de profundidade.

    A atração foi salva normalmente. O problema está só em transformar os objetos em texto.

O conserto é dizer ao conversor para não seguir um dos lados. Em `Destino.java`, acrescente `import com.fasterxml.jackson.annotation.JsonIgnore;` e a anotação acima do atributo:

```java title="Destino.java"
    @JsonIgnore
    @OneToMany(mappedBy = "destino")
    private List<Atracao> atracoes;
```

`@JsonIgnore` quer dizer "não inclua este atributo no JSON". O destino continua tendo as suas atrações no Java e no banco; elas só não aparecem na resposta.

**Execute** e abra <http://localhost:8080/atracoes>. A atração aparece uma vez, com o destino dentro:

```json
{"descricao":"Torre de ferro","destino":{"id":1,"nome":"Paris","pais":"França"},"id":1,"nome":"Torre Eiffel"}
```

## Passo 2 — Visitantes: muitos para muitos

Um visitante vai a várias atrações, e uma atração recebe vários visitantes. É o relacionamento **muitos para muitos**, como o de livros e autores da Aula 03.

Em um banco de dados, uma coluna não consegue guardar uma lista. A solução é uma terceira tabela, só de ligações, em que cada linha diz "este visitante foi a esta atração".

Crie a pasta `visitante` e a entidade:

```java title="Visitante.java"
package br.insper.turismo.visitante;

import br.insper.turismo.atracao.Atracao;
import jakarta.persistence.*;

import java.util.ArrayList;
import java.util.List;

@Entity
public class Visitante {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    @Column(nullable = false)
    private String nome;
    @Column(nullable = false, unique = true)
    private String cpf;

    @ManyToMany(mappedBy = "visitantes")
    private List<Atracao> atracaos = new ArrayList<>();

}
```

Escreva os getters e setters dos quatro atributos. O `unique = true` impede dois visitantes com o mesmo CPF.

Do outro lado, em `Atracao.java`, acrescente os `import` de `Visitante`, `JsonIgnore`, `ArrayList` e `List`, o atributo abaixo e o getter e o setter dele:

```java title="Atracao.java"
    @JsonIgnore
    @ManyToMany
    @JoinTable(
            name = "atracao_visitante",
            joinColumns = @JoinColumn(name = "id_atracao"),
            inverseJoinColumns = @JoinColumn(name = "id_visitante")
    )
    private List<Visitante> visitantes = new ArrayList<>();
```

| Anotação | O que diz |
| --- | --- |
| `@ManyToMany` | "Muitas atrações para muitos visitantes." |
| `@JoinTable(name = "atracao_visitante", ...)` | "Guarde as ligações em uma tabela com este nome, com uma coluna para o `id` da atração e outra para o `id` do visitante." |
| `@ManyToMany(mappedBy = "visitantes")` | No visitante: "a ligação já está descrita do outro lado." |
| `@JsonIgnore` | Evita o mesmo laço do Passo 1, agora entre atração e visitante. |

O repository:

```java title="VisitanteRepository.java"
package br.insper.turismo.visitante;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface VisitanteRepository extends JpaRepository<Visitante, Integer> {
}
```

Em `AtracaoService.java`, acrescente um método para buscar uma atração pelo `id`:

```java title="AtracaoService.java"
    public Atracao getById(int idAtracao) {
        return atracaoRepository
                .findById(idAtracao)
                .orElseThrow(() -> new RuntimeException("Atracao nao encontrada"));
    }
```

O service dos visitantes:

```java title="VisitanteService.java"
package br.insper.turismo.visitante;

import br.insper.turismo.atracao.Atracao;
import br.insper.turismo.atracao.AtracaoService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Service
public class VisitanteService {

    @Autowired
    private VisitanteRepository visitanteRepository;

    @Autowired
    private AtracaoService atracaoService;

    public Visitante salvar(Visitante visitante) {
        return visitanteRepository.save(visitante);
    }

    public List<Visitante> list() {
        return visitanteRepository.findAll();
    }

    public Visitante adicionaVisitanteAtracao(int idVisitante, int idAtracao) {

        Atracao atracao = atracaoService.getById(idAtracao);
        Visitante visitante = getById(idVisitante);

        visitante.getAtracaos().add(atracao);
        atracao.getVisitantes().add(visitante);

        return visitanteRepository.save(visitante);

    }

    private Visitante getById(int idVisitante) {
        return visitanteRepository
                .findById(idVisitante)
                .orElseThrow(() -> new RuntimeException("Visitante nao encontrado"));
    }
}
```

O método `adicionaVisitanteAtracao` faz a ligação nos dois sentidos, com as mesmas duas linhas que ligavam livro e autor na Aula 03. A diferença é a última linha: ao salvar, a ligação vai para o banco.

E o controller:

```java title="VisitanteController.java"
package br.insper.turismo.visitante;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
public class VisitanteController {

    @Autowired
    private VisitanteService visitanteService;

    @GetMapping("/visitantes")
    public List<Visitante> list() {
        return visitanteService.list();
    }

    @PostMapping("/visitantes")
    public Visitante salvar(@RequestBody Visitante visitante) {
        return visitanteService.salvar(visitante);
    }

    @PostMapping("/visitantes/{idVisitante}/atracoes/{idAtracao}")
    public Visitante adicionaVisitanteAtracao(@PathVariable int idVisitante,
                                              @PathVariable int idAtracao) {
        return visitanteService.adicionaVisitanteAtracao(idVisitante, idAtracao);
    }
}
```

**Execute.** Cadastre um visitante e registre a visita dele à atração 1:

```bash
curl -X POST http://localhost:8080/visitantes \
  -H "Content-Type: application/json" \
  -d '{"nome":"Ana","cpf":"111"}'

curl -X POST http://localhost:8080/visitantes/1/atracoes/1
```

A segunda resposta mostra a visitante com a atração dentro:

```json
{"atracaos":[{"descricao":"Torre de ferro","destino":{"id":1,"nome":"Paris","pais":"França"},"id":1,"nome":"Torre Eiffel"}],"cpf":"111","id":1,"nome":"Ana"}
```

!!! question "Atividade 2 — Prever"
    Quantas tabelas o banco tem agora, e quais colunas você espera na tabela nova de ligações? Confira no console do H2.

??? success "Confira"
    Quatro: `DESTINO`, `ATRACAO`, `VISITANTE` e `ATRACAO_VISITANTE`. A última tem só duas colunas, `ID_ATRACAO` e `ID_VISITANTE`, e uma linha para a visita que você registrou. Nenhuma classe Java corresponde a essa tabela: ela existe só para guardar as ligações.

## Passo 3 — Proteger a API, com os usuários no banco

Falta trazer para este projeto o que a biblioteca já tinha: a autenticação da Aula 09 e o Swagger da Aula 08. A novidade é que os usuários deixam de ficar em um `HashMap` e viram uma tabela.

No `pom.xml`, acrescente as duas dependências:

```xml title="pom.xml"
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springdoc</groupId>
			<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
			<version>3.1.0</version>
		</dependency>
```

Crie a pasta `common`, com duas classes de configuração. A primeira são as regras de acesso:

```java title="SecurityConfig.java"
package br.insper.turismo.common;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http
                .csrf(csrf -> csrf.disable())
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers(HttpMethod.POST, "/usuarios").permitAll()
                        .requestMatchers("/swagger-ui.html",
                                "/h2-console/**",
                                "/swagger-ui/**",
                                "/v3/api-docs/**",
                                "/swagger-resources/**",
                                "/webjars/**").permitAll()
                        .anyRequest().authenticated()
                )
                .httpBasic(Customizer.withDefaults())
                .build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

É a classe da Aula 09 com uma regra a mais, que libera os endereços do Swagger e do console do H2. A segunda descreve a API para o Swagger e avisa que ela usa HTTP Basic:

```java title="OpenApiConfig.java"
package br.insper.turismo.common;

import io.swagger.v3.oas.annotations.OpenAPIDefinition;
import io.swagger.v3.oas.annotations.enums.SecuritySchemeIn;
import io.swagger.v3.oas.annotations.enums.SecuritySchemeType;
import io.swagger.v3.oas.annotations.info.Info;
import io.swagger.v3.oas.annotations.security.SecurityRequirement;
import io.swagger.v3.oas.annotations.security.SecurityScheme;
import org.springframework.context.annotation.Configuration;

@Configuration
@OpenAPIDefinition(
        info = @Info(
                title = "API Turismo",
                version = "1.0.0",
                description = "API para gestão de destinos turísticos"
        ),
        security = @SecurityRequirement(name = "basicAuth")
)
@SecurityScheme(
        name = "basicAuth",
        type = SecuritySchemeType.HTTP,
        scheme = "basic",
        description = "Basic authentication"
)
public class OpenApiConfig {
}
```

Agora os usuários. Crie a pasta `usuario`, começando pela entidade:

```java title="User.java"
package br.insper.turismo.usuario;

import jakarta.persistence.*;

@Entity(name = "tb_user")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    @Column(nullable = false, unique = true)
    private String email;
    @Column(nullable = false)
    private String senha;

}
```

Escreva os getters e setters dos três atributos. O nome entre parênteses em `@Entity` define o nome da tabela, `tb_user`, porque `user` é uma palavra reservada em bancos de dados.

O repository, com uma consulta pelo e-mail:

```java title="UsuarioRepository.java"
package br.insper.turismo.usuario;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.Optional;

@Repository
public interface UsuarioRepository extends JpaRepository<User, Integer> {
    Optional<User> findByEmail(String username);
}
```

O service:

```java title="UsuarioService.java"
package br.insper.turismo.usuario;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;

import java.util.HashMap;

@Service
public class UsuarioService implements UserDetailsService {

    @Autowired
    private UsuarioRepository usuarioRepository;

    @Autowired
    private PasswordEncoder passwordEncoder;

    public User create(User userDTO) {

        userDTO.setSenha(passwordEncoder.encode(userDTO.getSenha()));

        return usuarioRepository.save(userDTO);

    }


    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

        User userDTO = usuarioRepository.findByEmail(username)
                .orElseThrow(() -> new UsernameNotFoundException("Usuário não encontrado"));

        return org.springframework.security.core.userdetails.User
                .builder()
                .username(userDTO.getEmail())
                .password(userDTO.getSenha())
                .build();

    }

}
```

Compare com o `UsuarioService` da Aula 09. Os dois métodos fazem o mesmo trabalho; o que mudou é onde os usuários ficam. `usuarios.put` virou `usuarioRepository.save`, e `usuarios.get` virou `findByEmail`.

E o controller:

```java title="UsuarioController.java"
package br.insper.turismo.usuario;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UsuarioController {

    @Autowired
    private UsuarioService usuarioService;

    @PostMapping("/usuarios")
    public User createUser(@RequestBody User createUserDTO) {
        return usuarioService
                .create(createUserDTO);
    }


}
```

**Execute.** A API voltou a responder `401` sem identificação. Cadastre um usuário e use-o:

```bash
curl -X POST http://localhost:8080/usuarios \
  -H "Content-Type: application/json" \
  -d '{"email":"ana@insper.edu.br","senha":"123"}'

curl -i -u ana@insper.edu.br:123 http://localhost:8080/destinos
```

A segunda resposta é `200`, com os destinos.

!!! question "Atividade 3 — Prever"
    Pare o servidor, execute de novo e repita o segundo comando, sem cadastrar a usuária outra vez. Na biblioteca da Aula 09, isso daria `401`. E aqui?

??? success "Confira"
    `200`. A usuária está na tabela `TB_USER`, com a senha embaralhada pelo BCrypt, e sobrevive ao reinício como os destinos.

Abra também <http://localhost:8080/swagger-ui.html>. A página abre sem pedir senha, porque foi liberada nas regras, e tem um botão **Authorize**, onde você informa e-mail e senha para testar as rotas protegidas.

O console do H2, por outro lado, deixa de funcionar direito: a página abre, mas o Spring Security bloqueia as partes internas dela. A próxima aula corrige isso com uma linha.

## O código da aula

O programa que você escreveu é o da aula. Compare com os arquivos da pasta [`aula-13/turismo`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-13/turismo/src/main/java/br/insper/turismo) no repositório. As classes são iguais, a não ser por dois `import` sem uso.

O repositório tem mais duas coisas, que este roteiro não constrói: o driver do banco **PostgreSQL** no `pom.xml` e três linhas comentadas no `application.properties`, com o endereço, o usuário e a senha de um banco PostgreSQL. Elas mostram como trocar o H2 por um banco de verdade, hospedado em um servidor: basta mudar a configuração, sem tocar nas classes. Para usá-las, é preciso ter um banco PostgreSQL disponível.

A pasta traz também uma coleção do Postman, com as requisições prontas.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| Referência circular | Dois objetos que apontam um para o outro, em laço | `Destino` e `Atracao` |
| `@JsonIgnore` | Deixa um atributo fora do JSON | `atracoes`, em `Destino` |
| Muitos para muitos | Os dois lados têm uma lista do outro | `Atracao` e `Visitante` |
| Tabela de ligação | Tabela que só guarda pares de `id` | `atracao_visitante` |
| `findById(...).orElseThrow(...)` | Busca e lança uma exceção se não achar | `DestinoService.buscarPorId` |
| Usuários persistidos | As contas ficam em uma tabela | `tb_user` |

Quer praticar? Continue em [Exercícios](exercicios.md).
