# **Enums**

Neste capítulo vamos ver enumerações, também chamadas de **enums**. Enums permitem definir um tipo por meio da enumeração de seus possíveis valores. Primeiro, vamos definir e usar uma enum para mostrar como ela pode atrelar significado aos nossos dados. Depois, vamos explorar uma enum particularmente útil, chamada Option, que expressa um valor que tanto pode ser algo quanto pode não ser nada.

Vamos ver um caso em que enums podem ser mais apropriadas do que structs e descobrir como elas podem ser úteis. Digamos que estamos trabalhando com endereços IP. Atualmente, existem duas versões do protocolo IP que são mais utilizadas: a quatro e a seis. Estas são as únicas possibilidades para um endereço IP com que o nosso programa vai trabalhar: nós podemos enumerar todos os possíveis valores, é daí que vem o nome enumeração.

Um endereço IP qualquer pode ser ou da versão quatro ou da versão seis, mas nunca das duas ao mesmo tempo. Esta propriedade dos endereços IP faz com que a enum seja bem apropriada para este caso, pois enums só podem assumir o valor de uma de suas variantes. Os endereços de ambas as versões, seja quatro ou seis, ainda são, fundamentalmente, endereços IP, e deveriam ser tratados pelo mesmo tipo no código em situações que se aplicam a qualquer versão de endereço IP.

___
### Nota histórica sobre as versões IP:

Segundo o site da Faculdade de Engenharia da Universidade do Porto, a Internet nasceu em 1969, nos Estados Unidos. Inicialmente, interligava os diversos laboratórios de pesquisa e chamava-se ARPAnet (ARPA: Advanced Research Projects Agency).  Era uma rede do Departamento de Defesa norte-americano cujo principal objectivo era atender necessidades militares. Assim, a função desta rede era que em caso de ocorrência de guerras ou catástrofes que pudessem afectar os meios de comunicação dos EUA, continuassem activas as ligações entre universidades e órgãos principais do governo mesmo que parte de rede fosse destruída. Era então necessário a utilização de um protocolo de comunicação que assegurasse tais funcionalidades, foi assim que começou a ser desenvolvida a arquitectura TCP/IP.

Uma linha do tempo pode ser desenvolvida:

- 1975: A ARPA deu lugar ao DARPA (Defence Advanced Research Projects Agency) e começou a desenvolver os protocolos TCP/IP.

- 1979: Foi formado comité ICCB (Internet Control and Configuration Board) para gerir o desenvolvimento do TCP/IP.

- 1983: A DARPA cedeu os direitos do código dos protocolos TCP/IP à Universidade da Califórnia para que fosse distribuído na versão UNIX. A DARPA exigiu que todos os PCs ligados ao ARPANET usassem os protocolos TCP/IP. Esses protocolos difundiram-se rapidamente, visto não serem produtos comerciais.

- 1992: Muitas redes foram desenvolvidas sobre o TCP/IP, novas aplicações criadas e um conjunto de serviços desenvolvidos de forma a melhorar e a diferenciar o tráfego que circula na Internet.

- 2012: Foi criada a sexta versão do protocolo da internet. A versão 6 é a mais atual e sucede a anterior, IPV4. A longo prazo, o IPv6 tem como objetivo substituir o IPv4, que suporta somente cerca de 4 bilhões $4 \times 10^9$ de endereços IP, contra cerca de 340 undecilhões $3,4 \times 10^{38}$ de endereços do novo protocolo. Em termos mais exatos, o IPv4 suporta 4294967296 endereços, enquanto o IPv6 suporta 340282366920938463463374607431768211456 endereços.

Enquanto os endereços IPv4 possuem a formatação de apenas quatro octetos de bits, os endereços de IPv6 possuem 16 octetos de bits. Cada octeto é um conjunto de oito bits. Logo, no IPv4, temos 32 bits disponíveis para escrever um endereço, enquanto no IPv6 temos 128 bits.

Como exemplo, a formatação de um endereço aleatório IPv4 é dado da seguinte forma: $168.192.53.193$

Um endereço aleatório IPv6 seria dado, teoricamente, como tendo a seguinte formatação: $73.93.165.99.201.192.192.153.251.0.0.0.0.93.158.63$

Mas devido a termos endereços praticamente inesgotáveis, nós representamos os endereços IPv6 com valores hexadecimais. Ou seja, números de 0 a 9 e letras de A a F. Sabendo disso, podemos escrever um endereço IPv6 dessa forma: 2001:0BAA:0000:0000:0000:24A3:123B:98BC.

