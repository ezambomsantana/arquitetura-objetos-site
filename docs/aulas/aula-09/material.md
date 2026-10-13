# Aula 09 — Autenticação com Spring Security

!!! abstract "Nesta aula"
    - **Você parte de:** a API da [Aula 08](../aula-08/material.md).
    - **Você termina com:** uma API que só responde a quem informa e-mail e senha.
    - **Ideias novas:** autenticação, HTTP Basic e senha embaralhada.
    - **Código pronto:** [`aula-09/biblioteca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-09/biblioteca)

## O que vamos construir

Hoje qualquer pessoa que souber o endereço da API pode cadastrar e excluir editoras e livros. Nesta aula a API passa a exigir **autenticação**: quem faz a requisição precisa provar quem é.

## Antes de começar

Parta da API que você terminou na [Aula 08](../aula-08/material.md). Os testes desta aula voltam a usar o `curl`, porque a página do Swagger também vai ficar protegida.

## Passo 1 — Trancar a API

No `pom.xml`, dentro de `<dependencies>`, acrescente:

```xml title="pom.xml"
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
```

**Execute** e peça a lista de editoras:

```bash
curl -i http://localhost:8080/editora
```

A resposta é `401`, que significa "não autenticado". Só de incluir a dependência, o Spring Security trancou todas as rotas. Você não escreveu nenhuma linha de Java.

!!! question "Atividade 1 — Observar"
    Procure, no terminal do servidor, uma linha que começa com `Using generated security password`. Copie a senha e tente de novo, agora se identificando:

    ```bash
    curl -i -u user:SENHA_COPIADA http://localhost:8080/editora
    ```

??? success "Confira"
    A resposta volta a ser `200`. Enquanto você não disser quem são os usuários, o Spring cria um, chamado `user`, com uma senha nova a cada execução. A opção `-u` do `curl` envia usuário e senha junto com a requisição. Essa forma de se identificar chama-se **HTTP Basic**.

Um usuário único com senha aleatória não serve para uma API de verdade. Os próximos passos definem as regras e os usuários.

## Passo 2 — As regras de acesso

Na pasta `utils`, crie:

```java title="SecurityConfig.java"
package br.edu.insper.biblioteca.utils;

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
                        .requestMatchers(HttpMethod.GET, "/usuarios").permitAll()
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

Essa classe não é um controller nem um service: é **configuração**. O método `securityFilterChain` se lê quase como uma lista de regras:

| Linha | Regra |
| --- | --- |
| `requestMatchers(HttpMethod.POST, "/usuarios").permitAll()` | Cadastrar um usuário é liberado para todos. Sem isso, ninguém conseguiria criar a primeira conta. |
| `requestMatchers(HttpMethod.GET, "/usuarios").permitAll()` | Listar usuários também é liberado. |
| `anyRequest().authenticated()` | Todo o resto exige autenticação. |
| `httpBasic(...)` | A identificação é por HTTP Basic, como no Passo 1. |

As setas `->` e os parênteses encadeados são a forma de o Spring Security receber a configuração. Você não precisa dominar essa sintaxe agora: leia cada linha como uma regra.

O segundo método cria o `BCryptPasswordEncoder`, que vai embaralhar as senhas antes de guardá-las. `@Bean` diz ao Spring para guardar o objeto que o método devolve, do mesmo jeito que `@Service` faz com uma classe. Assim ele pode ser entregue por `@Autowired` no próximo passo.

**Execute**, só para conferir que compila. As rotas continuam respondendo `401`: as regras existem, mas ainda não há usuários.

## Passo 3 — Os usuários

Crie a pasta `usuario`, com três arquivos. O primeiro guarda os dados de um usuário:

```java title="CreateUserDTO.java"
package br.edu.insper.biblioteca.usuario;

public class CreateUserDTO {

    private String email;
    private String senha;

}
```

Escreva um getter e um setter para `email` e para `senha`.

O segundo é o service:

```java title="UsuarioService.java"
package br.edu.insper.biblioteca.usuario;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;

import java.util.HashMap;

@Service
public class UsuarioService implements UserDetailsService {

    private HashMap<String, CreateUserDTO> usuarios = new HashMap<>();

    @Autowired
    private PasswordEncoder passwordEncoder;

    public CreateUserDTO create(CreateUserDTO userDTO) {

        userDTO.setSenha(passwordEncoder.encode(userDTO.getSenha()));

        usuarios.put(userDTO.getEmail(), userDTO);
        return userDTO;

    }


    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

        CreateUserDTO userDTO = usuarios.get(username);


        if (userDTO == null) {
            throw new UsernameNotFoundException("Usuário não encontrado");
        }
        return User
                .builder()
                .username(userDTO.getEmail())
                .password(userDTO.getSenha())
                .build();

    }

}
```

Ele tem dois métodos, com papéis diferentes:

- **`create`** é chamado por você, pelo controller. Ele troca a senha pela versão embaralhada e guarda o usuário em um `HashMap`, com o e-mail como chave.
- **`loadUserByUsername`** é chamado pelo Spring Security, a cada requisição protegida. O Spring pergunta "quem é o usuário com este e-mail?", e o método responde com os dados guardados. O Spring então compara a senha recebida com a guardada.

O que liga as duas coisas é `implements UserDetailsService`. Com isso, a classe promete ter o método `loadUserByUsername`, e o Spring Security passa a usá-la para encontrar usuários. O `@Override` marca que o método cumpre essa promessa.

O terceiro arquivo é o controller:

```java title="UsuarioController.java"
package br.edu.insper.biblioteca.usuario;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UsuarioController {

    @Autowired
    private UsuarioService usuarioService;

    @PostMapping("/usuarios")
    public CreateUserDTO createUser(@RequestBody CreateUserDTO createUserDTO) {
        return usuarioService
                .create(createUserDTO);
    }


}
```

**Execute** e cadastre um usuário. Essa rota é aberta, então não precisa de `-u`:

```bash
curl -i -X POST http://localhost:8080/usuarios \
  -H "Content-Type: application/json" \
  -d '{"email":"ana@insper.edu.br","senha":"123"}'
```

A resposta mostra o usuário guardado:

```json
{"email":"ana@insper.edu.br","senha":"$2a$10$8oOOwtLAiCnmiUfPEYj2t.P82uixKudaOv.STibAW83fvs4gOc8/q"}
```

A senha `123` não aparece. O que foi guardado é o resultado do BCrypt, que não permite descobrir a senha original. Se alguém tiver acesso aos dados, não fica sabendo a senha de ninguém.

Agora use a API como a Ana:

```bash
curl -i -u ana@insper.edu.br:123 http://localhost:8080/editora
```

A resposta é `200`.

!!! question "Atividade 2 — Prever"
    Qual é o código de status em cada caso?

    1. A mesma requisição, sem o `-u`.
    2. Com `-u ana@insper.edu.br:errada`.
    3. Com `-u bia@insper.edu.br:123`, sendo que a Bia nunca foi cadastrada.

??? success "Confira"
    `401` nos três. A API não diz se o problema foi a falta de identificação, a senha errada ou o usuário inexistente. Isso é proposital: dizer "esse e-mail existe, mas a senha está errada" ajudaria quem tenta adivinhar senhas.

!!! question "Atividade 3 — Observar"
    Abra <http://localhost:8080/swagger-ui.html> no navegador. O que acontece?

??? success "Confira"
    O navegador pede usuário e senha. A página do Swagger é uma rota como as outras e caiu na regra `anyRequest().authenticated()`. Liberá-la é um dos exercícios que o professor deixou para esta aula.

## O código da aula

O programa que você escreveu é o da aula. Compare com os arquivos da pasta [`aula-09/biblioteca`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-09/biblioteca/src/main/java/br/edu/insper/biblioteca) no repositório. As diferenças são alguns `import` que o repositório tem e não usa, além das sobras já comentadas nas aulas anteriores.

Dois pontos para pensar, que valem para o seu código e para o do repositório:

- a resposta do cadastro devolve a senha embaralhada, e uma API normalmente não devolve senha nenhuma;
- os usuários ficam em um `HashMap` e somem quando o servidor reinicia, como as editoras e os livros.

## Revisão

| Conceito | Em uma frase | No seu código |
| --- | --- | --- |
| Autenticação | Provar quem está fazendo a requisição | O `-u` do `curl` |
| HTTP Basic | Enviar usuário e senha em cada requisição | `httpBasic(...)` |
| 401 | Código de "não autenticado" | Toda rota protegida, sem `-u` |
| Configuração | Classe que ajusta o comportamento do Spring | `SecurityConfig` |
| `@Bean` | Pede ao Spring que guarde o objeto devolvido pelo método | `passwordEncoder()` |
| BCrypt | Embaralha a senha de forma que não dá para desfazer | `passwordEncoder.encode(...)` |
| `implements` | A classe promete ter os métodos que outra parte do sistema espera | `UsuarioService implements UserDetailsService` |

Quer praticar? Continue em [Exercícios](exercicios.md).
