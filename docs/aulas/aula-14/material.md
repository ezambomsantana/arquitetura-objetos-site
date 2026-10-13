# Aula 14 — Tokens JWT e validação

!!! abstract "Nesta aula"
    - **Você parte de:** a API da [Aula 13](../aula-13/material.md).
    - **Você termina com:** login com token e dados validados na entrada.
    - **Ideias novas:** token JWT, filtro e validação.
    - **Código pronto:** o programa pronto não está no repositório; a aula segue o [`README.md` do professor](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/blob/main/aula-14/turismo/README.md)

## O que vamos construir

A API de turismo está quase pronta. Nesta aula ela ganha dois acabamentos:

- a autenticação troca o HTTP Basic por **tokens JWT**: a pessoa faz login uma vez, recebe um token e passa a enviar o token, e não mais a senha;
- os dados recebidos passam por **validação**, com mensagens de erro claras.

Esta aula é diferente das outras em um ponto: a pasta do repositório traz o código **de partida**, que é o da Aula 13 com uma linha a mais, e o README do professor com o passo a passo. O programa pronto é o que você vai ter ao final.

## Antes de começar

Parta da API que você terminou na [Aula 13](../aula-13/material.md).

## Passo 1 — Consertar o console do H2

Na aula passada, o console do H2 deixou de funcionar direito depois que a segurança foi ligada. Em `SecurityConfig.java`, logo depois da linha do `csrf`, acrescente:

```java title="SecurityConfig.java"
                .headers(headers -> headers.frameOptions(frameOptions -> frameOptions.sameOrigin()))
```

O console monta a tela com quadros internos, e o Spring Security proíbe isso por padrão. A linha libera os quadros quando eles vêm do próprio site.

**Execute** e abra <http://localhost:8080/h2-console>. O console volta a funcionar. Essa é a única diferença entre o código da pasta `aula-14` e o da `aula-13`.

## Passo 2 — O login devolve um token

Com HTTP Basic, o e-mail e a senha viajam em **todas** as requisições. Com token, o fluxo é outro:

1. a pessoa faz login, enviando e-mail e senha uma única vez;
2. a API confere e devolve um **token**: um texto assinado por ela, com prazo de validade;
3. nas requisições seguintes, a pessoa envia só o token.

O formato usado é o **JWT**, sigla de *JSON Web Token*. Vamos começar pela parte que emite o token.

No `pom.xml`, acrescente a biblioteca que cria e confere tokens:

```xml title="pom.xml"
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt-api</artifactId>
			<version>0.12.3</version>
		</dependency>
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt-impl</artifactId>
			<version>0.12.3</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>io.jsonwebtoken</groupId>
			<artifactId>jjwt-jackson</artifactId>
			<version>0.12.3</version>
			<scope>runtime</scope>
		</dependency>
```

No `application.properties`, acrescente a chave com que a API assina os tokens e o prazo de validade, em milissegundos:

```properties title="application.properties"
# JWT Configuration
jwt.secret=uma-chave-secreta-de-exemplo-com-mais-de-32-caracteres
jwt.expiration=3600000
```

A chave precisa ter pelo menos 32 caracteres. Quem a conhece consegue fabricar tokens válidos, então, em um sistema de verdade, ela não fica escrita em um arquivo do repositório.

Na pasta `common`, crie o service que gera e confere tokens:

```java title="JwtService.java"
package br.insper.turismo.common;

import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.io.Decoders;
import io.jsonwebtoken.security.Keys;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import javax.crypto.SecretKey;
import java.util.Base64;
import java.util.Date;

@Service
public class JwtService {

    @Value("${jwt.secret}")
    private String secretKey;

    @Value("${jwt.expiration:3600000}") // 1 hora em ms
    private long jwtExpiration;

    // Gera um novo token JWT
    public String generateToken(String email) {
        return Jwts.builder()
                .subject(email)
                .issuedAt(new Date())
                .expiration(new Date(System.currentTimeMillis() + jwtExpiration))
                .signWith(getSigningKey(), SignatureAlgorithm.HS256)
                .compact();
    }

    // Extrai o email (subject) do token
    public String extractUsername(String token) {
        return Jwts.parser()
                .verifyWith(getSigningKey())
                .build()
                .parseSignedClaims(token)
                .getPayload()
                .getSubject();
    }

    // Valida se o token é válido (assinatura e expiração)
    public boolean isTokenValid(String token) {
        try {
            Jwts.parser()
                    .verifyWith(getSigningKey())
                    .build()
                    .parseSignedClaims(token);
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    private SecretKey getSigningKey() {
        byte[] keyBytes = Decoders.BASE64.decode(
            Base64.getEncoder().encodeToString(secretKey.getBytes())
        );
        return Keys.hmacShaKeyFor(keyBytes);
    }
}
```

