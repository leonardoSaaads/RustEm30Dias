# **Structs - Parte 01**

Uma ``struct``, ou estrutura, é um tipo de dado personalizado que nos permite nomear e criar um conjunto de vários valores relacionados que compõem um grupo de dados. Os conceitos de struct e enum são os blocos necessários para a criação de novos tipos para o seu programa, para tirar o máximo proveito da verificação de tipo no tempo de compilação do Rust. Em outras palavras, podemos falar que seria criar a estrutura de um Objeto e usar seu proveito ao máximo. Alguns autores classificam Rust como sendo Programação Orientada a Objetos (POO) - devido a estruturas como ``Struct``, ``Traits`` e ``Impl``. Vamos mergulhar neste assunto. 

Para definir uma struct, digite a palavra-chave ``struct`` e o nome da struct. O nome da struct deve descrever o significado dos dados agrupados. Em seguida, dentro das chaves ``{}``, vamos definir os nomes e tipos dos dados, o que chamamos de campos. Neste exemplo baixo, mostraremos uma struct para armazenar informações sobre a conta de um usuário:

```
struct User {
    username: String,     // Nome de usuário e tipo de dado
    email: String,        // Email do usuário e tipo de dado
    active: bool,         // Status de ativação e tipo de dado
}
```

Para usar uma struct depois de a definirmos, criamos uma instância dessa struct, especificando valores para cada um dos campos. Para criarmos uma instância, indicando o nome da struct e depois os valores entre as chavetas, adicionando os pares campo:valor onde as chaves são os nomes dos campos e os valores são os dados que deseja armazenar nesses campos. Por exemplo, podemos declarar um usuário específico:

```
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn main() {
let user1 = User {
    email: String::from("alguem@exemplo.com"),
    username: String::from("algumnome123"),
    active: true,
    sign_in_count: 1,
};
}
```

Para obter um valor de uma ``struct`` que você criou, podemos utilizar a notação de ponto. Como definimos a formatação ``struct`` e criamos o primeiro usuário - denominado como ``user1`` - podemos requisitar uma de suas instancias. Se quiséssemos, como exemplo, o endereço de e-mail do usuário, podemos usar ``user1.email`` sempre que queremos usar este valor.

```
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn main() {
let user1 = User {
    email: String::from("alguem@exemplo.com"),
    username: String::from("algumnome123"),
    active: true,
    sign_in_count: 1,
};

println!("{}", user1.email)  \\ Acessa essa instância.
}
```

Para mudarmos essa instância, basta realizarmos algo semelhante ao acesso da instância. A diferença se dá na atribuição de um novo valor. Para alterar um valor em uma struct, se a instância é mutável, podemos usar a notação de ponto e atribuir a um campo específico.

Veja o exemplo abaixo:

```
let mut user1 = User {
    email: String::from("alguem@exemplo.com"),
    username: String::from("algumnome123"),
    active: true,
    sign_in_count: 1,
};

user1.email = String::from("outroemail@exemplo.com");
```

## *field init shorthand* - inicialização abreviada do campo

Se você tiver as variáveis com os mesmos nomes dos campos da struct, você pode usar o *field init shorthand* (inicialização abreviada do campo). Isto pode fazer com que as funções que criam novas instâncias de structs mais concisos.

Ao escrever o código de inicialização para uma estrutura de dados, os nomes dos campos da estrutura geralmente se tornam os nomes mais diretos a serem usados ​​também para seus valores iniciais. No final de tal função de inicialização, o inicializador conterá muitos padrões de nomes de campo repetidos como valores de campo: ``.field: field, field2: field2, field3: field3``

Essa repetição dos nomes de campo torna menos ergonômico declarar e inicializar campos individuais separadamente e torna tentador incorporar código complexo diretamente no inicializador para evitar repetição.

O Rust já permite uma sintaxe semelhante para desestruturação em correspondências de padrões: uma correspondência de padrões pode ser usada para combinar e inserir valores com os mesmos nomes. Este RFC introduz a sintaxe simétrica para inicializadores. ``SomeStruct { field1, field2 } => ...`` para o *match* ``field1`` e ``field2`` em valores com o menos nome. Um pequeno exemplo é código abaixo:

