# **Strings - UTF-8**

Novos programadores Rust geralmente ficam presos em strings por uma combinação de três razões: a propensão de Rust para expor possíveis erros, strings sendo uma estrutura de dados mais complicada do que muitos programadores acreditam, e UTF-8. Esses fatores se combinam de uma forma que pode parecer difícil quando você vem de outras linguagens de programação.

Discutimos strings no contexto de coleções porque as strings são implementadas como uma coleção de bytes, além de alguns métodos para fornecer funcionalidade útil quando esses bytes são interpretados como texto. Nesta seção, iremos falar sobre as operações em String que todo tipo de coleção tem, como criar, atualizar e ler.

## O que é uma ``String``?

O tipo String, que é fornecido pela biblioteca padrão do Rust em vez de codificado na linguagem principal, é um tipo de string codificado em UTF-8 que pode ser expandido, mutável e de propriedade. Quando os programadores se referem a “strings” no Rust, eles podem estar se referindo aos tipos String ou string slice ``&str``, não apenas a um desses tipos. Embora esta seção seja basicamente sobre String, ambos os tipos são muito usados ​​na biblioteca padrão do Rust, e tanto String quanto fatias de string são codificadas em UTF-8.

## Criando uma ``String``

Muitas das mesmas operações disponíveis com ``Vec<T>`` também estão disponíveis com String, porque String é realmente implementado como um wrapper(é um valor que se apropriou de parte do valor, de modo que o valor envolvido possa ser recuperado posteriormente) em torno de um vetor de bytes com algumas garantias, restrições e recursos extras. Um exemplo de função que funciona da mesma forma com ``Vec<T>`` e String é a nova função para criar uma instância, como mostrado abaixo:

```
let mut s = String::new();
```

Essa linha cria uma nova string vazia chamada ``s``, na qual podemos carregar os dados. Frequentemente, teremos alguns dados iniciais com os quais queremos iniciar a string. Para isso, utilizamos o método ``to_string``, que está disponível em qualquer tipo que implemente o traço Display, como fazem os literais string.

```
let dados = "conteúdo inicial";
let s = datos.to_string();
// o método também funciona em um literal diretamente:
let s = "conteúdo inicial".to_string();
```

Este código cria uma string contendo o ``conteúdo inicial``. Também podemos usar a função ``String::from`` para criar uma String a partir de uma string literal. O código abaixo é semelhante ao que usa to_string.

```
let s = String::from("initial contents");
```

Como as strings são usadas para muitas coisas, podemos usar muitas APIs genéricas diferentes para strings, o que nos fornece muitas opções. Algumas delas podem parecer redundantes, mas todas têm seu lugar! Neste caso, ``String::from`` e ``to_string`` fazem a mesma coisa, então qual você escolhe é uma questão de estilo e legibilidade.

```
fn main() {
    let hello = String::from("السلام عليكم");
    let hello = String::from("Dobrý den");
    let hello = String::from("Hello");
    let hello = String::from("שָׁלוֹם");
    let hello = String::from("नमस्ते");
    let hello = String::from("こんにちは");
    let hello = "안녕하세요".to_string()
    let hello = "你好".to_string()
    let hello = "Olá".to_string()
    let hello = "Здравствуйте".to_string()
    let hello = "Hola".to_string()
}
```

## O update de uma ``String``.

Uma ``String`` pode aumentar de tamanho e seu conteúdo pode mudar, assim como o conteúdo de um ``Vec<T>``, se você inserir mais dados nele. Além disso, você pode usar convenientemente o operador ``+`` ou o macro ``format!`` para concatenar valores String.


### **Anexando a uma String com push_str e push**

Podemos aumentar uma String usando o método push_str para anexar uma fatia de string.

```
let mut s = String::from("foo");
s.push_str("bar");
```

Após essas duas linhas, ``s`` conterá ``foobar``. O método ``push_str`` pega uma fatia de string porque não queremos necessariamente nos apropriar(ownership) do parâmetro. Neste exemplo abaixo, queremos poder usar ``s2`` depois de anexar seu conteúdo a ``s1``.