São três métodos públicos: `generateToken` cria um token para um e-mail, `extractUsername` lê o e-mail de dentro de um token e `isTokenValid` confere a assinatura e o prazo. O `@Value` traz para o atributo o valor que está no `application.properties`.

Na pasta `usuario`, crie os dois DTOs do login:

```java title="LoginRequest.java"
package br.insper.turismo.usuario;

public class LoginRequest {

    private String email;
    private String senha;

}
```

Escreva um getter e um setter para os dois atributos.

```java title="LoginResponse.java"
package br.insper.turismo.usuario;

public class LoginResponse {

    private String token;

    public LoginResponse(String token) {
        this.token = token;
    }

    public String getToken() {
        return token;
    }
}
```

Em `UsuarioService.java`, acrescente `import br.insper.turismo.common.JwtService;`, o atributo e o método de login:

```java title="UsuarioService.java"
    @Autowired
    private JwtService jwtService;
```

```java title="UsuarioService.java"
    public LoginResponse login(LoginRequest loginRequest) {
        try {
            UserDetails userDetails = this.loadUserByUsername(loginRequest.getEmail());

            if (!passwordEncoder.matches(loginRequest.getSenha(), userDetails.getPassword())) {
                throw new RuntimeException("Senha inválida");
            }

            String token = jwtService.generateToken(loginRequest.getEmail());
            return new LoginResponse(token);
        } catch (UsernameNotFoundException e) {
            throw new RuntimeException("Usuário não encontrado");
        }
    }
```

O login busca o usuário pelo e-mail, compara a senha recebida com a senha embaralhada que está no banco, usando `passwordEncoder.matches`, e, se bater, pede um token ao `JwtService`.

Em `UsuarioController.java`, acrescente a rota:

```java title="UsuarioController.java"
    @PostMapping("/login")
    public LoginResponse login(@RequestBody LoginRequest loginRequest) {
        return usuarioService.login(loginRequest);
    }
```

Por fim, o login precisa ser uma rota aberta. Em `SecurityConfig.java`, abaixo da regra de `/usuarios`, acrescente:

```java title="SecurityConfig.java"
                        .requestMatchers(HttpMethod.POST, "/login").permitAll()
```

**Execute.** Cadastre uma usuária, se ainda não houver, e faça login:

```bash
curl -X POST http://localhost:8080/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ana@insper.edu.br","senha":"123"}'
```

A resposta é o token:

```json
{"token":"eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhbmFAaW5zcGVyLmVkdS5iciIsImlhdCI6...restante do token"}
```

!!! question "Atividade 1 — Observar"
    O token tem três partes, separadas por pontos. Copie o seu e cole em <https://jwt.io>. O que aparece na parte do meio? A senha está lá?

??? success "Confira"
    A parte do meio traz o e-mail (`sub`), o momento em que o token foi criado (`iat`) e o momento em que ele expira (`exp`), uma hora depois. A senha não está no token.

    Qualquer pessoa consegue **ler** um token. O que ninguém consegue, sem a chave secreta, é **alterar** um ou fabricar outro: a terceira parte é a assinatura, e ela deixa de conferir se o conteúdo mudar.

A API já emite tokens, mas ainda não sabe aceitá-los. Se você enviar o token agora, a resposta é `401`.

## Passo 3 — A API passa a aceitar o token

Falta uma peça que olhe cada requisição que chega, procure um token e, se ele for válido, considere a pessoa identificada. Uma peça que roda antes dos controllers, em toda requisição, chama-se **filtro**.

Na pasta `common`, crie:

