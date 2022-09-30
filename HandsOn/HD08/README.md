# **Referencias e Empréstimos(Borrowing)**

Uma referência, como um ponteiro, armazena o endereço de um objeto localizado em outro lugar na memória. Ao contrário de um ponteiro, uma referência depois de inicializada não pode ser feita para se referir a um objeto diferente ou definir como nulo.[1]

Veja, no exemplo aaixo, uma referência para um objeto como parâmetro.

```
fn main() {
    let s1 = String::from("texto");       // Define uma String
    let tamanho = calcula_tamanho(&s1);   // Vê o tamanho da String
    
    // Note que, sem a referência, não seria possível usar o param s1
    println!("O tamanho de '{}' é {}.", s1, tamanho);
}

fn calcula_tamanho(s: &String) -> usize {
    s.len()  // retorna o tamanho da String
}
```

Esses símbolos ``&`` indicam referências, e eles permitem que você se refira a algum valor **sem tomar posse dele**. note que passamos ``&s1`` para ``calcula_tamanho``, e na sua definição, temos ``&String`` em vez de apenas ``String``. Em outras palavras, temos os seguintes passos:

1) ``s1`` aponta para uma memória ``String`` alocada na *heap*.
2) ``tamanho`` utiliza a função ``calcula_tamanho``.
3) ``s``(variável da função ``calcula_tamanho``) recebe como referência ``s1`` (que está apontado para uma memória na *heap*).

**OBS 01:** A sintaxe ``&s1`` nos permite criar uma referência que se refere ao valor ``s1``, mas não o possui. Como ela não o possui, o valor a que ela aponta não será destruído quando a referência sair de escopo (visto no Hands-On anterior). Da mesma forma, a assinatura da função usa ``&String`` para indicar que o tipo do parâmetro ``s`` é uma referência. 

A imagem abaixo mostra isso.

![](/Imagens/HD08/Referencia.png)

Colocar **referências como parâmetros de funções é chamado de borrowing (do inglês, empréstimo)**. Assim como na vida real, se uma pessoa possui alguma coisa, você pode pegar emprestado dela. Quando você termina de usar, você deve devolver.

Algo muito importante a ser dito sobre referências é que ela tem características de imutabilidade. Em Rust, quando uma referência é passada para uma função, não há como alterar, apenas obter caracteísticas daquela entidade passada. O Exemplo abaixo irá mostrar:

```
fn main() {
    let s = String::from("texto");

    modifica(&s);
}

fn modifica(uma_string: &String) {
    uma_string.push_str(" longo");
}
```

a função ``modifica`` tenta mudar a string ``s``, mas como dito anteriormente, não há como mudar a referência. O compilador Rust identifica esse erro e sugere a modificação:

```
7 | fn modifica(uma_string: &String) {
  |                         ------- use `&mut String` here to make mutable
8 |     uma_string.push_str(" longo");
  |     ^^^^^^^^^^ cannot borrow as mutable
```

Com isso, entramos em outro ponto - nas referências mutáveis.

## Referências Mutáveis

Como o nome já diz, referências mutáveis podem mudar uma dada variável. São importantes em contextos em que voçê não deseja ter posse de uma variável, mas precisa realizar uma dada transformação.

O exemplo anterior pode ser refeito da seguinte maneira:

```
fn main() {
    // variável s é mutável
    let mut s = String::from("texto");
    modifica(&mut s);
}

    // pega uma referência mutável.
fn modifica(uma_string: &mut String) {
    uma_string.push_str(" longo");
}
```

Primeiro, temos que fazer com que ``s`` seja mutável. Depois, temos que criar uma referência mutável com ``&mut s`` e aceitar uma referência mutável com ``uma_string: &mut String``.

Apesar de ser extremamente útil para a manipulação via referência, devemos ter um cuidado especial com esse tema. Isso ocorre pois se passarmos mais de uma referência mutável de um dado em um determinado escopo, o código falhará. Veja o exemplo abaixo:

```
let mut s = String::from("texto");

let r1 = &mut s;   // primeira referência mutável de um dado mutável.
let r2 = &mut s;   // segunda referência mutável de um dado mutável.
```

