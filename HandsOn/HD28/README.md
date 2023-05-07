# **Traits: Definindo Comportamento de Estruturas**

Um trait em Rust é uma maneira de definir comportamento compartilhado entre tipos. Eles são semelhantes a interfaces em outras linguagens de programação. Traits nos permitem usar outro tipo de abstração: eles nos permitem abstrair sobre o comportamento que tipos têm em comum. Um trait diz ao compilador de Rust sobre uma funcionalidade que um tipo particular possui e pode compartilhar com outros tipos.

Por exemplo, se quisermos escrever uma função que possa aceitar argumentos de vários tipos diferentes (como `i32`, `f64`, etc.), mas queremos garantir que esses tipos tenham um comportamento específico (como serem imprimíveis), podemos usar um trait para especificar esse comportamento e restringir os argumentos da função aos tipos que implementam esse trait.

Para definir um trait, usamos a palavra-chave ``trait`` seguida pelo nome do trait e um bloco de código contendo as assinaturas dos métodos que o trait deve ter. Os métodos podem ter implementações padrão ou podem ser deixados sem corpo para serem implementados pelos tipos que implementam o trait.

Para implementar um trait para um tipo específico, usamos a palavra-chave ``impl`` seguida pelo nome do trait e do tipo para o qual estamos implementando o trait. Dentro do bloco de código seguinte, fornecemos as implementações dos métodos especificados no trait.

**Traits são uma ferramenta poderosa em Rust para abstrair comportamentos compartilhados entre tipos e permitir a reutilização de código.**

## Definindo uma trait;

O comportamento de um tipo consiste nos métodos que podemos chamar naquele tipo. Diferentes tipos compartilham o mesmo comportamento se pudermos chamar os mesmos métodos em todos esses tipos. As definições de trait são uma maneira de agrupar assinaturas de método para definir um conjunto de comportamentos necessários para realizar algum propósito.

Por exemplo, digamos que temos várias structs que contêm vários tipos e quantidades de texto: uma struct NewsArticle que contém uma reportagem jornalística arquivada em um local específico e um pequeno texto opinativo que pode ter no máximo 700 caracteres junto com metadados que indicam se era um novo texto opinativo, um compartilhamento de texto opinativo ou uma resposta a outro texto opinativo.

Queremos criar uma biblioteca crate chamada ``aggregator`` que possa exibir resumos dos dados armazenados em instâncias NewsArticle ou OpiniaoTexto. Para fazer isso, precisamos de um resumo de cada tipo e solicitaremos esse resumo chamando o método summarize em uma instância. O código mostra a definição de um trait público Summary que expressa esse comportamento.

```
pub trait Summary {
    fn summarize(&self) -> String;
}
```

Aqui, declaramos um trait usando a palavra-chave ``trait`` e depois o nome do trait, que neste caso é ``Summary``. Também declaramos o trait como ``pub`` para que crates que dependam deste crate possam fazer uso deste trait também, como veremos em alguns exemplos. Dentro das chaves, declaramos as assinaturas dos métodos que descrevem os comportamentos dos tipos que implementam este trait, que neste caso é ``fn summarize(&self) -> String``.

Após a assinatura do método, em vez de fornecer uma implementação dentro de chaves, usamos um ponto-e-vírgula. Cada tipo que implementa este trait deve fornecer seu próprio comportamento personalizado para o corpo do método. O compilador garantirá que qualquer tipo que tenha o ``trait Summary`` terá o método summarize definido com esta assinatura exatamente.

## Implementando uma Traint em um Type

Agora que definimos as assinaturas desejadas dos métodos do ``trait Summary``, podemos implementá-lo nos tipos em nosso agregador de mídia. O código abaixo mostra uma implementação do trait Summary na struct ``NewsArticle`` que usa o título, o autor e o local para criar o valor de retorno de ``summarize``. Para a struct OpiniaoTexto, definimos summarize como o nome de usuário seguido pelo texto inteiro, supondo que o conteúdo do tweet já esteja limitado a 700 caracteres.

```
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct OpiniaoTexto {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub shareable: bool,
}

impl Summary for OpiniaoTexto {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```

Implementar um trait em um tipo é semelhante a implementar métodos regulares. A diferença é que depois de ``impl``, colocamos o nome do trait que queremos implementar, então usamos a palavra-chave ``for`` e especificamos o nome do tipo para o qual queremos implementar o ``trait``. Dentro do bloco ``impl``, colocamos as assinaturas dos métodos que a definição do trait definiu. Em vez de adicionar um ponto-e-vírgula após cada assinatura, usamos chaves e preenchemos o corpo do método com o comportamento específico que queremos que os métodos do trait tenham para o tipo específico.