```java title="JwtAuthenticationFilter.java"
package br.insper.turismo.common;

import br.insper.turismo.usuario.UsuarioService;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import java.io.IOException;

@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private JwtService jwtService;

    @Autowired
    private UsuarioService usuarioService;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                   FilterChain filterChain) throws ServletException, IOException {

        String authHeader = request.getHeader("Authorization");
        String token = null;
        String username = null;

        // Extrai o token do header Authorization: Bearer <token>
        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            token = authHeader.substring(7);
            username = jwtService.extractUsername(token);
        }

        // Valida o token e configura a autenticação
        if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
            if (jwtService.isTokenValid(token)) {
                UserDetails userDetails = usuarioService.loadUserByUsername(username);
                UsernamePasswordAuthenticationToken authToken =
                    new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
                SecurityContextHolder.getContext().setAuthentication(authToken);
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

O filtro lê o cabeçalho `Authorization`. Se ele começar com `Bearer `, o resto é o token. O filtro extrai o e-mail, confere a validade, busca o usuário e registra no Spring Security que a requisição está autenticada. A última linha, `filterChain.doFilter`, passa a requisição adiante.

Agora troque o conteúdo de `SecurityConfig.java` por:

```java title="SecurityConfig.java"
package br.insper.turismo.common;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http, JwtAuthenticationFilter jwtAuthenticationFilter) throws Exception {
        return http
                .csrf(csrf -> csrf.disable())
                .headers(headers -> headers.frameOptions(frameOptions -> frameOptions.sameOrigin()))
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers(HttpMethod.POST, "/usuarios").permitAll()
                        .requestMatchers(HttpMethod.POST, "/login").permitAll()
                        .requestMatchers("/swagger-ui.html", "/h2-console/**",
                                "/swagger-ui/**", "/v3/api-docs/**", "/swagger-resources/**",
                                "/webjars/**").permitAll()
                        .anyRequest().authenticated()
                )
                .addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class)
                .build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

Em relação à versão anterior, mudaram duas coisas: a linha do `httpBasic` saiu, e entrou `addFilterBefore`, que encaixa o seu filtro na fila de verificações do Spring Security. O filtro chega como parâmetro do método, entregue pelo Spring.

Para o Swagger acompanhar a mudança, troque o conteúdo de `OpenApiConfig.java` por:

```java title="OpenApiConfig.java"
package br.insper.turismo.common;

import io.swagger.v3.oas.annotations.OpenAPIDefinition;
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
    security = @SecurityRequirement(name = "bearerAuth")
)
@SecurityScheme(
    name = "bearerAuth",
    type = SecuritySchemeType.HTTP,
    scheme = "bearer",
    bearerFormat = "JWT",
    description = "Autenticação via token JWT"
)
public class OpenApiConfig {
}
```

**Execute**, faça login de novo e use o token recebido:

```bash
curl -i -H "Authorization: Bearer SEU_TOKEN" http://localhost:8080/destinos
```

A resposta é `200`. No Swagger, o botão **Authorize** agora pede o token, em vez de e-mail e senha.

!!! question "Atividade 2 — Prever"
    Qual é o código de status em cada caso?

    1. `GET /destinos` sem nenhum cabeçalho.
    2. `GET /destinos` com `-u ana@insper.edu.br:123`, como na aula passada.
    3. `GET /destinos` com o token, trocando as três últimas letras por `abc`.

??? success "Confira"
    `403` nos três. O HTTP Basic foi desligado, então e-mail e senha não valem mais nada fora do login. E um token alterado não passa pela conferência da assinatura.

    Repare que o código agora é `403`, e não o `401` das aulas anteriores. Sem o HTTP Basic, o Spring Security não tem mais uma forma padrão de pedir identificação, e responde só que o acesso foi negado.

## Passo 4 — Validar os dados recebidos

Na Aula 12, cadastrar um destino sem país respondia `500`, porque quem recusava era o banco. É melhor conferir os dados logo na entrada, antes de chegar ao service.

No `pom.xml`, acrescente:

```xml title="pom.xml"
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>
```

Em `Visitante.java`, acrescente `import jakarta.validation.constraints.NotEmpty;` e `import jakarta.validation.constraints.NotNull;`, e as anotações acima dos dois atributos:

```java title="Visitante.java"
    @NotNull(message = "Nome não pode ser nulo")
    @NotEmpty(message = "Nome não pode ser vazio")
    @Column(nullable = false)
    private String nome;
    @NotNull(message = "CPF não pode ser nulo")
    @NotEmpty(message = "CPF não pode ser vazio")
    @Column(nullable = false, unique = true)
    private String cpf;
```