Esta restrição permite a mutação, mas de uma forma bem controlada. O benefício de ter esta restrição é que o Rust previne *data races* (*data races* ocorre quando um *thread* acessa um objeto mutável enquanto outro thread está gravando nele) em tempo de compilação. Um data race é parecido com uma condição de corrida, e acontece quando esses três fatores ocorrem:

1º) Dois ou mais ponteiros acessam o mesmo dado ao mesmo tempo.
2º) o menos um dos ponteiros é usado para escrever sobre o dado.
3º) Não há nenhum mecanismo sendo usado para sincronizar o acesso ao dado.

*Data races* causam comportamento indefinido e pode ser difíceis de diagnosticar e corrigir quando você está tentando rastreá-los em tempo de execução. Rust previne este problema de acontecer porque não vai nem deixar compilar um código com *data races*!

___

### Um pouco de história.

O termo condição de corrida (*races*, em inglês), em termos de circuitos lógicos, já estava em uso em 1954, por exemplo, na tese de doutorado de David A. Huffman "A síntese de circuitos de comutação sequenciais". Uma condição de corrida pode ser difícil de reproduzir e depurar porque o **resultado final não é uma característica determinística e depende do tempo relativo entre os encadeamentos dos circuitos**. Problemas dessa natureza podem, portanto, desaparecer ao executar no modo de depuração, adicionando log extra ou anexando um depurador. Um bug que desaparece assim durante as tentativas de depuração é frequentemente chamado de "Heisenbug". Portanto, é melhor evitar condições de corrida por meio de um design de software cuidadoso. [2]

Na neurociência, como o trabalho feito em 2013 dos cientistas Robert Schmidt, Daniel K. Leventhal, Nicolas Mallet, Fujun Chen, e Joshua D. Berke e intitulado *Canceling actions involves a race between basal ganglia pathways* propõe, a noção básica de uma corrida entre processos Go(começar, em português) e Stop(parar, em português) que inicialmente evoluem em circuitos neurais separados e são estimulados ao mesmo tempo em ratinhos, fornecem evidências para múltiplos mecanismos basais de gânglios na inibição do conflituo emtre esses circuitos antagônicos.

___

um jeito de fazermos o código acima compilar é adicionando um novo escopo.

```
let mut s = String::from("texto");

{
    let r1 = &mut s;

} // aqui r1 sai de escopo, então já podemos criar uma nova referência sem
  // problema nenhum.

let r2 = &mut s;
```

Nós também não podemos ter uma referência mutável enquanto temos uma imutável. Usuários de uma referência imutável não esperam que os valores mudem de repente! Porém, múltiplas referências imutáveis são permitidas, pois ninguém que esteja apenas lendo os dados será capaz de afetar a leitura que está sendo feita em outra parte do código.

Um exemplo do erro:

```
let mut s = String::from("texto");

let r1 = &s; // sem problema
let r2 = &s; // sem problema
let r3 = &mut s; // PROBLEMA GRANDE
```

Mesmo que esses erros sejam frustrantes às vezes, lembre-se que é o compilador do Rust apontando um bug potencial antecipadamente (em tempo de compilação, em vez de execução), e mostrando exatamente onde está o problema, em vez de você ter que investigar por que algumas vezes os seus dados não são aquilo que você esperava que fosse.

## Em suma

1º) Em um dado momento, você pode ter um ou outro, mas não os dois:
    - Uma referência mutável.
    - Qualquer número de referências imutáveis.
2º) Referências devem ser válidas sempre.


### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️ [Clique Aqui](/HandsOn/HD09/README.md)

## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Referências (C++) - Microsoft Ignite. Disponível em: <https://learn.microsoft.com/pt-br/cpp/cpp/references-cpp?view=msvc-170>. Acesso em 28/09/2022.

[2] - Race Condition - Wikipédia. Disponível em: <https://en.wikipedia.org/wiki/Race_condition>. Acesso em 28/09/2022.

[3] - Referencias e Borrowing. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch04-02-references-and-borrowing.html>. Acesso em 28/09/2022.