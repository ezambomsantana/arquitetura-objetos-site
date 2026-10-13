# Exercícios — Aula 11

!!! note "Exercícios complementares"
    Propostos por este site para praticar a aula. Não são do professor, não valem nota e não têm entrega.

Faça depois do [roteiro da aula](material.md), na ordem, usando o programa que você construiu.

## Exercício 1 — Dar corpo à avaliação

**Objetivo.** Implementar de formas diferentes o mesmo método de uma interface.

**Enunciado.** No código da aula, o método `avaliar` está vazio em `Aluno` e em `Professor`. Faça cada um imprimir uma linha:

- o professor imprime `Professor NOME avaliou NOME_DO_AVALIADO`;
- o aluno imprime `NOME avaliou NOME_DO_AVALIADO`.

**Como conferir.** Execute o `main` do roteiro, sem alterá-lo. O laço dos avaliadores deve imprimir, antes das apresentações:

```text
Professor Carla avaliou Ana
Bruno avaliou Ana
```

??? tip "Dica"
    Dentro do método, `getNome()` devolve o nome de quem avalia, e `p.getNome()`, o de quem é avaliado.

## Exercício 2 — Prever

**Objetivo.** Seguir a herança em dois níveis.

**Enunciado.** Sem executar, responda às três perguntas sobre o coordenador do `main`, que se chama Ana e tem CPF `333`.

1. Se você incluir `avaliadores.add(coordenador);`, o que o laço dos avaliadores imprime para ele, considerando o Exercício 1 feito?
2. Se uma sala nova for reservada com `reservar(coordenador)`, qual documento `imprimirSala()` mostra?
3. Uma sala é reservada pelo aluno e, em seguida, o coordenador tenta reservar a mesma sala. Qual documento fica guardado?

**Como conferir.** Escreva as três situações no `main` e execute. Considere resolvido quando você consegue dizer, para cada resposta, em qual classe está o método que foi executado.

??? tip "Dica"
    Quando um método é chamado, o Java procura primeiro na classe do objeto. Se não achar, sobe para a mãe, e depois para a mãe da mãe. Para a terceira pergunta, releia o `if` de `Sala.reservar`.

## Exercício 3 — Uma nova subclasse

**Objetivo.** Criar uma classe que herda de uma classe abstrata.

**Enunciado.** Crie a classe `Funcionario`, filha de `Pessoa`, com o atributo `setor`. Um funcionário se apresenta imprimindo o nome e o setor. Ele usa o CPF como documento e não faz avaliações.

**Como conferir.** No `main`, crie o funcionário Davi, com CPF `444`, do setor `Biblioteca`.

- Incluído na lista `pessoas`, ele faz o laço imprimir `Davi` e `Biblioteca`.
- Uma sala nova reservada por ele mostra o documento `444`.
- A linha `avaliadores.add(funcionario);` não compila. Leia a mensagem do compilador e explique-a com suas palavras. Depois, apague a linha.

??? tip "Dica"
    A classe precisa de pouca coisa: o atributo com getter e setter, e o método que `Pessoa` obriga toda filha a ter. O que ela **não** escreve também importa: sem `getDocumento`, vale o da mãe; sem `implements Avaliar`, ela não entra na lista de avaliadores.
