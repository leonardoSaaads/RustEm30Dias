# **Matchs**

O Rust tem um excelente operador de controle de fluxo chamado ``match``, que nos permite comparar um valor com uma série de padrões e executar um código com base no padrão que casar. Padrões podem ser compostos de valores literais, variáveis, caracteres-curinga e várias outras coisas. A grande utilidade do ``match`` vem da expressividade dos padrões e das análises feitas pelo compilador, tendo certeza de que todos os casos possíveis estão sendo tratados.

![](/Imagens/HD14/OperadorMatch.png)

Imagine que expressão ``match``(combinar, em português) funciona como um operador matemático que recebe um (ou alguns) valor(es) e cospe outro(s). Em outras palavras, pode ser visto como uma máquina de moedas - as moedas passam por um canal que possui furos de vários tamanhos, e cada moeda cai no primeiro furo em que ela couber. Da mesma forma, os valores passam por cada padrão de um ``match``, e logo no primeiro padrão que o valor "se encaixar", o bloco de código que estiver associado a ele será executado.

Vamos ver um exemplo de uma máquina de refrigerantes. Cada refrigerante possui um valor próprio e a máquina mostrará esse valor dado o nome de um refri. Nesta máquina, os refrigerantes disponíveis são Coca-Cola, Guaraná, Fanta Laranja, fanta Uva e H2O. Eles custam, respectivamente, R$ 3.22, R$ 2.32, R$ 6.37, R$ 3.93 e R$ 3.19. 

```
fn main() {
enum Refrigerante {
    CocaCola,
    Guarana,
    FantaLaranja,
    FantaUva,
    H2O,
    }

fn valor_do_refri(refri: Refrigerante) -> f32 {
    match refri {
        Refrigerante::CocaCola     => 3.22,
        Refrigerante::Guarana      => 2.32,
        Refrigerante::FantaLaranja => 6.37,
        Refrigerante::FantaUva     => 3.93,
        Refrigerante::H2O          => 3.19,
        }
    }
    
    let refrigerante_do_leonardo = Refrigerante::Guarana;
    let refrigerante_do_rodrigo = Refrigerante::CocaCola;
    let refrigerante_da_carla = Refrigerante::FantaUva;
    
    println!("O refri que Lenonardo escolheu custa: {}", valor_do_refri(refrigerante_do_leonardo));
    println!("O refri que Rodrigo escolheu custa: {}", valor_do_refri(refrigerante_do_rodrigo));
    println!("O refri que Carla escolheu custa: {}", valor_do_refri(refrigerante_da_carla));
}
```

Vamos analisar o ``match`` da função ``valor_do_refri``. Primeiro, usamos a palavra-chave ``match`` seguida de uma expressão, que neste caso é o atributo ``refri``. É parecido a uma expressão utilizada com ``if``, mas tem uma grande diferença: com if, a expressão precisa retornar um valor booleano. Aqui, pode ser de qualquer tipo. O tipo da variável refri, neste exemplo, é a enum ``Refrigerante``, que definimos acima.

Em seguida vêm os braços do match. Um braço é composto por duas partes: um padrão e algum código. O primeiro braço deste exemplo tem um padrão, que é o valor ``Refrigerante::CocaCola``, e o operador ``=>`` que separa o padrão do código associado a ele. O código, nesse caso, é apenas o valor ``3.22``. Os braços são separados uns dos outros com uma vírgula.

Quando o ``match`` é executado, ele compara o valor resultante com o padrão de cada braço, na ordem. Se o valor casar com um determinado padrão, o código associado a esse padrão será executado. Se o valor não se encaixa nesse padrão, a execução passa para o próximo braço, bem parecido com a execução de uma máquina. Podemos ter tantos braços quanto precisamos. Em códigos de baixo nível, existem operadores match centenas de braços. Um exemplo é o código de modulação de amplitude em quadratura, onde os valores de uma sequência de bits está associada a um número complexo, muito presente em telecomunicações.

![](/Imagens/HD14/Modula%C3%A7%C3%A3oAmplitudeQuadratura.png)

Tipicamente não se usa chaves se o braço do match for curto, como é o caso do nosso primeiro código, em que cada braço retorna apenas um valor. Se você quiser executar mais de uma linha de código em um braço, você pode usar chaves para delimitá-las. Por exemplo, o código seguinte vai escrever na tela "Refrigerante com Desconto!!!" sempre que o método for chamado com uma Refrigerante::H2O, mas ainda vai retornar o último valor do bloco, 3.19:

```
fn valor_do_refri(refri: Refrigerante) -> f32 {
    match refri {
        Refrigerante::CocaCola     => 3.22,
        Refrigerante::Guarana      => 2.32,
        Refrigerante::FantaLaranja => 6.37,
        Refrigerante::FantaUva     => 3.93,
        Refrigerante::H2O          => {
            println!("Refrigerante com Desconto!!!");
            3.19
            },
        }
    }
```

## Padrões Atrelados a Valores

Outra característica útil dos braços do match é que eles podem ser atrelados a partes dos valores que se encaixam no padrão. É assim que podemos extrair valores dentro de uma variante de uma enum.

Por exemplo, vamos alterar uma das nossas variantes, inserindo dados dentro dela. Como exemplo, iremos utilizar a Coca-Cola. A bebeida surgiu em 1886, quando o farmacêutico americano John S Pemberton desenvolveu uma fórmula para um xarope. Foi na Jacob ‘s Pharmacy que o xarope começou a ser misturado com água gaseificada e oferecido para que as pessoas experimentassem. Essa mistura deu tão certo que chamou a atenção de Frank Robinson, o contador da farmácia, e idealizador do refrigerante da Coca-Cola Company, 2 anos depois da morte de John Pemberton, em 1891.

