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



## REFERÊNCIAS BIBLIOGRÁICAS

[1] - Operador match de Controle de Fluxo. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch06-02-match.html>. Acesso em 18/09/2022.

IMAGEM 02 - https://www.researchgate.net/figure/a-32-QAM-b-64-QAM-c-128-QAM-constellation-maps-in-the-case-of-1100mV-driving_fig6_344313794