```
let exemple = SomeStruct{field1, field2: expression, field3 };
```

Vamos usaer um modelo visto anteriormente para deixar a forma mais didática. Iremos utilizar o mesmo modelo de usuários determinado anteriormente. Em uma primeira análise, você pode pensar que para criarmos uma função que controi um novo usuário para a rede é feita da seguinte forma:

```
fn main() {
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn build_user(email: String, username: String) -> User {
    User {
        email: email,
        username: username,
        active: true,
        sign_in_count: 1,
    }
}
}
```

mas note que o campo ``username`` e o campo ``email`` possuem como entrada exatamente os mesmos nomes de campo. Fica algo repetitivo termos que ficar utilizando entradas que possuem o mesmo nome. Outro exemplo seria contruir um ``Struct`` para determinar a área de um triângulo, onde as entradas são ``lado_1`` e ``lado_2``, e utilizássemos uma função geradora que recebe ``lado_1: lado_1`` e ``lado_2: lado_2``. Devido a isso, Rust possui uma sintaxe abreviada: para fazer casos como esse mais curtos de escrever, especialmente quando structs têm muitos campos.

Veja o exemplo com a sintaxe abreviada: 

```
fn build_user(email: String, username: String) -> User {
    User {
        email,           // Utiliza Diretamente
        username,        // Utiliza Diretamente
        active: true,   
        sign_in_count: 1,
    }
}
```

## *Struct Update Syntax* - Sintaxe de Atualização da Struct

É frequentemente útil criar uma nova instância a partir de uma antiga instância, usando a maioria dos valores da antiga instância mas mudando alguns. Um exemplo disso é quando um usuário só pode entrar em uma rede se for convidade por outro usuário pertencente aquela rede. Nesse exemplo, o status do usuário ingressante fica atrelado ao usuário que o chamou. 

O código abaixo mostra um exemplo da criação de uma nova instância do ``user1`` em ``user2`` através da definição dos valores de ``e-mail`` e ``username`` mas usando os mesmos valores para o resto dos campos do exemplo ``user1`` que criamos:

```
fn main() {
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

let user1 = User {
    email: String::from("alguem@exemplo.com"),
    username: String::from("algumnome123"),
    active: true,
    sign_in_count: 1,
};

let user2 = User {
    email: String::from("outro@exemplo.com"),
    username: String::from("outronome456"),
    active: user1.active,
    sign_in_count: user1.sign_in_count,
};
}
```

A sintaxe de atualização struct usa ``..`` para especificar que os campos restantes não explicitamente configurados devem ter o mesmo valor que os campos na determinada instância.

Veja o exemplo do código acima utilizando-se sintaxe de atualização struct:

```
fn main() {
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1   // Dados restantes preenchidos com dados do User1
};
}
```

## Structs-Tuplas

Podemos também definir structs que parecem semelhantes a tuplas, chamadas tuple structs, que têm o significado que o nome struct fornece, mas não têm os nomes associados com os seus campos, apenas os tipos dos campos. A definição de uma struct-tupla, ainda começa com a palavra-chave struct e o nome da struct, que é seguida pelos tipos na tupla. 

Um exemplo: 

```

fn main() {
struct Color(i32, i32, i32);  //  R,G,B
struct Point(i32, i32, i32);  //  X,Y,Z

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
}
```

Cada struct que definimos é o seu próprio tipo, embora os campos dentro do struct tenham os mesmos tipos. No geral as struct-tuplas comportam-se como instâncias de tuplas.

Para entender um pouco mais sobre o tema, veja o vídeo abaixo:

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/n3bPhdiJm9I/0.jpg)](https://www.youtube.com/watch?v=n3bPhdiJm9I)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️ [Clique Aqui](/HandsOn/HD11/README.md)

## REFERÊNCIAS BIBLIOGRÁFICAS:

[1] - Field-init-shorthand - The Rust RFC Book . Disponível em: <https://rust-lang.github.io/rfcs/1682-field-init-shorthand.html>. Acesso em 11 de outubro de 2022.


[2] - Definindo e Instanciando Structs. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch05-01-defining-structs.html>. Acesso em 11/10/2022.