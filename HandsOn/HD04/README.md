# **Funções**

Funções são difundidas em códigos em Rust. Você já viu uma das mais importantes funções da linguagem: a função ``main``, que é o ponto de entrada de diversos programas. Você também já viu a notação ``fn``, que permite você declarar uma nova função.

Códigos em Rust usam, por convenção, o estilo *snake case* para definição nomes de função e de variáveis. Foi falado anteriormente no **Hands-On 2** algo semelhante. Uma representação visual do *Snake Case* seria a seguinte:

    *camelCase*   (ex: someVariable)
    *snake_case*  (ex: some_variable)
    *PascalCase*  (ex: SomeVariable)

Em python, por exemplo, existe o PEP8, isto é, um guia de estilo para códigos em python. Em Rust e em python, a convenção é utilizar o modelo snake. O próprio modelo PEP8 cita 

"Function names should be lowercase, with words separated by underscores as necessary to improve readability." [1]

Um exemplo de função que segue o modelo: 

```
fn main() {
    println!("Olá, mundo!");

    outra_funcao();
}

fn outra_funcao() {
    println!("Outra função.");
}
```

![](/Imagens/HD04/FuncaoHelloWorld.png)

As linhas são executadas na ordem em que aparecem na função ``main``. Primeiro, a mensagem "Olá, mundo!" é exibida, e então ``outra_funcao`` é chamada e exibida a mensagem.

As definições de funções em Rust iniciam com ``fn`` e tem um par de parênteses depois do nome da função. As chaves dizem ao compilador onde o corpo da função começa e termina. Uma coisa muito importante a se observar é que a linguaguem Rust não se importa onde você definiu suas funções, apenas que elas foram definidas em algum lugar do seu código.

**PARÂMETROS DE FUNÇÕES**

Funções podem ser definidas tendo parâmetros, que são variáveis especiais que fazem parte da assinatura da função. Quando uma função tem parâmetros, você pode fornecer tipos específicos para esses parâmetros.

A seguinte versão (reescrita) da ``outra_funcao`` mostra como os parâmetros aparecem no Rust:

```
fn main() {
    outra_funcao(5);
}

fn outra_funcao(x: u8) {
    println!("O valor de x é: {}", x);
}
```
Nas assinaturas de função, você deve declarar o tipo de cada parâmetro. Essa é decisão deliberada no design do Rust: exigir anotações de tipo na definição da função, significa que o compilador quase nunca precisará que as use em outro lugar do código para especificar o que você quer.

Quando você precisa que uma função tenha vários parâmetros, separe as declarações de parâmetros com vírgula, como a seguir:

```
fn main() {
    outra_funcao(155, 254);
}

fn outra_funcao(x: u8, y: u8) {
    println!("O valor de x é: {}", x);
    println!("O valor de y é: {}", y);
}
```

Este exemplo cria uma função com dois parâmetros, ambos com o tipo ``u8``. Então a função exibe os valores de ambos os parâmetros.

Quando executamos a função: 

![](/Imagens/HD04/DuasVariaveis.png)

**CORPO DAS FUNÇÕES**

O corpo das funções é justamente o local de desenvolvimento e ações. Em outras palavras, corpos de função são constituídos por uma série de declarações que terminam, opcionalmente, em uma expressão. Veja uma notação abaixo de como seria o corpo da função:

```
declaraçao-de-função nome-da-função(parâmetros){
declarações e senteças (isto é, corpo da função)
}
```

**DECLARAÇÕES E EXPRESSÕES**

Declarações são instruções que executam alguma ação e não retornam um resultado. E expressões retornam um resultado. Um exemplo de declaração é quando usamos ``let`` para atribuirmos um determinado valor para uma variável. Expressões, por sua vez,  avaliam algo e compõem a maior parte do código que você escreverá em Rust. A chamada de função é uma expressão. Chamar uma macro é uma expressão. O bloco que vamos usar para criar um novo escopo, ``{}``, é uma expressão.

Exemplo de Declaração:

```
fn main() {
    let x = 5;

    println!("O valor de x é: {}", x);
}
```

Outro exemplo - declaração e expressão:

```
fn main() {
    // Declarações
    let carros_maria: u8 = 2; // total de carros da Maria
    let carros_junior = 3;    // total de carros do Júnior
    let carros_carol = 1;     // total de carros da Carol
    let carros_leo = 7;       // total de carros do Leonardo
    
    // Expressão que calcula o total de carros.
    {
        let carros_totais = carros_maria + carros_junior
                            + carros_carol + carros_leo;
        
        println!("O total de carros é: {}", carros_totais);
    }
}
```

**FUNÇÕES COM VALOR DE RETORNO**

