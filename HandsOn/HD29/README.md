# **Lifetimes** 

Em Rust, os lifetimes (tempo de vida) são uma característica fundamental do sistema de tipos, que ajuda a garantir a segurança da memória. O conceito de lifetimes é usado para garantir que referências a dados (ponteiros) são válidas por tempo suficiente para serem usadas com segurança.

Anotar tempo de vida não é nem mesmo um conceito que a maioria das outras linguagens de programação tem, então isso vai parecer desconhecido e novo. Embora não abordemos os tempos de vida em sua totalidade neste capítulo, discutiremos maneiras comuns de encontrar sintaxe lifetime para que você possa se sentir confortável com o conceito.

## Evitando referências penduradas com tempo de vida.

O principal objetivo do tempo de vida é evitar referências penduradas, que fazem com que um programa faça referência a dados diferentes dos dados que ele pretende referenciar. Em ourtras linguagens de programação, isso pode fazer sérios problemas, pois variáveis fora do escopo podem alterar todo o ambiente de trabalho - consequentemente aumentando o desperdício de tempo que você gasta debugando o programa. Veja um exemplo abaixo:

```
fn main() {
    // Iniciamos uma variável qualquer 
    let variavel_fora_escopo;

    {   
        // criamos outra variável 
        let temperatura = 24.5;
        // acoplamos essa variável na variável fora de escopo
        variavel_fora_escopo = &temperatura;
        println!("A temperatura é: {}", variavel_fora_escopo)
    }

    println!("variavel é: {}", variavel_fora_escopo);
}
```

O escopo externo declara uma variável chamada ``variavel_fora_escopo`` sem valor inicial e o escopo interno declara uma variável chamada ``temperatura`` com o valor inicial de 24.5. Dentro do escopo interno, tentamos definir o valor de ``variavel_fora_escopo`` como uma referência a ``temperatura``. Em seguida, o escopo interno termina e tentamos imprimir o valor em ``variavel_fora_escopo``. Esse código não será compilado porque o valor ao qual ``variavel_fora_escopo`` está se referindo saiu do escopo antes de tentarmos usá-lo. Então, como Rust determina que esse código é inválido? Ele usa um **verificador de empréstimo**.

## O verificador de empréstimos

O compilador Rust tem um verificador de empréstimos que compara escopos para determinar se todos os empréstimos são válidos. Deixando nosso exemplo mais elucidativo, o que o verificador de emprestimos faz, é verificar o tempo de vida dentro de cada namespace, como mostra a figura abaixa, semelhante ao exemplo utilizado acima:

```
fn main() {
    let r;                // ---------+-- 'a
                          //          |
    {                     //          |
        let x = 5;        // -+-- 'b  |
        r = &x;           //  |       |
    }                     // -+       |
                          //          |
    println!("r: {}", r); //          |
}                         // ---------+
```

Aqui, anotamos o tempo de vida de ``r`` com ``'a`` e o tempo de vida de ``x`` com ``'b``. Como você pode ver, o bloco ``'b`` interno é muito menor do que o bloco externo ``'a`` lifetime. No momento da compilação, Rust compara o tamanho das duas vidas e vê que ``r`` tem uma vida de ``'a``, mas que se refere à memória com uma vida de ``'b``. O programa é rejeitado porque ``'b`` é mais curto que ``'a``: o assunto da referência não vive tanto quanto a referência.

Para concertar o código feito acima, podemos simplismente utilizar o mesmo namespace, fazendo com que o tempo de vida não seja perdido! Veja abixo:

```
fn main() {
    let x = 5;            // ----------+-- 'b
                          //           |
    let r = &x;           // --+-- 'a  |
                          //   |       |
    println!("r: {}", r); //   |       |
                          // --+       |
}                         // ----------+
```

Aqui, ``x`` tem o tempo de vida ``'b``, que neste caso é maior que ``'a``. Isso significa que ``r`` pode fazer referência a ``x`` porque **Rust sabe que a referência em ``r`` sempre será válida enquanto ``x`` é válida.**

## Tempos de vida genéricos em funções

Vamos imaginar que você é funcionário de uma empresa de telecomunicações e você deseja saber o quanto um usuário da sua rede está gastando de tempo em ligações. Supondo que seu chefe queira ver quanto tempo a ``Alice`` está gastando no telefone, você utiliza o seguinte código:

