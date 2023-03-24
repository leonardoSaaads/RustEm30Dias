# **Closures: Anonymous Functions**

Os **Closures** da linguagem de programação Rust são funções anônimas que você pode salvar em uma variável ou passar como argumentos para outras funções. Você pode criar **Closures** em um lugar e, em seguida, chamar **Closures** de outro lugar para avaliá-lo em um contexto diferente. Diferente das funções, os **Closures** podem capturar valores do escopo em que são definidos. Demonstraremos como esses recursos de **Closures** permitem a reutilização e o comportamento do código personalização.

Em suma, Eles são semelhantes às funções, mas têm algumas diferenças importantes. Por exemplo, não é necessário anotar os tipos dos parâmetros ou o valor de retorno ao definir um closure. Um closure também pode ser atribuído a uma variável e chamado diretamente. A definição do closure é colocada entre pipes verticais ( | ). Veja uma rápida exibição:

```
fn main() {
    let add_one = |x| x + 1;
    let five = add_one(4);
    println!("{}", five);
}
```

## Capturando o ambiente com Closures

Vamos examinar como podemos usar closures para capturar valores do ambiente em que são definidos para uso posterior. Aqui está o cenário: De vez em quando, nossa empresa de camisetas dá uma camiseta exclusiva e de edição limitada a alguém em nossa lista de e-mails como promoção. As pessoas na lista de e-mails podem opcionalmente adicionar sua cor favorita ao seu perfil. Se a pessoa escolhida para receber uma camiseta grátis tiver definido sua cor favorita, ela receberá uma camiseta dessa cor. Se a pessoa não tiver especificado uma cor favorita, ela receberá a cor que a empresa tem mais no momento.

Existem muitas maneiras de implementar isso. Para este exemplo, vamos usar um ``enum`` chamado ``ShirtColor`` que tem as variantes ``Red`` e ``Blue`` (limitando o número de cores disponíveis por simplicidade). Representamos o estoque da empresa com uma estrutura chamada ``Inventory`` que tem um campo chamado ``shirts`` que contém um ``Vec<ShirtColor>`` representando as cores das camisas atualmente em estoque. O método ``giveaway``, definido em ``Inventory``, obtém a preferência opcional de cor da camisa do vencedor da promoção e retorna a cor da camisa que a pessoa receberá.

Aqui está um exemplo semelhante ao cenário descrito acima:

```
#[derive(Debug, PartialEq, Copy, Clone)]
enum ShirtColor {
    Red,
    Blue,
}

struct Inventory {
    shirts: Vec<ShirtColor>,
}

impl Inventory {
    fn giveaway(&self, user_preference: Option<ShirtColor>) -> ShirtColor {
        user_preference.unwrap_or_else(|| self.most_stocked())
    }

    fn most_stocked(&self) -> ShirtColor {
        let mut num_red = 0;
        let mut num_blue = 0;

        for color in &self.shirts {
            match color {
                ShirtColor::Red => num_red += 1,
                ShirtColor::Blue => num_blue += 1,
            }
        }
        if num_red > num_blue {
            ShirtColor::Red
        } else {
            ShirtColor::Blue
        }
    }
}

fn main() {
    let store = Inventory {
        shirts: vec![ShirtColor::Blue, ShirtColor::Red, ShirtColor::Blue],
    };

    let user_pref1 = Some(ShirtColor::Red);
    let giveaway1 = store.giveaway(user_pref1);
    println!(
        "The user with preference {:?} gets {:?}",
        user_pref1, giveaway1
    );

    let user_pref2 = None;
    let giveaway2 = store.giveaway(user_pref2);
    println!(
        "The user with preference {:?} gets {:?}",
        user_pref2, giveaway2
    );
}
```

Neste exemplo, podemos ver como closures podem ser usados para capturar valores do ambiente em que são definidos. O método ``giveaway`` recebe a preferência opcional de cor da camisa do vencedor da promoção e usa um closure para determinar qual cor retornar com base na preferência do usuário e no estoque atual da empresa.

Novamente, este código poderia ser implementado de muitas maneiras e aqui, para focar em closures, nos atemos aos conceitos que você já aprendeu, exceto pelo corpo do método ``giveaway`` que usa um closure. No método ``giveaway``, obtemos a preferência do usuário como um parâmetro do tipo ``Option<ShirtColor>`` e chamamos o método ``unwrap_or_else`` em ``user_preference``. O método ``unwrap_or_else`` em ``Option<T>`` é definido pela biblioteca padrão. Ele recebe um argumento: um closure sem nenhum argumento que retorna um valor ``T`` (o mesmo tipo armazenado na variante ``Some`` de ``Option<T>``, neste caso ``ShirtColor``). Se o ``Option<T>`` for a variante ``Some``, ``unwrap_or_else`` retorna o valor de dentro do ``Some``. Se o ``Option<T>`` for a variante ``None``, ``unwrap_or_else`` chama o closure e retorna o valor retornado pelo closure.

Especificamos a expressão de closure ``|| self.most_stocked()`` como argumento para ``unwrap_or_else``. Este é um closure que não recebe parâmetros (se o closure tivesse parâmetros, eles apareceriam entre as duas barras verticais). O corpo do closure chama ``self.most_stocked()``. Estamos definindo o closure aqui e a implementação de ``unwrap_or_else`` avaliará o closure mais tarde se o resultado for necessário.