Funções podem retornar valores para o código que os chama. Não nomeamos valores de retorno, mas declaramos o tipo deles depois de uma seta (``->``). Em Rust, o valor de retorno da função é sinônimo do valor da expressão final no bloco do corpo de uma função. Você pode retornar uma função usando a palavra-chave ``return`` e especificando um valor, mas a maioria das funções retorna a última expressão implicitamente.

Veja o exemplo abaixo:

```
fn vinte_e_nove_mil() -> u16 {
    29000  // Necessário não colocar ";"
}

fn main() {
    let x = vinte_e_nove_mil();

    println!("O valor de x é: {}", x);
}
```

Você poderia, semelhantemente, fzer do seguinte modo:

```
fn vinte_e_nove_mil() -> u16 {
    return 29000; // Necessário colocar ";"
}

fn main() {
    let x = vinte_e_nove_mil();

    println!("O valor de x é: {}", x);
}
```

O 29000 é o valor de retorno da função, e é por isso que o tipo de retorno é u16. Existem dois bits importantes: primeiro, a linha ``let x = vinte_e_nove_mil();`` mostra que estamos usando o valor de retorno de uma função para inicializar uma variável. Porque a função ``vinte_e_nove_mil`` retorna um ``29000``, essa linha é a mesma que a seguinte:

```
let x = 29000;
```

Outro exemplo de código com retorno:

```
// Calcula o imposto DE 5% sobre um determinado bem.
fn imposto(valor: f32) -> f32 {
    0.05 * valor
}

// Calcula o valor do imposto em alguns itens.
fn main() {
    let celular: f32 = 2300.0;     // Valor do celular de R$ 2300
    let computador: f32 = 4299.0;  // Valor do computador de R$ 4299 
    let botas = 290.0;             // Valor das botas de R$ 290
    
    println!("O valor de imposto no celular é: {}", imposto(celular));
    println!("O valor de imposto no computador é: {}", imposto(computador));
    println!("O valor de imposto nas botas é: {}", imposto(botas));
}
```

**OBSERVAÇÃO IMPORTANTE!!!**: Quando colocamos um ponto e vírgula no final da variável que será retornada, recebemos o erro de **tipos incompatíveis**. Acontece que o ``;`` inviabiliza o retorno de um dado, fazendo com que haja uma incompatibilidade da saida esperada com a ausência de saída e, consequentemente, provocando o erro de tipos incompatíveis. 

Veja o exemplo de quando mudamos a função ``imposto`` do exemplo acima:

```
// Calcula o imposto DE 5% sobre um determinado bem.
fn imposto(valor: f32) -> f32 {
    0.05 * valor;   // ESPERADO UM ERRO DE INCOMPATIBILIDADE
}
```

A saída do programa resulta em:

```
fn imposto(valor: f32) -> f32 {
  |    -------                ^^^ expected `f32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
3 |     0.05 * valor;   // ESPERADO UM ERRO DE INCOMPATIBILIDADE
  |                 - help: remove this semicolon
```

O prórpio compilador nota que houve um erro!

Veja uma função interessante do livro ["Rust by Examples"](https://doc.rust-lang.org/rust-by-example/fn.html). Apesar de não termos visto operadores, esse exemplo mostra como a linguagem pode operar em diferentes cenários.

```
// Unlike C/C++, there's no restriction on the order of function definitions
fn main() {
    // We can use this function here, and define it somewhere later
    fizzbuzz_to(100);
}

// Function that returns a boolean value
fn is_divisible_by(lhs: u32, rhs: u32) -> bool {
    // Corner case, early return
    if rhs == 0 {
        return false;
    }

    // This is an expression, the `return` keyword is not necessary here
    lhs % rhs == 0
}

// Functions that "don't" return a value, actually return the unit type `()`
fn fizzbuzz(n: u32) -> () {
    if is_divisible_by(n, 15) {
        println!("fizzbuzz");
    } else if is_divisible_by(n, 3) {
        println!("fizz");
    } else if is_divisible_by(n, 5) {
        println!("buzz");
    } else {
        println!("{}", n);
    }
}

// When a function returns `()`, the return type can be omitted from the
// signature
fn fizzbuzz_to(n: u32) {
    for n in 1..=n {
        fizzbuzz(n);
    }
}
```

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️ [Clique Aqui](/HandsOn/HD05/README.md)

## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Disponível em: <https://peps.python.org/pep-0008/#function-and-variable-names>. Acesso em 12/09/2022.

[2] - Functions. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch03-03-how-functions-work.html>. Acesso em 15/09/2022.

[3] - Functions. The Rust By Exemples  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/rust-by-example/fn.html>. Acesso em 15/09/2022.