```
fn main() {
    let calls = [
        Call {caller: "Bob", duration: 110  },
        Call {caller: "Bob", duration: 182  },
        Call {caller: "Alice", duration: 40 },
        Call {caller: "Alice", duration: 33 },
        Call {caller: "Alice", duration: 16 },
    ];

    // seu chefe pede para você saber quantos minutos a Alice
    // ficou no telefone nesse banco de dados
    // usuário que você deseja saber a duração da chamada
    let user = "Alice";
    let total_duration_alice = calculate_total_duration(&calls, user);
    println!("Duração total das chamadas de {}: {} segundos", user, total_duration_alice);
}
```

Quando você consulta a base de dados da sua empresa, você nota que há um grande erro! Elas não utilizaram lifetimes - e devido a esse motivo ficou impossível utilizar o código. Veja como a empresa deixou os dados sem lifetimes:

```
// Struct que representa uma chamada telefônica
struct Call {
    caller: &str,
    duration: u32,
}

fn calculate_total_duration(calls: &[Call], user: &str) -> u32 {
    calls                               //  +------------------------+
    .iter()                             //  | Toda essa parte acha   |
    .filter(|call| call.caller == user) //  | um usuário da lista    |
    .map(|call| call.duration)          //  | e calcula quanto tempo |
    .sum()                              //  | ele ficou no celular.  |
                                        //  +------------------------+
}
```

Quando o código foi definido na empresa, os engenheiros de software esqueceram do seguinte detalhe: não sabemos os tempos de vida concretos das referências que serão passadas, então não podemos olhar para os escopos para determinar se a referência que retornamos sempre será válida. O verificador de empréstimo também não pode determinar isso, porque não sabe como os tempos de vida são.

Para solucionar o problema, podemos utilizar a seguinte formatação:

```
// Struct que representa uma chamada telefônica
struct Call<'a> {
    caller: &'a str,
    duration: u32,
}

// Função que calcula a duração total das chamadas feitas por um determinado usuário
fn calculate_total_duration<'a>(calls: &[Call<'a>], user: &'a str) -> u32 {
    calls
        .iter()
        .filter(|call| call.caller == user)
        .map(|call| call.duration)
        .sum()
}
```