```
let mut s1 = String::from("foo");
    let s2 = "bar";
    s1.push_str(s2);
    println!("s2 is {s2}");
```

Se o método ``push_str`` tomasse posse de ``s2``, não poderíamos imprimir seu valor na última linha. No entanto, esse código funciona como esperávamos!

O método ``push`` pega um único caractere como parâmetro e o adiciona ao String. Veja o exemplo abaixo:

```
\\ bit transmitido, mas falta o identificador.
let mut transmitido = String::from("001001010010101")
transmitido.push("1");  // adiciona identificador.
// O resultado fica: 0010010100101011
```

### Concatenação com o Operador ``+`` ou o ``format!`` Macro

Frequentemente, você desejará combinar duas strings existentes. Uma maneira de fazer isso é usar o operador ``+``, conforme mostrado abaixo:

```
let s1 = String::from("Isso é");
    let s2 = String::from(" um teste!");
    let s3 = s1 + &s2; // s1 foi movida e não poderá mais ser utilizada.
```

Note que com esse método, pelo menos uma variável deixará de existir. Caso você tente Borrowing, isso também não dará certo. Veja o erro abaixo:

```
let s1 = String::from("Isso é ");
let s2 = String::from("um teste.");
let s3 = &s1 + &s2;
print!("{}", s3);

error[E0369]: cannot add `&String` to `&String`
  |
4 |     let s3 = &s1 + &s2;
  |              --- ^ --- &String
  |              |   |
  |              |   `+` cannot be used to concatenate two `&str` strings
  |              &String
  |
  = note: string concatenation requires an owned `String` on the left
help: remove the borrow to obtain an owned `String`
  |
4 -     let s3 = &s1 + &s2;
4 +     let s3 = s1 + &s2;
  |

For more information about this error, try `rustc --explain E0369`.
```

A razão pela qual s1 não é mais válido após a adição, e a razão pela qual usamos uma referência a s2, tem a ver com a assinatura do método que é chamado quando usamos o operador +. O operador + usa o método add, cuja assinatura é mais ou menos assim:

```
fn add(self, s: &str) -> String {
        └──> Utiliza pelo menos um termo sem empréstimo (Borrowing).
```

Na biblioteca padrão, você verá ``add`` definido usando genéricos e tipos associados. Aqui, substituímos tipos concretos, que é o que acontece quando chamamos esse método com valores String. Discutiremos os genéricos nos capítulos à frente. Essa assinatura nos dá as pistas de que precisamos para entender as partes complicadas do operador ``+``.

Primeiro, ``s2`` tem um ``&``, o que significa que estamos adicionando uma referência da segunda string à primeira string. Isso ocorre por causa do parâmetro ``s`` na função add: só podemos adicionar um &str a uma String; não podemos adicionar dois valores String juntos. Mas espere — o tipo de &s2 é &String, não &str, conforme especificado no segundo parâmetro a ser adicionado. Mas então por que raios isso funciona? Então... A razão pela qual podemos usar &s2 na chamada para adicionar é que o compilador pode forçar o argumento ``&String`` em um tipo ``&str``.

Veja outro exemplo:

```
let s1 = String::from("Oi");
let s2 = String::from("Olá");
let s3 = String::from("Hello!");
let s = s1 + "-" + &s2 + "-" + &s3;

// Isso fica: Oi-Olá-Hello!
```

Essa é uma saída boa, mas para Strings mais compridas usamos o macro ``format!`` (lembre-se disso, é muito importante!!!). 

```
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");
let s = format!("{s1}-{s2}-{s3}");

// Isso ficará tic-tac-toe
```

A macro ``format!`` funciona como ``println!``, mas ao invés de imprimir a saída na tela, ela retorna uma String com o conteúdo. A versão do código usando o ``format!`` é muito mais fácil de ler, e o código gerado pelo ``format!`` macro **usa referências para que esta chamada não se aproprie de nenhum de seus parâmetros**.

