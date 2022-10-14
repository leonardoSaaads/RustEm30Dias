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

### **Constantes**

Assim como as variáveis ​​imutáveis, as constantes são valores vinculados a um nome e não podem ser alterados, mas há algumas diferenças entre constantes e variáveis. Você pode declarar constantes usando a palavra-chave ``const`` em vez da palavra-chave ``let``, e o tipo do valor deve ser anotado. Ademais, outra diferença é que as constantes podem ser definidas apenas para uma expressão constante, não o resultado de um valor que só pode ser calculado em tempo de execução.

Varemos um exemplo abaixo:

```
const UMA_HORA_EM_SEGUNDOS: u32 = 60 * 60;
```
As constantes são válidas durante todo o tempo em que um programa é executado, dentro do escopo em que foram declarados. Essa propriedade torna as constantes úteis para valores em seu domínio de aplicativo que várias partes do programa podem precisar saber, como o número máximo de pontos que qualquer jogador de um jogo é permitido ganhar ou a velocidade da luz.

### **Shadowing (Sombreamento, em português)**

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

Cada valor no Rust é de um determinado tipo de dados, que informa ao Rust que tipo de dados está sendo especificado para que ele saiba como trabalhar com esses dados. Veremos dois subconjuntos de tipos de dados: escalar e composto. Tenha em mente que Rust é uma linguagem de tipagem estática, o que significa que ela deve conhecer os tipos de todas as variáveis ​​em tempo de compilação.

Veja um exemplo:

```
fn main() {
let guess: u32 = "42".parse().expect("Not a number!");
}
```

Se não adicionarmos a anotação do tipo ``:u32`` acima, o Rust exibirá o seguinte erro, o que significa que o compilador precisa de mais informações nossas para saber qual tipo queremos usar:

```
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^ consider giving `guess` a type

For more information about this error, try `rustc --explain E0282`.
error: could not compile `no_type_annotations` due to previous error
```

Veremos as notações para cada tipo de dado:

### **Escalares**

Um tipo escalar representa um único valor. Rust tem quatro tipos escalares primários: inteiros, números de ponto flutuante, booleanos e caracteres.

**INTEIROS**

Um inteiro é um número sem um componente fracionário. Existem dois tipos de inteiros no Rust. *Signed* e *Unsigned* refere-se à possibilidade do número ser negativo ou positivo - em outras palavras, se o número precisa de um sinal com ele (signed) ou se sempre for positivo pode ser representado sem um sinal (unsigned). Devido a essa característica, é utilizado ``i`` para representar singed e ``u`` para unsigned.

Cada variante com sinal pode armazenar números de $-(2^{n - 1})$ até $(2^{n - 1}) - 1$ incluso, sendo ``n`` o número de bits que varia de acordo com o uso. Então, um ``i8`` pode armazenar números de $-(2^7)$ até $2^7 - 1$, que é igual a $-128$ até $127$. Variantes sem sinal pode armazenar números de $0$ até $2^n - 1$, entao um ``u8`` pode armazenar números de $0$ até $2^8 - 1$, que é de $0$ até $255$.

Podemos pensar em termos das tabelas abaixo:

<table>
<thead>
  <tr>
    <th>Tamanho</th>
    <th>Signed</th>
    <th>Intervalo de Valores</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>8-bit</td>
    <td>i8</td>
    <td>-128 à 127</td>
  </tr>
  <tr>
    <td>16-bit</td>
    <td>i16</td>
    <td>-32768 à 32767</td>
  </tr>
  <tr>
    <td>32-bit</td>
    <td>i32</td>
    <td>-2147483648 à 2147483647</td>
  </tr>
  <tr>
    <td>64-bit</td>
    <td>i64</td>
    <td>-9223372036854775808 à 9223372036854775807</td>
  </tr>
  <tr>
    <td>128-bit</td>
    <td>i128</td>
    <td>-170141183460469231731687303715884105728 à 170141183460469231731687303715884105727</td>
  </tr>
  <tr>
    <td>arch</td>
    <td>isize</td>
    <td>isize</td>
  </tr>
</tbody>
</table>

Para Unsigned

<table>
<thead>
  <tr>
    <th>Tamanho</th>
    <th>Signed</th>
    <th>Intervalo de Valores</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>8-bit</td>
    <td>u8</td>
    <td>0 à 255</td>
  </tr>
  <tr>
    <td>16-bit</td>
    <td>u16</td>
    <td>0 à 65535</td>
  </tr>
  <tr>
    <td>32-bit</td>
    <td>u32</td>
    <td>0 à 4294967295</td>
  </tr>
  <tr>
    <td>64-bit</td>
    <td>u64</td>
    <td>0 à 18446744073709551615</td>
  </tr>
  <tr>
    <td>128-bit</td>
    <td>u128</td>
    <td>0 à 340282366920938463463374607431768211455</td>
  </tr>
  <tr>
    <td>arch</td>
    <td>usize</td>
    <td>usize</td>
  </tr>
</tbody>
</table>

Além disso, os tipos isize e usize dependem do computador em que seu programa está rodando: 64 bits se estiver em uma arquitetura de 64-bit e 32 bits se sua arquitetura for 32-bit. Um caso mais raro é para processadores 128 bits.

Também podemos utilizar um outro sistema de numeração, como octal ou hexadecimal:

