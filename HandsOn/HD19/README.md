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


## REFERÊNCIAS BIBLIOGRÀFICAS

[1] - Crate wrapper - <https://docs.rs/wrapper/latest/wrapper/>. Acesso em 21 de novembro de 2022.