Agora que a biblioteca implementou o ``trait Summary em NewsArticle`` e ``OpiniaoTexto``, os usuários da crate podem chamar os métodos do trait em instâncias de ``NewsArticle`` e ``OpiniaoTeto`` da mesma maneira que chamamos métodos regulares. A única diferença é que o usuário deve trazer o trait para o escopo, bem como os tipos. Aqui está um exemplo de como uma crate binária poderia usar nossa crate biblioteca ``aggregator``:

```
use aggregator::{Summary, OpiniaoTexto};

fn main() {
    let texto = OpiniaoTexto {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        shareable: false,
    };

    println!("1 new texto : {}", texto.summarize());
}
```

Outras crates que dependem da crate aggregator também podem trazer o ``trait Summary`` para o escopo para implementar ``Summary`` em seus próprios tipos. Uma restrição a ser observada é que podemos implementar um trait em um tipo apenas se pelo menos um dos dois (trait ou tipo) for local à nossa crate. Por exemplo, podemos implementar traits da biblioteca padrão como Display em um tipo personalizado como OpiniaoTexto como parte da funcionalidade de nossa crate aggregator, porque o tipo OpiniaoTexto é local à nossa crate aggregator. Também podemos implementar Summary em ``Vec<T>`` em nossa crate aggregator, porque o trait Summary é local à nossa crate aggregator.

Mas não podemos implementar traits externos em tipos externos. Por exemplo, não podemos implementar o ``trait Display`` em ``Vec<T>`` dentro de nossa crate aggregator, porque Display e Vec<T> são ambos definidos na biblioteca padrão e não são locais à nossa crate aggregator. Essa restrição faz parte de uma **propriedade chamada coerência e mais especificamente a regra do órfão**, assim chamada porque o tipo pai não está presente. Essa regra garante que o código de outras pessoas não possa quebrar seu código e vice-versa. Sem a regra, duas crates poderiam implementar o mesmo trait para o mesmo tipo e Rust não saberia qual implementação usar.

## Implementações Default (Padrão)

Às vezes é útil ter um comportamento padrão para alguns ou todos os métodos de um trait em vez de exigir implementações para todos os métodos em cada tipo. Então, à medida que implementamos o trait em um tipo específico, podemos manter ou substituir o comportamento padrão de cada método.

No código baixo, especificamos uma string padrão para o método summarize do trait Summary em vez de apenas definir a assinatura do método.

```
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}

```

Para usar uma implementação padrão para resumir instâncias de ``NewsArticle``, especificamos um bloco ``impl`` vazio com ``impl Summary for NewsArticle {}``.

Mesmo que não estejamos mais definindo o método ``summarize`` em ``NewsArticle`` diretamente, fornecemos uma implementação padrão e especificamos que ``NewsArticle`` implementa o ``trait Summary``. Como resultado, ainda podemos chamar o método ``summarize`` em uma instância de ``NewsArticle``, assim:

```
    let article = NewsArticle {
        headline: String::from("Penguins win the Stanley Cup Championship!"),
        location: String::from("Pittsburgh, PA, USA"),
        author: String::from("Iceburgh"),
        content: String::from(
            "The Pittsburgh Penguins once again are the best \
             hockey team in the NHL.",
        ),
    };

    println!("New article available! {}", article.summarize());
```

A criação de uma implementação padrão não exige que mudemos nada sobre a implementação do Summary de OpiniaoTexto no código já demonstrado acima. O motivo é que a sintaxe para substituir uma implementação padrão é a mesma que a sintaxe para implementar um método de traits que não tem uma implementação padrão.

Implementações padrão podem chamar outros métodos no mesmo trait, mesmo que esses outros métodos não tenham uma implementação padrão. Dessa forma, um trait pode fornecer muita funcionalidade útil e exigir apenas que os implementadores especifiquem uma pequena parte dele. Por exemplo, poderíamos definir o trait ``Summary`` para ter um método ``summarize_author`` cuja implementação é obrigatória e, em seguida, definir um método ``summarize`` que tem uma implementação padrão que chama o método ``summary_author``:

```
pub trait Summary {
    fn summarize_author(&self) -> String;

    fn summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }
}
```

Para usar esta versão do ``Summary``, só precisamos definir ``summary_author`` quando implementamos o trait em um tipo:

``` 
impl Summary for OpiniaoTexto {
    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }
}
```

Depois de definirmos resumir_autor, podemos chamar resumir em instâncias da estrutura ``OpiniaoTexto``, e a implementação padrão de ``summarize`` chamará a definição de ``summary_author`` que fornecemos. Como implementamos ``summary_author``, o trait ``Summary`` nos deu o comportamento do método resumir sem exigir que escrevêssemos mais código.

```
    let tweet = OpiniaoTexto {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        shareable: false,
    };

    println!("1 new tweet: {}", tweet.summarize());

```

Observe que não é possível chamar a implementação padrão a partir de uma implementação substituta desse mesmo método.