<table><thead><tr><th>Números literais</th><th>Exemplo</th></tr></thead><tbody>
<tr><td>Decimal</td><td><code class="hljs">98_222</code></td></tr>
<tr><td>Hexadecimal</td><td><code class="hljs">0xff</code></td></tr>
<tr><td>Octal</td><td><code class="hljs">0o77</code></td></tr>
<tr><td>Binário</td><td><code class="hljs">0b1111_0000</code></td></tr>
<tr><td>Byte (<code class="hljs">u8</code> apenas)</td><td><code class="hljs">b'A'</code></td></tr>
</tbody></table>

As escolhas padrões do Rust geralmente são boas, e por padrão os inteiros são do tipo ``i32``: Esse tipo geralmente é o mais rápido, até em sistemas de 64-bit. A principal situação em que você usuaria ``isize`` ou ``usize`` é indexar algum tipo de coleção.

**PONTO FLUTUANTE**

Rust também tem dois tipos primitivos para números de ponto flutuante, que são números com casas decimais. Os pontos flutuantes do Rust são ``f32`` e ``f64``, que têm respectivamente os tamanhos de 32 e 64 bits.

Veja o seguinte exemplo:

```
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

Números em ponto flutuante são representados de acordo com o padrão IEEE-754. O tipo f32 é de precisão simples e f64 tem precisão dupla.

**BOOLEANOS**

Como em diversas linguagens de programação, o tipo Booleano em Rust possue dois valores possíveis: ``true`` e ``false``.

Um exemplo de código booleano:

```
fn main() {
    let t = true;

    let f: bool = false; // com tipo explícito
}
```

**CARACTERES**

Até agora trabalhamos apenas com números, mas Rust também suporta letras. O ``char`` é o tipo mais primitivo da linguaguem e o seguinte código mostra uma forma de utilizá-lo. (Observe que o char é específicado com **aspas simples**, é o oposto de strings, que usa **aspas duplas**.) 

OBS: O tipo ``char`` representa um valor unicode, o que quer dizer que você pode armazenar muito mais que apenas ASCII. Os valores Unicode vão de ``U+0000`` até ``U+D7FF`` e ``U+E000`` até ``U+10FFFF`` incluso.

Veja o exemplo abaixo:

```
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';
}
```

### **Compostos**

Tipos compostos podem agrupar vários valores em um único tipo. Rust tem dois tipos primitivos compostos: tuplas e vetores.

**TUPLAS**

Em geral, uma tupla é uma coleção não ordenada de itens. Criamos uma tupla escrevendo uma lista de valores separados por vírgula dentro de parênteses. Cada posição da tupla tem um tipo e os tipos dos elementos da tupla não necessitam serem iguais.

Um exemplo:

```
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

variável ``tup`` liga-se a tupla, porque uma tupla é considerada um único elemento composto. Além de desestruturar através da correspondência de padrões, podemos acessar diretamente um elemento da tupla usando um ponto ``(.)`` como índice do valor que queremos acessar. Por exemplo:

```
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let quinhentos = x.0;

    let seis_ponto_quatro = x.1;

    let um = x.2;
}
```

**MATRIZES**

Em geral, mma matriz é uma coleção ordenada de itens. Em outras palavras, uma outra maneira de ter uma coleção de vários valores é uma matriz. Diferentemente de uma tupla, **todos os elementos de uma matriz devem ser do mesmo tipo.**

Em Rust, os valores que entram numa matriz são escritos em uma lista separados por vírgulas dentro de colchetes:

```
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

Matrizes são úteis quando você deseja que seus dados sejam alocados em pilha do que no heap (Discutiremos posteriormente).

Um exemplo de quando você poderia necessitar usar uma matriz no lugar de um vetor é um programa em que você precisa saber o nome dos meses do ano. É improvável que tal programa deseje adicionar ou remover meses, então você pode usar uma matriz porque você sabe que sempre conterá 12 itens:

```
let meses = ["Janeiro", "Fevereiro", "Março", "Abril", "Maio", "Junho", "Julho",
              "Agosto", "Setembro", "Outubro", "Novembro", "Dezembro"];
    
```

Para acessar um determinado item em uma matriz, utilize um índice entre parenteses. Veja o exemplo abaixo:

```
fn main() {
    let meses = ["Janeiro", "Fevereiro", "Março", "Abril", "Maio", "Junho", "Julho",
              "Agosto", "Setembro", "Outubro", "Novembro", "Dezembro"];
    let primeiro = meses[0];
    let segundo = meses[1];
}
```
## Conversão de Data-Types (Exemplos: u8 -> f32 / f64 -> i32)

Em Rust, algo importante a ser ressaltado é que podemos realizar a mudança de um tipo em outro tipo. Usualmente, o compilado Rust não permite que diferentes tipos realizem operações. Em outras palavras, se desejarmos fazer uma divisão de um tipo ``i32`` por outro ``u8``, como exemplo, o compilador não permitirá. Por isso, podemos utilizar o ``as``(como, em português) para tratar essas transformações.

````
let a: u8 = 10;
let b: i32 = 100;
print!("{}", b/a);  // Não é possível fazer a divisão
```

Agora utilizando o ``as``:

```
fn main(){
    let a: u8 = 10;
    let b: i32 = 100;
    print!("{}", b/a as i32);  // Possivel usar a divisão
    // Rust lê o resultado de b/a como sendo uma variável i32 
}
```

Para entender um pouco mais sobre o tema, veja o vídeo abaixo:

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/t047Hseyj_k/0.jpg)](https://www.youtube.com/watch?v=t047Hseyj_k)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD04/README.md)

**REFERÊNCIAS BIBLIOGRÁFICAS**

[1] - Variables and Mutability. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html>. Acesso em 10/09/2022.

[2] - Data Types. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch03-02-data-types.html>. Acesso em 10/09/2022.