veja que apareceu um ``<'a>`` em ``struct Call<'a>``, ``caller: &'a str,``, ``calculate_total_duration<'a>`` e em ``calls: &[Call<'a>], user: &'a str``. Mas por que isso ocorreu?

Nessa versão corrigida, introduzimos um tempo de vida genérico ``'a`` na estrutura ``Call`` e na função ``calculate_total_duration``. O tempo de vida ``'a`` está relacionado à vida útil das referências ``&str``. A declaração ``struct Call<'a>`` indica que a estrutura ``Call`` possui uma referência ``caller`` com tempo de vida ``'a``. Da mesma forma, a função ``calculate_total_duration<'a>`` recebe uma fatia de ``Call`` com tempo de vida ``'a`` e uma referência user com tempo de vida ``'a``.

Veremos com mais detalhes isso nos tópicos abaixo.

## Sintaxe de anotação lifetimes

As anotações de lifetimes **não alteram o tempo de vida de qualquer uma das referências**. Em vez disso, elas descrevem os relacionamentos dos tempos de vida de referências múltiplas entre si sem afetar os tempos de vida. Assim como as funções podem aceitar qualquer tipo quando a assinatura especifica um parâmetro de tipo genérico, **as funções podem aceitar referências com qualquer tempo de vida especificando um parâmetro de tempo de vida genérico.**

Anotações de tempo de vida têm uma sintaxe um pouco incomum: **os nomes dos parâmetros de tempo de vida devem começar com um apóstrofo (')** e geralmente são todos minúsculos e muito curtos, como tipos genéricos. A maioria das pessoas usa o nome 'a para a primeira anotação da vida. Colocamos anotações de parâmetro de vida útil após o & de uma referência, usando um espaço para separar a anotação do tipo da referência.

Aqui estão alguns exemplos: uma referência a um i32 sem um parâmetro de tempo de vida, uma referência a um i32 que tem um parâmetro de tempo de vida chamado 'a, e uma referência mutável a um i32 que também tem o tempo de vida 'a.

```
&i32        // uma referência
&'a i32     // uma referência com um tempo de vida explícito
&'a mut i32 // uma referência mutável com um tempo de vida explícito
```

Uma anotação de tempo de vida por si só não tem muito significado, porque as anotações destinam-se a informar ao Rust como os parâmetros de tempo de vida genéricos de várias referências se relacionam entre si.

## Anotações lifetimes em assinaturas de função

Para usar anotações de tempo de vida em assinaturas de função, precisamos declarar os parâmetros de tempo de vida genéricos dentro de colchetes entre o nome da função e a lista de parâmetros, assim como fizemos com os parâmetros de tipo genérico.

Queremos que a assinatura expresse a seguinte restrição: a referência retornada será válida desde que ambos os parâmetros sejam válidos. Esta é a relação entre os tempos de vida dos parâmetros e o valor de retorno. Vamos nomear o tempo de vida ``'a`` e depois adicioná-lo a cada referência. Veja abaixo o exemplo de uma função que retorna a maior string.

```
fn maior_string<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

A assinatura da função agora diz ao Rust que, por algum tempo de vida ``'a``, a função recebe dois parâmetros, ambos os quais são fatias de string que duram pelo menos tanto quanto o tempo de vida ``'a``. A assinatura da função também informa ao Rust que a fatia de string retornada da função viverá pelo menos tanto quanto o tempo de vida ``'a``. Na prática, isso significa que o tempo de vida da referência retornada pela função ``maior_string`` é igual ao menor dos tempos de vida dos valores referenciados pelos argumentos da função. Esses relacionamentos são o que queremos que o Rust use ao analisar esse código.

Lembre-se, quando especificamos os parâmetros de tempo de vida nesta assinatura de função, não estamos alterando os tempos de vida de quaisquer valores transmitidos ou retornados. Em vez disso, estamos especificando que o verificador de empréstimo deve rejeitar quaisquer valores que não cumpram essas restrições. Observe que a função ``maior_string`` não precisa saber exatamente quanto tempo ``x`` e ``y`` viverão, apenas que algum escopo pode ser substituído por ``'a`` que satisfará essa assinatura.

Ao anotar tempos de vida em funções, as anotações vão na assinatura da função, não no corpo da função. As anotações vitalícias tornam-se parte do contrato da função, assim como os tipos na assinatura. Ter assinaturas de função contendo o contrato vitalício significa que a análise do compilador Rust pode ser mais simples. Se houver um problema com a forma como uma função é anotada ou chamada, os erros do compilador podem apontar para a parte do nosso código e as restrições com mais precisão. Se, em vez disso, o compilador Rust fizesse mais inferências sobre o que pretendíamos que fossem os relacionamentos dos tempos de vida, o compilador só poderia apontar para um uso de nosso código a muitos passos da causa do problema.

Quando passamos referências concretas para a ``maior_string``, o tempo de vida concreto que é substituído por ``'a`` é a parte do escopo de ``x`` que se sobrepõe ao escopo de ``y``. Em outras palavras, o tempo de vida genérico ``'a`` obterá o tempo de vida concreto que é igual ao menor dos tempos de vida de ``x`` e ``y``. Como anotamos a referência retornada com o mesmo parâmetro de tempo de vida ``'a``, a referência retornada também será válida para o comprimento do menor dos tempos de vida de ``x`` e ``y``.

Vamos deixar as coisas mais claras com esse exemplo: Vejamos como as anotações de tempo de vida restringem a função ``maior_string`` passando referências que têm tempos de vida concretos diferentes.

```
fn main() {
    let string1 = String::from("long string is long");

    {
        let string2 = String::from("xyz");
        let result = maior_string(string1.as_str(), string2.as_str());
        println!("A maior string é {}", result);
    }
    // Como resultado, temos a frase:
    // "A maior string é long string is long"
}
```

Neste exemplo, ``string1`` é válido até o final do escopo externo, ``string2`` é válido até o final do escopo interno e ``result`` faz referência a algo que é válido até o final do escopo interno. Execute este código e você verá que o verificador de empréstimo aprova; ele irá compilar e imprimir. A maior string é long string is long.

Note que se mudassemos a condição desse código, o verifador de empréstimos não aprovaria essa medida. Neste segundo caso, iremos mudar a ``result`` de lugar e o verificador não aprovará esse movimento. Lembre-se sempre que o tempo de vida concreto que é igual ao menor dos tempos de vida de ``string1`` e ``string2``, então, neste caso, o tempo de vida é igual ao tempo de vida de ``string2``. Veja que se mudarmos o result de lugar, **isso provocará um erro!**

```
fn main() {
    let string1 = String::from("long string is long");
    let result;
    {
        let string2 = String::from("xyz");
        result = longest(string1.as_str(), string2.as_str());
    }
    println!("The longest string is {}", result);
}
```

O erro mostra que para o resultado ser válido para a declaração ``println!``, ``string2`` precisaria ser válido até o final do escopo externo. Rust sabe disso porque anotamos os tempos de vida dos parâmetros da função e retornamos valores usando o mesmo parâmetro de tempo de vida ``'a``.

```
// VERIFICAÇÃO DE TEMPO (Olhar mais humano)

// {
//  string1 -------------+
//      {   string2 -----+------+ (namespace da execução da função)
//          result  -----+      |
//      }                       |
// }                            |
//                              ˅
//                       result é válido
```

No outro caso, onde ``result`` é declarado fora de escopo, isso terá um erro, pois a função que definimos exige que a saída tenha o mesmo tempo de vida do bloco com menor tempo de vida. Como resultado, rust sobre um erro.

Como humanos, podemos olhar para este código e ver que ``string1`` é maior que ``string2`` e, portanto, o resultado conterá uma referência a ``string1``. Como ``string1`` ainda não saiu do escopo, uma referência a ``string1`` ainda será válida para a declaração ``println!``. No entanto, o compilador não pode ver se a referência é válida nesse caso.

```
// VERIFICAÇÃO DE TEMPO (Olhar mais humano)

// {
//  string1 -------------+
//  result --------------+
//      {   string2 -----+------+ (namespace da execução da função)
//      }                       |
// }                            |
//                              ˅
//                      result NÃO é válido
```

## Pensando com base em termpos de vida

A maneira como você precisa especificar parâmetros de tempo de vida depende do que sua função está fazendo. Por exemplo, se alterássemos a implementação da função ``maior_string`` para sempre retornar o primeiro parâmetro em vez da fatia de ``maior_string``, não precisaríamos especificar um tempo de vida no parâmetro ``y``. O seguinte código irá compilar:

```
//                note que y mudou
fn maior_string<'a>(x: &'a str, y: &str) -> &'a str {
    x
}
```

Especificamos um parâmetro de tempo de vida ``'a`` para o parâmetro ``x`` e o tipo de retorno, mas não para o parâmetro ``y``, porque o tempo de vida de ``y`` não tem nenhuma relação com o tempo de vida de ``x`` ou o valor de retorno.

Ao retornar uma referência de uma função, o parâmetro de tempo de vida para o tipo de **retorno precisa corresponder ao parâmetro de tempo de vida de um dos parâmetros**. Caso a referência retornada não se refira a um dos parâmetros, ela deve se referir a um valor criado dentro desta função. No entanto, isso seria uma referência oscilante porque o valor sairia do escopo no final da função. Considere esta tentativa de implementação da função ``maior_string`` que não compila:

```
fn maior_string<'a>(x: &str, y: &str) -> &'a str {
    let result = String::from("really long string");
    result.as_str()
}
```

Aqui, embora tenhamos especificado um parâmetro de tempo de vida ``'a`` para o tipo de retorno, esta implementação falhará ao compilar porque o tempo de vida do valor de retorno não está relacionado ao tempo de vida dos parâmetros. Aqui está a mensagem de erro que recebemos:

```
$ cargo run
   Compiling chapter10 v0.1.0 (file:///projects/chapter10)
error[E0515]: cannot return reference to local variable `result`
  --> src/main.rs:11:5
   |
11 |     result.as_str()
   |     ^^^^^^^^^^^^^^^ returns a reference to data owned by the current function

For more information about this error, try `rustc --explain E0515`.
error: could not compile `chapter10` due to previous error
```

O problema é que o ``result`` sai do escopo e é limpo no final da função ``maior_string``. Também estamos tentando retornar uma referência ao ``result`` da função. Não há como especificar parâmetros de vida útil que mudariam a referência pendente, e o Rust não nos permitirá criar uma referência pendente. Nesse caso, a melhor correção seria retornar um tipo de dados de propriedade em vez de uma referência para que a função de chamada seja responsável por limpar o valor.

Em última análise, a sintaxe de tempo de vida é sobre conectar os tempos de vida de vários parâmetros e valores de retorno de funções. Uma vez conectados, o Rust tem informações suficientes para permitir operações seguras de memória e impedir operações que criariam ponteiros pendentes ou violariam a segurança da memória.

## Anotações de tempo de vida em definições struct

Até agora, todas as ``structs`` que definimos contêm tipos próprios. Podemos definir ``structs`` para conter referências, mas nesse caso precisaríamos adicionar uma anotação vitalícia em cada referência na definição da struct.

```
struct ``ImportantExcerpt``<'a> {
    part: &'a str,
}