## Traits como parâmetros

Agora que você já sabe como definir e implementar traits, podemos explorar como usar traits para definir funções que aceitam muitos tipos diferentes. Usaremos o traits ``Summary`` que implementamos nos tipos ``NewsArticle`` e ``OpiniaoTexto`` no código abaixo para definir uma função de notificação que chama o método ``summarize`` em seu parâmetro item, que é de algum tipo que implementa o traits ``Summary``. Para fazer isso, usamos a sintaxe ``impl Trait``, assim:

```
 pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

Em vez de um tipo concreto para o parâmetro do ``item``, especificamos a palavra-chave ``impl`` e o nome da trait. Este parâmetro aceita qualquer tipo que implemente a característica especificada. No corpo de ``notify``, podemos chamar qualquer método no item que vem do trait ``Summary``, como ``summarize``. Podemos chamar ``notify`` e passar em qualquer instância de ``NewsArticle`` ou ``OpiniaoTexto``. O código que chama a função com qualquer outro tipo, como String ou i32, não será compilado porque esses tipos não implementam ``Summary``.

## **Sintaxe das Traits**

A sintaxe ``impl Trait`` funciona para casos diretos, mas na verdade é uma variação extremanente útil de sintaxe para uma forma mais longa conhecida como limite de traits; Se parece com isso:

```
 pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

Essa forma mais longa é equivalente ao exemplo da seção anterior, mas é mais detalhada. Colocamos traits de característica com a declaração do parâmetro de **tipo genérico** após dois pontos e dentro de colchetes.

A sintaxe ``impl Trait`` é conveniente e torna o código mais conciso em casos simples, enquanto a sintaxe mais completa pode expressar mais complexidade em outros casos. Por exemplo, podemos ter dois parâmetros que implementam ``Summary``. Fazendo isso com a sintaxe ``impl Trait``, fica assim:

```
pub fn notify(item1: &impl Summary, item2: &impl Summary) {
```

Usar ``impl Trait`` é apropriado se quisermos que esta função permita que ``item1`` e ``item2`` tenham tipos diferentes (desde que ambos os tipos implementem ``Summary``). Se quisermos forçar ambos os parâmetros a terem o mesmo tipo, porém, devemos usar um limite de traço, como este:

```
pub fn notify<T: Summary>(item1: &T, item2: &T) {
```

O tipo genérico T especificado como o tipo dos parâmetros item1 e item2 restringe a função de forma que o tipo concreto do valor passado como um argumento para item1 e item2 deve ser o mesmo.

## Multiplas Traits

Usar muitos traits de característica tem suas desvantagens. Cada genérico tem seus próprios traits de traço. Por esta razão, Rust tem uma sintaxe alternativa para especificar traits de características dentro de uma cláusula ``where`` após a assinatura da função. Então ao invés de escrever isso:

```
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {
```

podemos usar uma cláusula where, assim:

```
fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{

```

A assinatura dessa função é menos confusa: o nome da função, a lista de parâmetros e o tipo de retorno estão próximos, semelhante a uma função sem muitos limites de característica.

Veja um exemplo:

```
fn jogo_contar_palavras<T, U>(texto: T, numero: U) -> bool
where
    T: AsRef<str>,
    U: Into<usize> + PartialEq<usize>,
{
    // Inicializa o contador
    let mut count = 0;
    // Itera por cada palavra no texto fornecido
    for palavra_atual in texto.as_ref().split_whitespace() {
        // Incrementa o contador se a palavra atual for "correto"
        if palavra_atual == "correto" {
            count += 1;
        }
    }
    // Verifica se o contador é igual ao número fornecido
    count == numero.into()
}
```

A função conta_palavras tem dois parâmetros genéricos: texto e numero. O primeiro parâmetro, texto, é uma referência a uma string, que implementa o trait ``AsRef<str>``. O segundo parâmetro, numero, é uma quantidade que pode ser convertida em um número inteiro e comparada com o número de palavras **"correto"** no texto.

Em outras palavras, se você acertar quantas vezes a palavra "correto" tem em uma texto, você ganha!. Veja uma potencial função main para o código feito acima.

```
fn main() {
    let texto = "o resultado do jogo foi correto, mas ele não foi jogado corretamente";
    let numero_palavras_corretas = 2;

    if conta_palavras(texto, numero_palavras_corretas) {
        println!("Você acertou!");
    } else {
        println!("Você perdeu!");
    }
}
```

O resultado deve ser "Você perdeu", pois a palavra **"correto"** apareceu apenas uma vez.

## Retornando tipos que implementam traits

Também podemos usar a sintaxe ``impl Trait`` na posição de retorno para retornar um valor de algum tipo que implementa uma trait, como mostrado aqui:

```
fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        retweet: false,
    }
}
```

