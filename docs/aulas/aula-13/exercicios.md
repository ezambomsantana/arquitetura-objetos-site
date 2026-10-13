# Exercícios — Aula 13

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem, usando a API que você construiu. Todas as requisições precisam de `-u ana@insper.edu.br:123`, ou do botão **Authorize** do Swagger.

## Exercício 1 — Prever o JSON

**Objetivo.** Prever o efeito de `@JsonIgnore` em um relacionamento.

**Enunciado.** A visitante Ana visitou a Torre Eiffel. Sem executar, responda:

1. `GET /visitantes` mostra as atrações de cada visitante?
2. `GET /atracoes` mostra os visitantes de cada atração?
3. `GET /destinos` mostra as atrações de cada destino?

**Como conferir.** Faça as três requisições. Considere resolvido quando você consegue apontar, para cada resposta, o atributo e a anotação que a explicam. Depois responda: o que aconteceria se o `@JsonIgnore` de `Atracao.visitantes` fosse apagado?

??? tip "Dica"
    Procure os três `@JsonIgnore` do projeto. O relacionamento existe nos dois sentidos no Java e no banco; o JSON mostra só o lado que não foi ignorado.

## Exercício 2 — Buscar um destino pelo id

**Objetivo.** Expor em uma rota um método que o service já tem.

**Enunciado.** Crie a rota `GET /destinos/{id}`, que devolve um destino. O método `buscarPorId` do `DestinoService` já existe.

**Como conferir.**

- `GET /destinos/1` devolve o destino de `id` 1;
- `GET /destinos/99` responde `500`.

Explique por que o código é `500` e não `404`. Depois, se quiser ir além, faça a API responder `404` com uma mensagem, usando o que você aprendeu na [Aula 08](../aula-08/material.md): uma exceção própria e um tratador com `@ControllerAdvice`.

??? tip "Dica"
    No controller, o `id` chega com `@PathVariable int id`. Para a explicação, veja qual exceção o `orElseThrow` lança e se alguma classe do projeto a trata.

## Exercício 3 — Listar usuários sem mostrar a senha

**Objetivo.** Usar um DTO para controlar o que a API expõe.

**Enunciado.** Crie a rota `GET /usuarios`, que lista os usuários cadastrados. A resposta deve trazer só o e-mail de cada um, sem a senha e sem o `id`. Como a regra `anyRequest().authenticated()` já cobre essa rota, ela só responde a quem estiver identificado.

**Como conferir.**

- com `-u`, a resposta é `[{"email":"ana@insper.edu.br"}]`;
- sem `-u`, a resposta é `401`.

Compare com a resposta do cadastro, `POST /usuarios`, que devolve a senha embaralhada. Qual das duas formas você escolheria para uma API de verdade?

??? tip "Dica"
    Crie uma classe pequena, só com o atributo `email`, um construtor e o getter, como o `EditoraLivrosDTO` da Aula 08. No service, percorra `usuarioRepository.findAll()` e monte uma lista desses objetos.