Veja outro exemplo de closure abaixo. Neste outro exemplo, utilizaremos uma abordagem mais focada em telecomunicações. 

```
struct TelecomCompany {
    customers: Vec<Customer>,
}

struct Customer {
    name: String,
    phone_number: String,
    data_usage: u64,
}

impl TelecomCompany {
    fn send_warning_messages(&self) {
        let warning_threshold = 1000;
        let message = "Aviso: Você atingiu seu limite de uso de dados. Por favor, considere adquirir um plano adicional.";

        let send_message = |phone_number: &str| {
            // código para enviar mensagem para o número de telefone especificado
            println!("Enviando mensagem para {}: {}", phone_number, message);
        };

        self.customers
            .iter()
            .filter(|customer| customer.data_usage > warning_threshold)
            .for_each(|customer| send_message(&customer.phone_number));
    }
}

fn main() {
    let company = TelecomCompany {
        customers: vec![
            Customer {
                name: "Alice".to_string(),
                phone_number: "123-456-7890".to_string(),
                data_usage: 1200,
            },
            Customer {
                name: "Bob".to_string(),
                phone_number: "987-654-3210".to_string(),
                data_usage: 800,
            },
        ],
    };

    company.send_warning_messages();
}
```

Neste exemplo, temos uma estrutura chamada ``TelecomCompany`` que representa uma empresa de telecomunicações. Ela tem um campo chamado customers que contém um ``Vec<Customer>`` representando os clientes da empresa.

Também temos uma estrutura chamada ``Customer`` que representa um cliente da empresa. Ela tem campos para armazenar o nome do cliente, seu número de telefone e seu uso atual de dados.

O método ``send_warning_messages`` definido em ``TelecomCompany`` é usado para enviar mensagens de aviso aos clientes que ultrapassaram um determinado limite de uso de dados. Ele usa closures com os métodos ``filter`` e ``for_each`` do iterador para selecionar apenas os clientes que ultrapassaram o limite e enviar a mensagem a eles.

## Inferência e anotação do tipo de Closures

**Existem mais diferenças entre funções e closures. Closures geralmente não exigem que você anote os tipos dos parâmetros ou o valor de retorno como as funções fazem**. As anotações de tipo são necessárias nas funções porque os tipos fazem parte de uma interface explícita exposta aos seus usuários. Definir esta interface rigidamente é importante para garantir que todos concordem sobre quais tipos de valores uma função usa e retorna. Por outro lado, os closures não são usados em uma interface exposta como esta: eles são armazenados em variáveis e usados sem nomeá-los e expô-los aos usuários de nossa biblioteca.

**Os closures geralmente são curtos e relevantes apenas dentro de um contexto limitado**, em vez de em qualquer cenário arbitrário. Dentro desses contextos limitados, o compilador pode inferir os tipos dos parâmetros e o tipo de retorno, semelhante a como é capaz de inferir os tipos da maioria das variáveis (existem casos raros em que o compilador precisa também das anotações do tipo closure).

Assim como com variáveis, podemos adicionar anotações de tipo se quisermos aumentar a explicitação e clareza ao custo de ser mais verboso do que é estritamente necessário. Anotar os tipos para um closure seria semelhante à definição mostrada abaixo.

```
    let expensive_closure = |num: u32| -> u32 {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };
```

Com as anotações de tipo adicionadas, a sintaxe dos closures fica mais semelhante à sintaxe das funções. Aqui definimos uma função que adiciona 1 ao seu parâmetro e um closure que tem o mesmo comportamento, para comparação. Adicionamos alguns espaços para alinhar as partes relevantes. Isso ilustra como a sintaxe do closure é semelhante à sintaxe da função, exceto pelo uso de pipes e pela quantidade de sintaxe que é opcional:

```
fn  add_one_v1   (x: u32) -> u32 { x + 1 }
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```

Para definições de closure, o compilador inferirá um tipo concreto para cada um de seus parâmetros e para seu valor de retorno. Como você já pode imaginar, observe que não adicionamos nenhuma anotação de tipo à definição. Como não há anotações de tipo, podemos chamar o closure com qualquer tipo, o que fizemos aqui com a primeira vez. Se tentarmos chamar com um inteiro em seguida, obteremos um erro.

Em suma, quando o compilador encontra uma definição de closure sem anotações de tipo explícitas para os parâmetros e o valor de retorno, ele inferirá um tipo concreto para cada um deles **com base no primeiro uso do closure**. Isso significa que se você chamar o closure pela primeira vez com um determinado tipo de argumento, o compilador assumirá que esse é o tipo do parâmetro e gerará um erro se você tentar chamá-lo posteriormente com um argumento de tipo diferente.

O código mostra isso:

```
    let example_closure = |x| x;

    let s = example_closure(String::from("hello"));
    let n = example_closure(5);
```

Veja o que o rust diz:

```
5 |     let n = example_closure(5);
  |             --------------- ^- help: try using a conversion method: `.to_string()`
  |             |               |
  |             |               expected struct `String`, found integer
  |             arguments to this function are incorrect
```

o que comprova a nossa tese.

## Capturando referências ou movendo Ownership

Closures podem capturar valores de seu ambiente de três maneiras, que correspondem diretamente às três maneiras pelas quais uma função pode receber um parâmetro: emprestando imutavelmente, emprestando mutavelmente e tomando posse. O closure decidirá qual desses usar com base no que o corpo da função faz com os valores capturados.


## Movendo valores capturados para fora dos Closures e das Fn Traits.