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