![IPv6](https://e3z7c6v7.rocketcdn.me/blog/wp-content/uploads/2015/02/ipv6.jpg.webp)

___

Veja o código abaixo:

```
fn main() {
enum VersaoIp {
    V4,  //  Protocolo de Internet versão 4
    V6,  //  Protocolo de Internet versão 6
    }
}
```

``V4`` e ``V6`` são as chamadas variantes da enum. Podemos criar instâncias de cada uma das duas variantes de VersaoIp, da seguinte forma:

```
fn main() {
enum VersaoIp {
    V4,
    V6,
}

let quatro = VersaoIp::V4;
let seis = VersaoIp::V6;
}
```

Repare que as variantes pertencem ao namespace da enum, e se usa ``::`` para separar os dois. Isso é útil porque agora ambos os valores ``VersaoIp::V4`` e ``VersaoIp::V6`` são do mesmo tipo: VersaoIp. O uso de enums tem ainda mais vantagens. Pensando mais a fundo sobre o nosso tipo de endereço IP, ainda não temos uma forma de representar o endereço em si, apenas sabemos qual a versão dele.

Podemos utilizar um modelo se struct para lidarmos com as diferentes versões de IP. Veja o exemplo abaixo:

```
fn main() {
enum VersaoIp {
    V4,
    V6,
    }

struct EnderecoIp {
    versao: VersaoIp,
    endereco: String,
    }

let local = EnderecoIp {
    versao: VersaoIp::V4,
    endereco: String::from("127.0.0.1"),
    };

let loopback = EnderecoIp {
    versao: VersaoIp::V6,
    endereco: String::from("::1"),
    };
}
```

Aqui nós definimos uma struct ``EnderecoIp`` que tem dois membros: ``versao``, do tipo ``VersaoIp`` (que definimos anteriormente) e endereco, do tipo String. Temos duas instâncias dessa struct. A primeira, local, tem o valor ``VersaoIp::V4`` como sua versao, e um endereço associado igual a ``127.0.0.1``. A segunda instância, loopback, tem como sua versao a outra variante de ``VersaoIp``, V6, e o endereço ``::1`` associado a ela. Nós usamos uma struct para encapsular os valores de versao e endereco, agora a variante está associada ao valor.

Podemos representar o mesmo conceito de uma forma mais concisa usando apenas uma enum, em vez de uma enum dentro de uma struct, colocando dados dentro de cada variante da enum, diretamente. Esta nova definição da enum ``EnderecoIp`` diz que ambas as variantes, V4 e V6, terão uma ``String`` associada:

```
fn main() {
enum EnderecoIp {
    V4(String),  // Recebe String
    V6(String),  // Recebe String 
}

// Estrutura IPv4
let local = EnderecoIp::V4(String::from("168.192.53.193"));
// Estrutura IPv6
let loopback = EnderecoIp::V6(String::from("2001:0BAA:0000:0000:0000:24A3:123B:98BC"));
}
```

Há uma outra vantagem de se usar uma enum em vez de uma struct: **cada variante pode conter dados de diferentes tipos e quantidades**. Os endereços IP da versão quatro têm sempre quatro componentes numéricas, cada uma com valor de 0 a 255. Se quiséssemos representar endereços V4 como quatro valores u8, e ao mesmo tempo manter os endereços V6 como uma String, não poderíamos usar uma struct. Já as enums podem facilmente atender a este caso:

```
fn main() {
enum EnderecoIp {
    V4(u8, u8, u8, u8),
    V6(String),
}

let local = EnderecoIp::V4(168, 192, 53, 193);

let loopback = EnderecoIp::V6(String::from("2001:0BAA:0000:0000:0000:24A3:123B:98BC"));
}
```

Acabamos de ver algumas possibilidades que poderíamos usar para representar endereços IP das duas versões por meio de uma enum. Vamos ver outro exemplo de uma ``enum``.

```
fn main() {
enum Mensagem {
    Sair,
    Mover { x: i32, y: i32 },
    Escrever(String),
    MudarCor(i32, i32, i32),
    }
}
```

Esta enum tem quatro variantes de diferentes tipos:

- ``Sair`` não tem nenhum dado associado.
- ``Mover`` contém uma struct anônima.
- ``Escrever`` contém uma única String.
- ``MudarCor`` contém três valores do tipo i32.

Definir uma ``enum`` com variantes iguais aos exemplo acima é similar a definir diferentes tipos de ``struct``, exceto que a enum não usa a palavra-chave struct, e todas as variantes são agrupadas dentro do tipo ``Mensagem``.

Se fossemos fazer algo parecido em struct, teríamos de gastar mais tempo fazendo código não limpo. Veja o exemplo abaixo:

```
fn main() {
struct MensagemSair; // unit struct
struct MensagemMover {
    x: i32,
    y: i32,
}
struct MensagemEscrever(String); // tuple struct
struct MensagemMudarCor(i32, i32, i32); // tuple struct
}
```

Há mais uma similaridade entre enums e structs: da mesma forma como podemos definir métodos em structs usando impl, também podemos definir métodos em enums. Aqui está um método chamado invocar, que poderia ser definido na nossa enum Mensagem:

```
fn main() {
enum Mensagem {
    Sair,
    Mover { x: i32, y: i32 },
    Escrever(String),
    MudarCor(i32, i32, i32),
}

impl Mensagem {
    fn invocar(&self) {
        // o corpo do método é definido aqui
    }
}

let m = Mensagem::Escrever(String::from("olá"));
m.invocar();
}
```

O corpo do método usaria o valor ``self`` para obter a mensagem sobre a qual o método foi chamado. Neste exemplo, criamos a variável ``m``, que contém o valor ``Mensagem::Escrever(String::from("olá"))``, e é isso que self vai ser no corpo do método invocar quando ``m.invocar()`` for executado.

Vamos ver agora outra enum da biblioteca padrão que também é muito útil e comum: ``Option``.

## A Enum Option e Null

Sir Charles Antony Richard Hoare, comumente conhecido como Tony Hoare, é um cientista da computação britânico, provavelmente mais conhecido pelo desenvolvimento em 1960, aos 26 anos, do Quicksort. Ele também desenvolveu a lógica Hoare, a linguagem formal de comunicação de processos sequenciais (CSP), e inspirou a linguagem de programação Occam.

Em sua apresentação de 2009 “Null References: The Billion Dollar Mistake”, Tony Hoare, o inventor do null, diz o seguinte:

"Eu chamo isso de meu erro de um bilhão de dólares. Naquela época, eu estava projetando o primeiro sistema de tipos abrangente para referências em uma linguagem orientada a objetos. Meu objetivo era garantir que todo uso de referências fosse absolutamente seguro, com a verificação realizada automaticamente pelo compilador. Mas não resisti à tentação de colocar uma referência nula, simplesmente porque era muito fácil de implementar. Isso levou a inúmeros erros, vulnerabilidades e falhas no sistema, que provavelmente causaram um bilhão de dólares de dor e danos nos últimos quarenta anos."

O problema com valores nulos é que, se você tentar usar um valor nulo como um valor não nulo, receberá algum tipo de erro. Como essa propriedade nula ou não nula é generalizada, é extremamente fácil cometer esse tipo de erro.

No entanto, o conceito que null está tentando expressar ainda é útil: um null é um valor atualmente inválido ou ausente por algum motivo.

O problema não está realmente no conceito, mas na implementação específica. Como tal, Rust não possui nulos, mas possui uma enumeração que pode codificar o conceito de um valor estar presente ou ausente. Esta enum é a ``Option<T>``, que está definida na biblioteca padrão da seguinte forma:

```
fn main() {
enum Option<T> {
    Some(T), // algum valor
    None,    // nenhum valor
    }
}
```

Você pode usar ``Some`` e ``None`` diretamente sem prefixá-las com ``Option::``. A estrutura ``Option<T>`` continua sendo uma enum como qualquer outra, e ``Some(T)`` e ``None`` ainda são variantes do tipo ``Option<T>``.

A sintaxe do ``<T>`` é uma característica do Rust de que não falamos ainda. Trata-se de um parâmetro de tipo genérico, vamos abordá-lo com mais detalhe futuramente. Por ora, tudo que você precisa saber é que ``<T>`` significa que a variante ``Some`` da enum Option pode conter um dado de qualquer tipo.

Você pode se perguntar: Por que devemos utilizar o Oprtion<T> ao invés de um nulo qualquer?

Isso está relacionado com a segurança da limguagem. Por exemplo, o código seguinte não vai compilar, porque ele está tentando somar um i8 a um Option<i8>:

```
let x: i8 = 5;
let y: Option<i8> = Some(5);

let soma = x + y;
```

Ao tentamos executar esse código, Rust exibirá um erro. O que quer dizer que o Rust não consegue entender como somar um i8 e um Option<i8>, porque eles são de tipos diferentes.

Em outras palavras, você tem que converter um ``Option<T>`` em um ``T`` antes de poder executar operações com ele. Geralmente, isso ajuda a detectar um dos problemas mais comuns com valores nulos: assumir que algo não é nulo quando, na verdade, ele é. Só de não ter que se preocupar com a possibilidade de ter deixado um valor nulo escapar já lhe dá mais confiança em seu código.


Para entender um pouco mais sobre o tema, veja o vídeo abaixo:

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/DSZqIJhkNCM/0.jpg)](https://www.youtube.com/watch?v=DSZqIJhkNCM)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD04/README.md)

**REFERÊNCIAS BIBLIOGRÁFICAS**

[1] - Entendendo o IPv6. Alura - Matheus, Yuri. Disponível em: <https://www.alura.com.br/artigos/entendendo-o-ipv6l>. Acesso em 15/09/2022.

[2] - Pequena história da Internet. Faculdade de Engenharia da Universidade do Porto. Disponível em: <https://paginas.fe.up.pt/~mrs01003/TCP_IP.htm>. Acesso em 15/09/2022.

[3] - Definindo uma Enum. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch06-01-defining-an-enum.html>. Acesso em 15/09/2022.

Null References: The Billion Dollar Mistake. infoQ. Disponível em:<https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/>. Acesso em 15/09/2022.

Imagens 01 - https://e3z7c6v7.rocketcdn.me/blog/wp-content/uploads/2015/02/ipv6.jpg.webp