As regras ficam escritas junto do atributo, cada uma com a sua mensagem. Para que sejam conferidas, o controller precisa pedir. Em `VisitanteController.java`, acrescente `import jakarta.validation.Valid;` e `@Valid` no parâmetro:

```java title="VisitanteController.java"
    @PostMapping("/visitantes")
    public Visitante salvar(@Valid @RequestBody Visitante visitante) {
        return visitanteService.salvar(visitante);
    }
```

Quando uma regra falha, o Spring lança uma exceção antes mesmo de chamar o seu método. Falta tratá-la, como na Aula 08. Na pasta `common`, crie o DTO da resposta de erro:

```java title="ErrorResponseDTO.java"
package br.insper.turismo.common;

import java.util.ArrayList;
import java.util.List;

public class ErrorResponseDTO {

    private String message;
    private int status;
    private List<String> errors = new ArrayList<>();

}
```

Escreva os getters e setters dos três atributos. A lista já nasce criada, para o tratador só precisar acrescentar mensagens.

E o tratador:

```java title="GlobalExceptionHandler.java"
package br.insper.turismo.common;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ErrorResponseDTO handleValidationException(
            MethodArgumentNotValidException ex) {

        ErrorResponseDTO errors = new ErrorResponseDTO();
        ex.getBindingResult().getFieldErrors().forEach(error ->
                errors.getErrors().add(error.getDefaultMessage())
        );

        errors.setStatus(HttpStatus.BAD_REQUEST.value());
        errors.setMessage("Erro de validação");

        return errors;
    }
}
```

`@RestControllerAdvice` é o `@ControllerAdvice` da Aula 08 já com o `@ResponseBody` embutido. O método percorre os erros de validação e copia a mensagem de cada um para a lista.

**Execute**, faça login e tente cadastrar um visitante com o nome vazio:

```bash
curl -i -X POST http://localhost:8080/visitantes \
  -H "Authorization: Bearer SEU_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"nome":"","cpf":"222"}'
```

O corpo da resposta traz a mensagem da regra que falhou:

```json
{"errors":["Nome não pode ser vazio"],"message":"Erro de validação","status":400}
```

!!! question "Atividade 3 — Observar"
    Olhe a primeira linha da resposta do `curl -i`. O código de status combina com o `"status":400` que está no corpo?

??? success "Confira"
    Não. A primeira linha diz `200`. O número 400 está só dentro do JSON; para o protocolo, a requisição deu certo. Um programa que confira apenas o código de status vai achar que o visitante foi cadastrado.

O conserto é o mesmo da Aula 08. Em `GlobalExceptionHandler.java`, acrescente `import org.springframework.web.bind.annotation.ResponseStatus;` e a anotação acima do método:

```java title="GlobalExceptionHandler.java"
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
```

**Execute** e repita o teste. Agora a primeira linha diz `400`.

## O código da aula

Nesta aula, o programa pronto não está no repositório. A pasta [`aula-14/turismo`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-14/turismo) tem o código de partida, igual ao seu depois do Passo 1, e o README do professor, de onde vêm as classes dos passos 2 a 4.

O que este roteiro acrescentou ao README:

- as pastas em que cada classe nova fica e os `import` de cada uma;
- a classe `ErrorResponseDTO`, que o README usa sem mostrar;
- a anotação `@ResponseStatus` no tratador, para o código de status acompanhar o corpo da resposta;
- a divisão do JWT em dois passos, um para emitir o token e outro para aceitá-lo.

O README anuncia uma terceira mudança, o uso de DTOs nas requisições e nas respostas, mas não a descreve. O [Exercício 3](exercicios.md#exercicio-3-uma-resposta-sem-senha) dá um primeiro passo nessa direção.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| Token | Um texto que prova quem é a pessoa, sem levar a senha | A resposta de `POST /login` |
| JWT | Formato de token com três partes, a última sendo a assinatura | `JwtService` |
| Chave secreta | O que a API usa para assinar e conferir tokens | `jwt.secret` |
| Filtro | Código que roda antes dos controllers, em toda requisição | `JwtAuthenticationFilter` |
| `Bearer` | A forma de enviar o token, no cabeçalho `Authorization` | `curl -H "Authorization: Bearer ..."` |
| Validação | Regras conferidas na entrada dos dados | `@NotEmpty`, `@NotNull`, `@Valid` |

Quer praticar? Continue em [Exercícios](exercicios.md).
