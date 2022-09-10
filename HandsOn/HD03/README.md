# **Variáveis, Mutabilidade e Data Types**

## Variáveis & Mutabilidade

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

![](/Imagens/HD03/MutableVariable1.png)

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

Assim como as variáveis ​​imutáveis, as constantes são valores vinculados a um nome e não podem ser alterados, mas há algumas diferenças entre constantes e variáveis. Você pode declarar constantes usando a palavra-chave ``const`` em vez da palavra-chave ``let``, e o tipo do valor deve ser anotado. Ademais, outra diferença é que as constantes podem ser definidas apenas para uma expressão constante, não o resultado de um valor que só pode ser calculado em tempo de execução.

Varemos um exemplo abaixo:

```
const UMA_HORA_EM_SEGUNDOS: u32 = 60 * 60;
```
As constantes são válidas durante todo o tempo em que um programa é executado, dentro do escopo em que foram declarados. Essa propriedade torna as constantes úteis para valores em seu domínio de aplicativo que várias partes do programa podem precisar saber, como o número máximo de pontos que qualquer jogador de um jogo é permitido ganhar ou a velocidade da luz.

**Shadowing (Sombreamento, em português)**

O processo de sombreamento é basicamente quando uma variável é sobreposta. Em outras palavras, é quando você declara uma nova variável com o mesmo nome de uma variável anterior. Dizemos que a primeira variável foi sombreada pela segunda - o que significa que a segunda variável é o que o compilador verá quando você usar o nome da variável.

Podemos sombrear uma variável usando o nome da mesma variável e repetindo o uso do ``let``. Fizemos algo parecido nos exemplos acima, mas iremos nos aprofundar nesse tópido. 

Veja o exemplo de Shadowing abaixo:

```
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}
```

Este programa primeiro vincula ``x`` a um valor de ``5``. Em seguida, ele cria uma nova variável ``x`` repetindo ``let x =``, pegando o valor original e adicionando ``1`` para que o valor de ``x`` seja ``6``. Então, dentro de um escopo interno criado com o curly colchetes, a terceira instrução ``let`` também sombreia ``x`` e cria uma nova variável, multiplicando o valor anterior por ``2`` para dar a ``x`` um valor de ``12``. Quando esse escopo termina, o sombreamento interno termina e ``x`` volta a ser ``6``.

![](/Imagens/HD03/ShadowingExemple.png)

A diferença entre mut e shadowing é que, como estamos criando efetivamente uma nova variável quando usamos a palavra-chave let novamente, podemos alterar o tipo do valor, mas reutilizar o mesmo nome. Além disso, se acidentalmente tentarmos reatribuir a esta variável sem usar o let, será exibido uma mensagem de erro.

Veja o exemplos abaixo:

```
fn main() {
    let spaces = "    ";
    let spaces = spaces.len();

    println!("O valor dos espaços é: {spaces}");
}
```

Com isso, podemos mudar o tipo da variável. 

![](/Imagens/HD03/ShadowingExempleType.png)

Isso já não seria possível utilizando-se ``mut``.

## Data Types

Cada valor no Rust é de um determinado tipo de dados, que informa ao Rust que tipo de dados está sendo especificado para que ele saiba como trabalhar com esses dados. Veremos dois subconjuntos de tipos de dados: escalar e composto.



**REFERÊNCIAS BIBLIOGRÁFICAS**

[1] - Variables and Mutability. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html>. Acesso em 10/09/2022.

[2] - Data Types. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch03-02-data-types.html>. Acesso em 10/09/2022.