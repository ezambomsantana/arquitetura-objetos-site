# Aula 10 — Aula estúdio: API de locação de equipamentos

!!! abstract "Nesta aula"
    - **Formato:** aula estúdio. Você resolve os exercícios do professor, que estão na página [Exercícios](exercicios.md).
    - **Você pratica:** construir uma API completa, com erros tratados e autenticação.
    - **Você usa como base:** tudo o que a biblioteca ganhou das aulas [05](../aula-05/material.md) a [09](../aula-09/material.md).
    - **Resolução:** [`aula-10/equipamento`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-10/equipamento), para comparar depois de tentar.

## Como funciona esta aula

Esta é uma aula estúdio. Você constrói uma API inteira, do zero, juntando tudo o que a biblioteca ganhou das aulas [05](../aula-05/material.md) a [09](../aula-09/material.md): rotas, services, injeção de dependência, tratamento de erros e autenticação.

O sistema é uma **locadora de equipamentos**, com clientes, equipamentos e locações.

## Antes de começar

Crie um projeto em [start.spring.io](https://start.spring.io), como no Passo 1 da Aula 05, e acrescente a dependência **Spring Security** além de **Spring Web**.

Cada requisito do enunciado corresponde a algo que você já fez:

| O enunciado pede | Você já fez igual em |
| --- | --- |
| Rotas para criar, listar e buscar | `EditoraController`, na Aula 05 |
| Uma entidade ligada a outras duas | `LivroService.criar`, na Aula 06 |
| Campos obrigatórios | A validação do nome em `EditoraService.criar`, na Aula 08 |
| Responder 404 quando não encontra | `EditoraNaoEncontradaException` e o tratador, na Aula 08 |
| Rotas autenticadas, com uma exceção aberta | `SecurityConfig`, na Aula 09 |

Duas coisas são novas: a rota de **editar** e a **exclusão lógica**. As dicas abaixo tratam das duas.

## Um caminho para resolver

**1. Comece sem segurança.** Deixe a dependência do Spring Security para o fim. Testar com tudo trancado atrapalha no começo.

**2. Faça o cliente completo.** Classe, service e controller, com as cinco rotas, e teste. O cliente não depende de ninguém.

**3. Repita para o equipamento.** O desenho é o mesmo, então vai mais rápido.

**4. Faça a locação por último.** Ela precisa dos dois services anteriores, recebidos com `@Autowired`.

**5. Acrescente as validações e os erros.** Uma exceção para "campo inválido" e uma para "não encontrado", com os tratadores.

**6. Tranque a API.** Acrescente a dependência, o `SecurityConfig` e o cadastro de usuários da Aula 09, liberando só a listagem de equipamentos.

## Pontos que costumam travar

??? tip "A rota de editar"
    Use `@PutMapping("/clientes/{id}")`, com dois parâmetros no método: o identificador, com `@PathVariable`, e os dados novos, com `@RequestBody`. No service, busque o objeto guardado e troque os atributos pelos valores recebidos.

??? tip "Exclusão lógica"
    Na exclusão lógica, o registro não sai do `HashMap`. A classe ganha um atributo `boolean deleted`, e excluir significa mudar esse atributo para `true`.

    O trabalho está em fazer o resto do sistema respeitar isso: a listagem pula quem está excluído, e a busca trata um registro excluído como se ele não existisse. Se você concentrar essa verificação no método de buscar do service, e os outros métodos sempre passarem por ele, a regra fica em um lugar só.

??? tip "Escolher o equipamento da locação"
    O cliente vem no corpo da requisição, mas o equipamento é o sistema que escolhe. Crie no service de equipamentos um método que percorre os equipamentos e devolve o primeiro que esteja disponível e não excluído. Depois de escolhido, marque-o como indisponível. Decida também o que acontece quando não há nenhum.

??? tip "O equipamento nasce disponível?"
    Um atributo `boolean` que ninguém preenche vale `false`. Se o JSON de cadastro não trouxer o campo `disponivel`, o equipamento nasce indisponível e nenhuma locação consegue usá-lo. Você viu o mesmo efeito com o `livro2`, na Aula 02. Decida se o cadastro deve exigir o campo ou se o equipamento deve nascer disponível.

??? tip "Datas no JSON"
    Para `dataInicio` e `dataFim`, use o tipo `LocalDate`. No JSON, a data vai como texto, no formato `"2026-09-20"`.

??? tip "Liberar só a listagem de equipamentos"
    No `SecurityConfig`, a regra leva o verbo e o endereço: `requestMatchers(HttpMethod.GET, "/equipamentos").permitAll()`. Assim, listar fica aberto, e cadastrar um equipamento, que usa `POST` no mesmo endereço, continua protegido.

## Depois de tentar

O repositório tem uma resolução completa, na pasta [`aula-10/equipamento`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-10/equipamento). Use-a para comparar com a sua, e não como ponto de partida. Ela traz também uma coleção do Postman, com uma requisição pronta para cada rota.

Para conferir a sua solução contra o enunciado:

- [ ] as três entidades têm rotas para criar, listar, buscar, editar e excluir;
- [ ] um registro excluído não aparece na listagem, e buscá-lo responde `404`;
- [ ] cadastrar com um campo faltando responde `400`, com uma mensagem;
- [ ] criar uma locação escolhe um equipamento sozinho, e esse equipamento deixa de estar disponível;
- [ ] sem usuário e senha, só a listagem de equipamentos responde; o resto dá `401`.