fn main() {
    let ``novel`` = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = ``novel``.split('.').next().expect("Could not find a '.'");
    let i = ``ImportantExcerpt`` {
        part: first_sentence,
    };
}
```

Essa struct tem a ``part`` do campo que contém uma fatia de string - que é uma referência. Assim como acontece com os tipos de dados genéricos, declaramos o nome do parâmetro genérico de tempo de vida dentro de colchetes após o nome do struct para que possamos usar o parâmetro de tempo de vida no corpo da definição do struct. Essa anotação significa que uma instância de ``ImportantExcerpt`` não pode sobreviver à referência que contém em seu campo de parte.

A função ``main`` aqui cria uma instância da struct ``ImportantExcerpt`` que contém uma referência à primeira sentença da String pertencente à variável ``novel``. Os dados em ``novel`` existem antes da instância ``ImportantExcerpt`` ser criada. Além disso, o romance não sai do escopo até que o ``ImportantExcerpt`` saia do escopo, portanto, a referência na instância do ``ImportantExcerpt`` é válida.

Veja outro exemplo: 

```
struct TelecommunicationsDevice<'a> {
    id: u32,
    name: &'a str,
    lifespan: u32,
}

impl<'a> TelecommunicationsDevice<'a> {
    fn new(id: u32, name: &'a str, lifespan: u32) -> TelecommunicationsDevice<'a> {
        TelecommunicationsDevice {
            id,
            name,
            lifespan,
        }
    }

    fn check_lifespan(&self) {
        if self.lifespan > 0 {
            println!("O dispositivo {} está ativo.", self.name);
        } else {
            println!("O dispositivo {} atingiu o fim de sua vida útil.", self.name);
        }
    }
}

fn main() {
    let device_name = "Huawei Smartphone";
    let mut device = TelecommunicationsDevice::new(1, device_name, 2);
    device.check_lifespan(); // O dispositivo Smartphone está ativo.
    device.lifespan -= 1;
    device.check_lifespan(); // O dispositivo Smartphone está ativo.
    device.lifespan -= 1;
    device.check_lifespan(); // O dispositivo Smartphone atingiu o fim de sua vida útil.
}
```

Neste exemplo, o struct TelecommunicationsDevice possui três campos: id para identificar o dispositivo, name para armazenar o nome do dispositivo e lifespan para rastrear a vida útil do dispositivo em anos. O tempo de vida é representado como um número inteiro.

O tempo de vida é associado ao tempo de vida do parâmetro 'a usado no struct. Isso permite que o dispositivo referencie uma string (name) que está vinculada a um escopo externo. A utilização do tempo de vida permite ao Rust garantir em tempo de compilação que não ocorram referências inválidas.

No exemplo main(), criamos uma instância do dispositivo TelecommunicationsDevice chamada device com uma vida útil de 2 anos. Chamamos o método check_lifespan() para verificar o estado do dispositivo após cada ano. Ao final de 2 anos, a vida útil do dispositivo é zerada e é exibida uma mensagem indicando que o dispositivo atingiu o fim de sua vida útil.

Comentaremos mais abaixo sobre a ``impl`` em structs nos tópicos abaixo.

## Lifetime Elision

