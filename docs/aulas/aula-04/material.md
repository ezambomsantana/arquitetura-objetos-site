# Aula 04 — Aula estúdio: exercícios de orientação a objetos

!!! abstract "Nesta aula"
    - **Formato:** aula estúdio. Você resolve os exercícios do professor, que estão na página [Exercícios](exercicios.md).
    - **Você pratica:** criar classes, ligar objetos e usar listas, sem roteiro pronto.
    - **Você usa como base:** a biblioteca das aulas [02](../aula-02/material.md) e [03](../aula-03/material.md).
    - **Resolução:** [`aula-04/exercicios-maven`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-04/exercicios-maven), para comparar depois de tentar.

## Como funciona esta aula

Esta é uma aula estúdio: não há código novo para acompanhar. Você recebe dois enunciados e constrói os sistemas por conta própria, usando o que fez nas aulas [02](../aula-02/material.md) e [03](../aula-03/material.md).

Os dois sistemas são um **campeonato de futebol** e uma **loja virtual**. O que se pratica, segundo a própria lista:

- criar classes, atributos, construtores e métodos;
- ligar objetos entre si, com relacionamentos de um para um, um para muitos e muitos para muitos;
- usar `ArrayList` e navegar de um objeto para outro.

## Antes de começar

Crie uma pasta para cada exercício, do mesmo jeito que na Aula 02. Cada exercício terá várias classes e uma classe `Main` para testar.

Tudo de que você precisa já apareceu na biblioteca:

| Se o enunciado pede | Você já fez igual em |
| --- | --- |
| Uma classe com atributos, construtor e getters | `Livro`, nas aulas 02 e 03 |
| Um objeto que guarda vários de outra classe | `Editora` e sua lista de livros |
| Um método que acrescenta um item à lista | `Editora.addLivro` |
| Um objeto que aponta para outro | `Livro.setEditora` |
| Um método que percorre a lista procurando algo | `buscaLivro`, no `Main` |

## Um caminho para resolver

Um enunciado com cinco ou seis classes assusta se você tentar escrever tudo de uma vez. Vá por partes e execute a cada parte.

**1. Comece pelas classes mais simples.** São as que só têm dados, sem listas. No campeonato, comece por `Jogador`; na loja, por `Produto`. Escreva os atributos de texto e número como `private`, um construtor e os getters. Deixe para o item 3 os atributos que apontam para outra classe, como o `time` do jogador e a `categoria` do produto. Crie um objeto no `Main` e imprima um atributo, só para ver funcionando.

**2. Suba para quem guarda uma lista.** `Time` guarda jogadores, do mesmo jeito que `Editora` guardava livros. Escreva o atributo `ArrayList`, crie a lista no construtor e faça o método de adicionar. Teste no `Main`: crie um time, adicione três jogadores e liste.

**3. Ligue os dois lados quando o enunciado pedir.** `Jogador` tem o atributo `time`, e `Time` tem a lista `jogadores`. É a mesma ligação nos dois sentidos de `livro1.setEditora(editora)` e `editora.addLivro(livro1)`.

**4. Deixe os métodos de cálculo para depois.** `mediaIdadeJogadores`, `calcularTotal` e `exibirClassificacao` são um `for` sobre uma lista, somando ou comparando. Só os escreva quando as classes e as listas já estiverem funcionando.

**5. Monte o `Main` do enunciado por último.** Cada exercício termina com uma seção "Teste", que diz o que criar e o que exibir. Use-a como lista de conferência.

Uma ordem possível para cada exercício:

| Exercício | Ordem sugerida |
| --- | --- |
| Campeonato de Futebol | `Jogador` → `Time` → `Partida` → `Cartao` → `Campeonato` → `Main` |
| Loja Virtual | `Produto` → `Categoria` → `ItemPedido` → `Pedido` → `Cliente` → `Loja` → `Main` |

## Pontos que costumam travar

??? tip "A classificação do campeonato"
    Nenhuma classe do enunciado guarda os pontos de um time. Eles podem ser calculados na hora: para cada time, percorra as partidas e some 3 por vitória e 1 por empate. Depois, mostre os times do maior para o menor.

??? tip "O preço dentro do pedido"
    O enunciado pede que mudar o preço de um produto não altere pedidos antigos. Por isso `ItemPedido` tem o próprio `precoUnitario`: no momento em que o produto entra no pedido, copie o preço atual para esse atributo, e calcule o subtotal a partir dele.

??? tip "O artilheiro do campeonato"
    O teste do Exercício 1 pede o artilheiro, mas, pelos atributos e métodos listados, nenhuma classe registra **quem** fez cada gol: `registrarGol` recebe só os dois números do placar. Para responder, você precisa decidir onde guardar essa informação. É uma decisão de modelagem sua.

## Depois de tentar

O repositório tem uma resolução dos dois exercícios, na pasta [`aula-04/exercicios-maven`](https://github.com/ezambomsantana/arquitetura-objetos-2026-2/tree/main/aula-04/exercicios-maven). Use-a para comparar com a sua, e não como ponto de partida. O arquivo `README.md` dessa pasta explica como executá-la.

Sua solução não precisa ser igual. Vale conferir se:

- [ ] todas as classes do enunciado existem, com os atributos pedidos;
- [ ] os atributos são `private`;
- [ ] cada lista é criada no construtor e alterada por um método da própria classe;
- [ ] o `Main` faz tudo o que a seção "Teste" do enunciado pede.