### ** A Indexação e acesso a uma ``char``.

Em várias letras podemos acessar diretamente uma variável ``char``. Um exemplo disso é a linguagem de programação Pyton, onde você consegue acessar uma determinada letra através dos colchetes. Como exemplo, se tivermos uma variavel ``s = 'isso é uma string em python'``, podemos fazer a operação ``s[0]`` e isso nos retornaria ``i``. Contudo, em Rust isso não é possível justamente pelo comportamento de armazenamento na memória e seu relacionamento com o compilador.

Uma String é um *wrapper* sobre um ``Vec<u8>``. Vejamos algumas de nossas strings de exemplo UTF-8 devidamente codificadas. Primeiro, este:

```
fn main(){
let len = String::from("Telecomunicacoes eh show").len();
print!("{}", len)}
```

Neste caso, ``len`` terá valor de 24, o que significa que o ``Vec`` armazena a string ”Hola” tem 24 bytes de comprimento: cada uma dessas letras leva um byte quando codificado em UTF-8. Vamos a outro exemplo

```
fn main(){
let len = String::from("電信是作秀").len();
print!("{}", len)
}
```

Entretanto, neste caso, ``len`` terá valor de 15, o que significa que o ``Vec`` armazena a string ”電信是作秀” tem 15 bytes de comprimento. Como foi monstrado acima, cada uma dessas "letras" não ocupa necessariamente uma relação de uma letra para um byte. Isso fez com que a linguagem tenha três formas de enxegar as Strings da perspectiva do Rust: como bytes, valores escalares e clusters de grafemas (a coisa mais próxima do que as pessoas chamariam letras).

Se olharmos para a palavra Hindi “नमस्ते” escrita na escrita Devanagari, é em última instância, armazenada como um Vec de valores u8 que se parece com isto:

**Primeira visão - Bytes**
```
[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164,
224, 165, 135]
```

 Se olharmos para eles como valores escalares Unicode, que são o tipo char de Rust, aqueles bytes se parecem com isto:

 **Segundo Tipo - Char**
```
['न', 'म', 'स', '्', 'त', 'े']
```

Eles são diacríticos que não fazem sentido por conta própria. Finalmente, se olharmos para eles como clusters de grafemas, teríamos o que uma pessoa chamaria as quatro letras que compõem esta palavra:

**Terceiro Tipo - Clusters**
```
["न", "म", "स्", "ते"]
```

Rust fornece diferentes maneiras de interpretar os dados de uma string bruta que os computadores armazenem para que cada programa possa escolher a interpretação que necessite, não importa em que idioma humano os dados estão.

Especialmente em teoria da informação, essa escolha da linguagem se torna interessante - tendo em vista que os valores podem corresponder a amontoados de informações.

![](/Imagens/HD19/ascii-codes.gif)

Devido a esses motivos, existem várias formas de iterar sobre Strings, Veremos duas formas aqui e no próximo capitulo abordaremos ``Hash Maps``.

**Operando sobre ``chars``**

```
for c in "नमस्ते".chars() {
    println!("{}", c);
}

// deve imprimir algo como:
//न
//म
//स
//्
//त
//े
```

**Operando sobre ``bytes``**
```
for c in "नमस्ते".bytes() {
    println!("{}", c);
}

// deve imprimir algo como:
// 224
// 164
// 168
// ...

```

Obter clusters de grafemas de strings é complexo, então esta funcionalidade não é fornecida pela biblioteca padrão (o que é meio bizarro hahaha).

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/Mcuqzx3rBWc/0.jpg)](https://www.youtube.com/watch?v=Mcuqzx3rBWc)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD20/README.md)

## REFERÊNCIAS BIBLIOGRÀFICAS

[1] - Crate wrapper - <https://docs.rs/wrapper/latest/wrapper/>. Acesso em 21 de novembro de 2022.

[2] - Storing UTF-8 Encoded Text with Strings. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch08-02-strings.html>. Acesso em 28/12/2022.

Imagem - https://naveenr.net/content/images/2017/03/ascii-codes.gif