A popularidade da bebida aconteceu nos Estados Unidos, após a segunda guerra mundial. Foi inclusive nessa época que surgiram as latinhas. Os refrigerantes eram todos comercializados em garrafas de vidro e as latinhas tinham o objetivo de facilitar o transporte com seu material mais resistente que o vidro.

Com a expensão da empresa de bebidas e a diversificação de produtos, alguns modelos de Coca-Cola ficaram marcados como sendo raros. Dentre esses produtos Raros, podemos citar as garrafas da copa de 2006 e a promoção com os nomes das pessoas. Podemos adicionar essa informação à nossa ``enum`` alterando a variante ``CocaCola`` para incluir o atributo
 ``Evento``.

```
#![allow(unused)]
fn main() {
#[derive(Debug)]
enum Evento {
    Copa,
    Cha,
    Beijing,
    // ... etc
}

enum Refrigerante {
    CocaCola(Evento),
    Guarana,
    FantaLaranja,
    FantaUva,
    H2O,
    }
```

Vamos imaginar que um amigo nosso está tentando colecionar todas as Coca-Colas raras de todos os Eventos. Enquanto separamos nosso refri por tipo de marca, vamos também dizer o nome do eventos associado a cada Coca-Cola. Se for um dos que o nosso amigo ainda não tem, ele pode colocá-lo na sua coleção.

Na expressão match desse código, vamos adicionar uma variável chamada evento ao padrão que casa com os valores da variante ``Refrigerante::CocaCola``. Quando um ``Refrigerante::CocaCola`` é testada, a variável ``evento`` vai ser atrelada ao valor do evento daquela Coca. Assim vamos poder usar o evento no código do braço, desse jeito:

```
fn valor_do_refri(refri: Refrigerante) -> f32 {
    match refri {
        Refrigerante::CocaCola(evento)    => {print!("Coca do evento: {:?}", evento); 3.22},
        Refrigerante::Guarana             => 2.32,
        Refrigerante::FantaLaranja        => 6.37,
        Refrigerante::FantaUva            => 3.93,
        Refrigerante::H2O                 => 3.19,
    }
}
```

Se executarmos ``valor_do_refri(Refrigerante::CocaCola(Evento::Beijing))``, refri corresponde a ``Refrigerante::CocaCola(Evento::Beijing)``.

## Usando match com Option<T>

Na seção anterior, queríamos obter o valor ``T`` contido em um ``Some`` quando era o caso em uma ``Option<T>``. Também podemos manipular uma ``Option<T>`` usando match, assim como fizemos com a enum Refrigerante! Em vez de comparar moedas, vamos comparar as variantes de ``Option``, mas a forma de trabalhar com a expressão match continua a mesma.

Digamos que queremos escrever uma função que recebe um ``Option<i32>``, e se houver um valor embutido nele, soma um a esse valor. Se não houver um valor, a função deve retornar ``None``, e nem tentar executar nenhuma operação.

Essa função é bem fácil de implementar, graças ao match, e vai ficar semelhante ao código abaixo:

```
fn main() {
fn mais_um(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let cinco = Some(5);
let seis = mais_um(cinco);
let nenhum = mais_um(None);
}
```

OBS: O Rust sabe que nós não cobrimos todos os casos possíveis, e sabe até de qual padrão nos esquecemos! Matches em Rust são exaustivos: precisamos extinguir até a última possibilidade pra que o nosso código seja válido. Especialmente no caso de uma Option<T>, em que o Rust não nos deixa esquecer de tratar explicitamente o caso None. Ele nos impede de assumir que temos um valor válido quando possivelmente temos um valor nulo, e portanto, cometer o erro de um bilhão de dólares que vimos mais cedo.

A linguagem também tem um padrão que podemos usar em situações em que não queremos listar todos os valores possíveis. Por exemplo, um ``u8`` pode ter valores válidos de 0 a 255. Se nos importamos apenas com os valores 1, 3, 5 e 7, não queremos ser obrigados a listar o 0, 2, 4, 6, 8, 9, e assim por diante até 255. Felizmente, nem precisamos: em vez disso, podemos usar o padrão especial ``_``.

```
fn main() {

let algum_valor_u8 = 0u8;
match algum_valor_u8 {
    1 => println!("um"),
    3 => println!("três"),
    5 => println!("cinco"),
    7 => println!("sete"),
    _ => (),
    }
}
```

O padrão ``_`` casa com qualquer valor. Colocando ele depois dos demais braços, o ``_`` vai casar com todos os casos possíveis que não foram especificados antes dele. O ``()`` é só o valor-unidade, pra que nada aconteça no caso ``_``. Como resultado, podemos dizer que não queremos fazer nada com os possíveis valores que não listamos antes do *placeholder* ``_``.

## REFERÊNCIAS BIBLIOGRÁICAS

[1] - Operador match de Controle de Fluxo. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch06-02-match.html>. Acesso em 18/09/2022.

[2] - TIPOS DE COCA COLA: CONHEÇA E EXPERIMENTE OS SABORES EXÓTICOS!. Disponível em: <hhttps://blog.tabacariadamata.com.br/tipos-de-coca-cola-conheca-e-experimente-os-sabores-exoticos/>. Acesso em 18/09/2022.

IMAGEM 02 - https://www.researchgate.net/figure/a-32-QAM-b-64-QAM-c-128-QAM-constellation-maps-in-the-case-of-1100mV-driving_fig6_344313794