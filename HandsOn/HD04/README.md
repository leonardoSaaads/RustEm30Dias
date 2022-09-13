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



## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Disponível em: <https://peps.python.org/pep-0008/#function-and-variable-names>. Acesso em 12/09/2022.