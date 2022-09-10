# **Variáveis, Mutabilidade e Data Types**

Uma coisa muito importante a se resaltar em Rust é que suas variáveis são imutáveis por *default*. Devemos sempre lembrar que Rust é uma linguagem estática - que garante maior segurnaça e confiabilidade aos seus códigos. Entretante, No entanto, você ainda tem a opção de tornar suas variáveis ​​mutáveis. Vamos explorar como e por que o Rust o incentiva a favorecer a imutabilidade.

Abra um arquivo e coloque o seguinte código abaixo:

```
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

Você verá que a saida do código será semelhante a imagem abaixo:

![](/Imagens/HD03/MutableVariable.png)

Este exemplo mostra como o compilador o ajuda a encontrar erros em seus programas. Os erros do compilador podem ser frustrantes, mas na verdade eles apenas significam que seu programa ainda não está fazendo com segurança o que você deseja.

A mensagem de erro indica que a causa do erro é que você não pode atribuir duas vezes à variável imutável ``x``, porque você tentou atribuir um segundo valor à variável imutável x.

É importante que recebamos erros em tempo de compilação quando tentamos alterar um valor designado como imutável, pois essa mesma situação pode levar a erros. Se uma parte de nosso código opera na suposição de que um valor nunca mudará e outra parte de nosso código altera esse valor, é possível que a primeira parte do código não faça o que foi projetada para fazer. A causa desse tipo de bug pode ser difícil de rastrear após o fato, especialmente quando a segunda parte do código altera o valor apenas algumas vezes. O compilador Rust garante que quando você declara um valor não mudará, ele realmente não mudará, então você não precisa acompanhá-lo por conta própria. Seu código é, portanto, mais fácil de raciocinar.

Mas a mutabilidade pode ser muito útil e pode tornar o código mais conveniente para escrever. Embora as variáveis ​​sejam imutáveis ​​por padrão, você pode torná-las mutáveis ​​adicionando ``mut`` na frente do nome da variável como você fez no Capítulo 2. Adicionar ``mut`` também transmite a intenção para futuros leitores do código, indicando que outras partes do código serão alteradas o valor desta variável.

Quando mudamos o código muda para:

```
fn main() {
    let mut x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

O código compila corretamente. Veja a imagem abaixo:

![](/Imagens/HD03/MutableVariable2.png)

Em última análise, decidir usar ou não a mutabilidade depende de você e depende do que você acha que é mais claro nessa situação específica.

**Constantes**

Assim como as variáveis ​​imutáveis, as constantes são valores vinculados a um nome e não podem ser alterados, mas há algumas diferenças entre constantes e variáveis.



**REFERÊNCIAS BIBLIOGRÁFICAS**

[1] - Variables and Mutability. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html>. Acesso em 10/09/2022.