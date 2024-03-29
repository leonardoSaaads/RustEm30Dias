# **Closures: Funções Anônimas**

Os **Closures** da linguagem de programação Rust são funções anônimas que você pode salvar em uma variável ou passar como argumentos para outras funções. Você pode criar **Closures** em um lugar e, em seguida, chamar **Closures** de outro lugar para avaliá-lo em um contexto diferente. Diferente das funções, os **Closures** podem capturar valores do escopo em que são definidos. Demonstraremos como esses recursos de **Closures** permitem a reutilização e o comportamento do código personalização.

![](https://image.slidesharecdn.com/sgitkjutbcro95jwczkk-signature-23c597326c6884dd557497e9fa417689f5917ed356587f880403ec338b95c660-poli-141024084809-conversion-gate02/95/anonymous-functions-in-javascript-3-638.jpg?cb=1414140674)

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

Closures podem capturar valores de seu ambiente de três maneiras, que correspondem diretamente às três maneiras pelas quais uma função pode receber um parâmetro: emprestando imutavelmente, emprestando mutavelmente e tomando posse. O closure decidirá qual desses usar com base no que o corpo da função faz com os valores capturados. Veja o código abaixo, onde nós definimos um Closure que captura uma referência imutável para o vetor chamado lista, porque ele só precisa de uma referência imutável para imprimir o valor.:

```
fn main() {
    let list = vec![1, 2, 3];
    println!("Before defining closure: {:?}", list);

    let only_borrows = || println!("From closure: {:?}", list);

    println!("Before calling closure: {:?}", list);
    only_borrows();
    println!("After calling closure: {:?}", list);
}
```

Este exemplo também ilustra que uma variável pode se ligar a uma definição de closure, e podemos depois chamar o closure usando o nome da variável e parênteses como se o nome da variável fosse um nome de função.

Como podemos ter múltiplas referências imutáveis para a ``list`` ao mesmo tempo, a ``list`` ainda é acessível pelo código antes da definição do closure, depois da definição do closure mas antes do closure ser chamado, e depois do closure ser chamado.


Veja um outro exemplo abaixo que também ilustra essa relação.

```
// This closure captures a reference to x
let x = 10;
let f = |y| x + y;
println!("{}", f(5)); // prints 15

// This closure moves ownership of x
let x = vec![1, 2, 3];
let f = move |y| x[0] + y;
println!("{}", f(5)); // prints 6
// x is no longer accessible here
```

Se você quiser forçar o closure a assumir a propriedade dos valores que ele usa no ambiente, mesmo que o corpo do closure não precise estritamente de propriedade, você pode usar a palavra-chave ``move`` antes da lista de parâmetros. Em outras palavras, a palavra-chave move em Rust é usada para um closure assumir a propriedade de todos os valores que ele usa no ambiente, em vez de apenas pegá-los por referência ou referência mutável.

Isso significa que o closure pode consumir os valores e não pode usá-los mais de uma vez. Isso é útil quando você quer passar um closure para outra função ou thread que pode ser executada em um momento diferente. Além disso, essa técnica é útil principalmente ao passar um closure para um novo thread para mover os dados para que eles sejam de propriedade do novo thread.

Por enquanto, vamos explorar brevemente a geração de um novo thread usando um closure que precisa da palavra-chave ``move``. O código abaixo mostra como podemos fazer para imprimir o vetor em um novo thread em vez de no thread principal:

```
use std::thread;

fn main() {
    let list = vec![1, 2, 3];
    println!("Before defining closure: {:?}", list);

    thread::spawn(move || println!("From thread: {:?}", list))
        .join()
        .unwrap();
}
```

Nós geramos um novo thread, dando ao thread um Closure para executar como argumento. O corpo do Closure imprime a lista. No código acima ( o que utilizamos move primeiro), o Closure capturou apenas a lista usando uma referência imutável porque é o mínimo de acesso à lista necessário para imprimi-la. Neste exemplo, mesmo que o corpo do Closure ainda precise apenas de uma referência imutável, precisamos especificar que a lista deve ser movida para o Closure colocando a palavra-chave ``move`` no início da definição do Closure. O novo thread pode terminar antes do resto do thread principal terminar ou o thread principal pode terminar primeiro. Se o thread principal mantivesse a propriedade da lista, mas terminasse antes do thread novo e soltasse a lista, a referência imutável no thread seria inválida.

Portanto, o compilador exige que a lista seja movida para o closure dado ao novo thread para que a referência seja válida. Tente remover a palavra-chave move ou usar a lista no thread principal após a definição do closure para ver quais erros do compilador você obtém!

Agora, retornando ao código feito anteriormente, iremos modifica-lo para que ele seja capaz de adicionar um novo elemento na lista. O closure agora captura uma referência mutável:

```
fn main() {
    let mut list = vec![1, 2, 3];
    println!("Before defining closure: {:?}", list);

    let mut borrows_mutably = || list.push(7);

    borrows_mutably();
    println!("After calling closure: {:?}", list);
}
```

Note que não há mais um ``println!`` entre a definição e a chamada do closure ``borrows_mutably``: quando ``borrows_mutably`` é definido, ele captura uma referência mutável à ``list``. Não usamos o closure novamente depois que o closure é chamado, então o empréstimo mutável termina. Entre a definição de closure e a chamada de closure, um empréstimo imutável para imprimir não é permitido porque nenhum outro empréstimo é permitido quando há um empréstimo mutável. Tente adicionar um println! lá para ver que mensagem de erro você recebe!

## Movendo valores capturados para fora dos Closures e das Fn Traits.

Uma vez que um closure tenha capturado uma referência ou propriedade de um valor do ambiente onde o closure é definido (afetando assim o que, se alguma coisa, é movido para o closure), o código no corpo do closure define o que acontece com as referências ou valores quando o closure é avaliado posteriormente (afetando o que, se alguma coisa, é removido do closure). Então, o código que está dentro do closure define o que acontece com essas referências ou valores quando o closure é avaliado mais tarde, afetando o que é movido para fora do closure.

O corpo de um closure pode fazer uma das seguintes coisas: mover um valor capturado para fora do closure, mutar o valor capturado, não mover nem mutar o valor ou não capturar nada do ambiente para começar. A forma como o closure captura e manipula valores do ambiente afeta quais "traits" (ou características) o closure implementa. "Traits" são como funções e estruturas podem especificar que tipos de closures elas podem usar.

Existem três tipos de "traits" que um closure pode implementar: **FnOnce, FnMut e Fn**.

- ``FnOnce`` se aplica a closures que só podem ser chamados uma vez. Todos os closures implementam pelo menos este trait, pois todos os closures podem ser chamados. Um closure que move valores capturados para fora de seu corpo só implementará FnOnce e nenhum dos outros traits Fn, porque ele só pode ser chamado uma vez.


- ``FnMut`` se aplica a closures que não movem valores capturados para fora de seu corpo, mas que podem mutar esses valores. Esses closures podem ser chamados mais de uma vez.

- ``Fn`` se aplica a closures que não movem valores capturados para fora de seu corpo e que não mutam esses valores, bem como a closures que não capturam nada do seu ambiente. Esses closures podem ser chamados mais de uma vez sem mutar seu ambiente, o que é importante em casos como chamar um closure várias vezes simultaneamente.

Vamos começar pelo ``FnOnce``. Vejamos a definição do método ``unwrap_or_else`` em ``Option<T>`` que usamos anteriormente.

```
impl<T> Option<T> {
    pub fn unwrap_or_else<F>(self, f: F) -> T
    where
        F: FnOnce() -> T
    {
        match self {
            Some(x) => x,
            None => f(),
        }
    }
}
```

Lembre-se de que ``T`` é o tipo genérico que representa o tipo do valor na variante ``some`` de uma ``Option``. Esse tipo ``T`` também é o tipo de retorno da função ``unwrap_or_else``: o código que chama ``unwrap_or_else`` em uma ``Option<String>``, por exemplo, obterá uma String.

Em seguida, observe que a função ``unwrap_or_else`` tem o parâmetro de tipo genérico adicional ``F``. O tipo ``F`` é o tipo do parâmetro chamado ``f``, que é o closure que fornecemos ao chamar ``unwrap_or_else``.

A trait ligada especificada no tipo genérico ``F`` é ``FnOnce() -> T``, o que significa que ``F`` deve ser capaz de ser chamado uma vez, sem pegar argumentos e retornando um ``T``. Usar ``FnOnce`` na trait limite expressa a restrição de que ``unwrap_or_else`` só vai chamar ``f`` no máximo uma vez. No corpo de ``unwrap_or_else``, podemos ver que se a ``Option`` é ``Some``, ``f`` não será chamado. Se a opção for ``None``, f será chamado uma vez.

Agora, iremos analisar o conceito de ``FnMut``. Vamos ver o método de biblioteca padrão ````sort_by_key```` definido em fatias (slices), para ver como isso difere de ``unwrap_or_else`` e por que ````sort_by_key```` usa **FnMut** em vez de **FnOnce** para o limite de característica. O closure obtém um argumento na forma de uma referência ao item atual na fatia sendo considerado, e retorna um valor do tipo ``K`` que pode ser ordenado. Esta função é útil quando você deseja classificar uma fatia por um atributo específico de cada item. Vamos fazer uma lista de instâncias Rectangle e usamos ````sort_by_key```` para ordená-los por seu atributo de largura de baixo para cima:

```
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let mut list = [
        Rectangle { width: 10, height: 1 },
        Rectangle { width: 3, height: 5 },
        Rectangle { width: 7, height: 12 },
    ];

    list.``sort_by_key``(|r| r.width);
    println!("{:#?}", list);
}
```

O motivo pelo qual ````sort_by_key```` é definido para receber um encerramento **FnMut é que ele chama o encerramento várias vezes: uma vez para cada item na fatia**. O encerramento ``|r| r.width`` não captura, transforma ou remove nada de seu ambiente, portanto, atende aos requisitos de limite de característica.

Em contraste, ao código abaixo mostra um exemplo de closure que implementa apenas a trait ``FnOnce``, porque move um valor para fora do ambiente. O compilador não nos permite usar esse closure com ````sort_by_key````:

```
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let mut list = [
        Rectangle { width: 10, height: 1 },
        Rectangle { width: 3, height: 5 },
        Rectangle { width: 7, height: 12 },
    ];

    let mut sort_operations = vec![];
    let value = String::from("by key called");

    list.``sort_by_key``(|r| {
        sort_operations.push(value);
        r.width
    });
    println!("{:#?}", list);
}
```

Este closure pode ser chamado uma vez. Tentar chamá-lo uma segunda vez não funcionaria porque o valor não estaria mais no ambiente para ser enviado para ``sort_operations`` novamente! Portanto, este closure implementa apenas **FnOnce**. Quando tentamos compilar este código, recebemos este erro de que o valor não pode ser removido do closure porque o closure deve implementar **FnMut**:

```
15 |     let value = String::from("by key called");
   |         ----- captured outer variable
16 |
17 |     list.``sort_by_key``(|r| {
   |                      --- captured by this `FnMut` closure
18 |         sort_operations.push(value);
   |                              ^^^^^ move occurs because `value` has type `String`, which does not implement the `Copy` trait

```

Para corrigir isso, precisamos alterar o corpo do closure para que ele não mova valores para fora do ambiente. Para contar o número de vezes que ``sort_by_key`` é chamado, mantendo um contador no ambiente e incrementando seu valor no corpo do closure é uma maneira mais direta de calcular isso. O closure no código abaixo funciona com ``sort_by_key`` porque está capturando apenas uma referência mutável ao contador ``num_sort_operations`` e pode, portanto, ser chamado mais de uma vez:

```
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let mut list = [
        Rectangle { width: 10, height: 1 },
        Rectangle { width: 3, height: 5 },
        Rectangle { width: 7, height: 12 },
    ];

    let mut num_sort_operations = 0;
    list.sort_by_key(|r| {
        num_sort_operations += 1;
        r.width
    });
    println!("{:#?}, sorted in {num_sort_operations} operations", list);
}
```

Por fim, o modelo ``Fn`` está mais ligado ao conceito de ``iterators``. Muitos métodos iteradores recebem argumentos de closures. Todos os iteradores implementam uma característica chamada Iterator que é definida na biblioteca padrão. A definição da característica se parece com isto:

```
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations elided
}

```

Veja abaixo um exemplo:

```
let v1: Vec<i32> = vec![1, 2, 3];
let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

assert_eq!(v2, vec![2, 3, 4]);
```

Isso será mais detalhado em Iterators. Mas note que os iterators juntamente com os closures são uma ferramenta poderosa e que podem realizar operações em uma ampla gama de problemas. Melhor ainda, os iterators são muito utilizados em ténicas de otimização.

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/kZXJvLfjUS4/0.jpg)](https://www.youtube.com/watch?v=kZXJvLfjUS4)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD31/README.md)

## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Toggle navigation Closures. cs.brandeis.edu. Disponível em: <https://www.cs.brandeis.edu/~cs146a/rust/doc-02-21-2015/book/closures.html>. Acesso em 24/03/2023

[2] - Closure. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch13-01-closures.html>. Acesso em 24/03/2023

[3] - BING. Conversa com o assistente de busca Bing. [S.l.], 24 mar. 2023. Disponível em: https://www.bing.com. Acesso em: 24 mar. 2023.