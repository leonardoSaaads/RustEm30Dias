# **Structs - parte 01**

Uma ``struct``, ou estrutura, é um tipo de dado personalizado que nos permite nomear e criar um conjunto de vários valores relacionados que compõem um grupo de dados. Os conceitos de struct e enum são os blocos necessários para a criação de novos tipos para o seu programa, para tirar o máximo proveito da verificação de tipo no tempo de compilação do Rust. Em outras palavras, podemos falar que seria criar a estrutura de um Objeto e usar seu proveito ao máximo. Alguns autores classificam Rust como sendo Programação Orientada a Objetos (POO) - devido a estruturas como ``Struct``, ``Traits`` e ``Impl``. Vamos mergulhar neste assunto. 

Para definir uma struct, digite a palavra-chave ``struct`` e o nome da struct. O nome da struct deve descrever o significado dos dados agrupados. Em seguida, dentro das chaves ``{}``, vamos definir os nomes e tipos dos dados, o que chamamos de campos. Neste exemplo baixo, mostraremos uma struct para armazenar informações sobre a conta de um usuário:

```
struct User {
    username: String,     // Nome de usuário e tipo de dado
    email: String,        // Email do usuário e tipo de dado
    active: bool,
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

