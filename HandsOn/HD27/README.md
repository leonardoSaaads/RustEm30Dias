# **Tipos Genéricos**

Usamos genéricos para criar definições para itens como assinaturas de função ou structs, que podemos usar com muitos tipos de dados concretos diferentes. Vamos primeiro ver como definir funções, structs, enums e métodos usando genéricos. Em seguida, discutiremos como os genéricos afetam o desempenho do código.

![](/Imagens/HD27/Generic_pill_hahaha.jpg)

## Genéricos em Funções!

Podemos utilizar o tipo genérico para, como o nome já diz, generalizar uma ampla gama de variáveis. Especialmente em funções, os genéricos possuem uma importância grande, pois podem (e devem) previnir código repetitivo. Neste primeiro exemplo, desejamos fazer uma função que retorna um vetor com os valores da função seno no intervalo $[a, b]$ particionado por um valor $p$.

$$ f(x) = \sin(x) $$

Se planejamos implementar esse código em rust, encontraremos alguns desafios como:

- Qual o tipo das variáveis **a**, **b** e **p**?
- Se forem de tipos diferentes, terei de ficar implementando um código para cada tipo?
- Isso resultaria em um código com 6 funções todas iguais, mas mudando apenas o tipo das variáveis. Existe uma forma mais simples de contornar esse problema?

Motivado por esse tipo de problema que Rust desenvolveu um sistema genérico. Neste primeiro exemplo, iremos assumir que **a**, **b** e **p** são pontos flutuantes.

```
fn calcular_seno(a: f32, b: f32, p: f32) -> Vec<f64> {
    let mut resultados = Vec::new();
    let mut x = a;
    while x <= b {
        resultados.push(x.sin());
        x += p;
    }
    resultados
}

fn main() {
    let resultados = calcular_seno(0.0, 2.0, 0.5);
    println!("{:?}", resultados);
}
```

Apesar desse código funcionar perfeitamente, ele serve apenas para a situção onde a: f32, b: f32, p: f32. Você não gostaria de poder expandir para outro tipo de argumento? Seria interessante fazer um código que funcionasse tanto para f32, como f64, i32 e assim por diante. Agora, iremos fazer uma modificação com o intuito de usarmos um tipo genérico.

```
fn calcular_seno<T>(a: T, b: T, p: T) -> Vec<f64>
where
    T: Into<f64> + Copy
{
    let mut resultados = Vec::new();
    let mut x: f64 = a.into();
    while x <= b.into() {
        resultados.push(x.sin());
        x += p.into();
    }
    resultados
}


fn main() {
    let resultados = calcular_seno(0.0, 2.0, 0.5);
    println!("{:?}", resultados);
    let resultados = calcular_seno(0, 3, 1);
    println!("{:?}", resultados);
}
```

Veja que com pequenas modificações no código, podemos agora usar tanto métodos onde a, b e p são u32, i32, f32 e f64. Iremos destrinchar mais com o exemplo a seguir. 

Imagine que você possui um slice e você deseja achar o maior valor nesse vetor. As funções abaixo chamadas de "maior" mostra duas funções que ambas encontram o maior valor em um slice. Em seguida, vamos combiná-los em um único função que usa genéricos.

```
fn maior_i32(lista: &[i32]) -> &i32 {
    let mut maior = &lista[0];

    for item in lista {
        if item > maior {
            maior = item;
        }
    }

    maior
}

fn maior_char(lista: &[char]) -> &char {
    let mut maior = &lista[0];

    for item in lista {
        if item > maior {
            maior = item;
        }
    }

    maior
}
```

Para parametrizar os tipos em uma nova função única, precisamos nomear o parâmetro de tipo, assim como fazemos para os parâmetros de valor de uma função. Você pode usar qualquer identificador como um nome de parâmetro de tipo. Mas usaremos ``T`` porque, por convenção, os nomes dos parâmetros de tipo em Rust são curtos, geralmente apenas uma letra, e a convenção de nomenclatura de tipo do Rust é CamelCase. Abreviação de “tipo”, ``T`` é a escolha padrão da maioria dos programadores Rust.

Quando usamos um parâmetro no corpo da função, temos que declarar o nome do parâmetro na assinatura para que o compilador saiba o que esse nome significa. Da mesma forma, quando usamos um nome de parâmetro de tipo em uma assinatura de função, temos que declarar o nome do parâmetro de tipo antes de usá-lo. Para definir a maior função genérica, coloque declarações de nome de tipo dentro de colchetes angulares, ``<>``, entre o nome da função e a lista de parâmetros, assim:

```
fn maior<T>(lista: &[T]) -> &T {
```

Lemos essa definição como: a função maior é genérica sobre algum tipo ``T``. Essa função tem um parâmetro chamado lista, que é uma fatia de valores do tipo ``T``. A função maior retornará uma referência a um valor do mesmo tipo ``T``.

O código abaixo mostra a maior definição de função combinada usando o tipo de dados genérico em sua assinatura. A listagem também mostra como podemos chamar a função com uma fatia de valores ``i32`` ou valores ``char``. Observe que esse código ainda não será compilado, mas o corrigiremos mais adiante.

```
fn maior<T>(lista: &[T]) -> &T {
    let mut max_valor = &lista[0];

    for item in lista {
        if item > max_valor {
            max_valor = item;
        }
    }

    max_valor
}
```

Se executarmos esse código, ele irá dar um erro e dar a seguinte que:

```
  |         if item > max_valor {
  |            ---- ^ --------- &T
  |            |
  |            &T
  |
```

**Por que isso ocorre?**

Quando passamos um tipo genérico ao compilador Rust, ele não tem muitas informações sobre como essa estrutura se comporta! você pode ter argumentos que relações de < (maior que) e > (menor que) não fazem sentido. Um exemplo disso é pegar um vetor $<4, 4, 6>$ e afirmar que é maior que $<3, 5, 6>$. Isso não faz sentido. Logo, o compilador Rust só executa essas estruturas quando você diz a ele que com que tipo de estrutura estamos lidando!

Na realidade, esse tipo de estruturas que definem como o argumento da função genérica irá se comportar são ``traits`` (veremos mais na frente) e eles são de extrema importância. Neste caso, iremos adicionar a trait ``std::cmp::PartialOrd``, que assegura que existirá uma relação de ordenação, isto é, as relações de maior, maior ou igual, menor ou igual e menor são mantidas.

Com as alterações feitas:

```
fn maior<T>(lista: &[T]) -> &T 
where 
    T: std::cmp::PartialOrd
{
    let mut max_valor = &lista[0];

    for item in lista {
        if item > max_valor {
            max_valor = item;
        }
    }

    max_valor
}

// alguns testes ráidpos
fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let result = maior(&number_list);
    println!("O maior número é {}", result);

    let char_list = vec!['y', 'm', 'a', 'q'];

    let result = maior(&char_list);
    println!("A maior letra é {}", result);
}
```

## Genéricos em Structs!

Também podemos definir structs para usar um parâmetro de tipo genérico em um ou mais campos usando a sintaxe ``<>``. Também podemos definir structs para usar um parâmetro de tipo genérico em um ou mais campos usando a sintaxe ``<>``. Vejamos um exemplo para ponto:

```
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let inteiro = Point { x: 5, y: 10 };
    let ponto_f = Point { x: 1.0, y: 4.0 };
}
```

A sintaxe para usar genéricos em definições de struct é semelhante àquela usada em definições de função. Primeiro, declaramos o nome do parâmetro de tipo dentro de colchetes logo após o nome da estrutura. Em seguida, usamos o tipo genérico na definição de struct onde, de outra forma, especificaríamos tipos de dados concretos.

Uma coisa importante que você precisa se antentar: x e y possuem o mesmo tipo, logo um ``panic!`` será emergido caso $x=5$ e $y=4.0$, por exemplo. Veja como o erro é exibido:

```
  |
  |     let wont_work = Point { x: 5, y: 4.0 };
  |                                      ^^^ expected integer, found floating-point number
```

Para definir uma estrutura ``Point`` em que ``x`` e ``y`` são genéricos, mas podem ter tipos diferentes, podemos usar vários parâmetros de tipo genérico. Por exemplo, alteramos a definição de ``Point`` para ser genérico sobre os tipos ``T`` e ``U``, onde ``x`` é do tipo ``T`` e ``y`` é do tipo ``U``.

```
struct Point<T, U> {
    x: T,
    y: U,
}

fn main() {
    let both_integer = Point { x: 5, y: 10 };
    let both_float = Point { x: 1.0, y: 4.0 };
    let integer_and_float = Point { x: 5, y: 4.0 };
}
```

Agora todas as instâncias de ``Point`` mostradas são permitidas! Você pode usar quantos parâmetros de tipo genérico quiser em uma definição, mas usar mais do que alguns dificulta a leitura do código.

## Genéricos em Enums!

Assim como fizemos com structs, podemos definir enums para armazenar tipos de dados genéricos em suas variantes. Vamos dar outra olhada no enum ``Option<T>`` que a biblioteca padrão fornece, que usamos no Capítulo de Enums:

```
enum Option<T> {
    Some(T),
    None,
}
```

Esta definição agora deve fazer mais sentido para você. Como você pode ver, a enumeração ``Option<T>`` é genérica sobre o tipo ``T`` e tem duas variantes: ``Some``, que contém um valor do tipo ``T``, e uma variante ``None`` que não contém nenhum valor. Usando a enum ``Option<T>``, podemos expressar o conceito abstrato de um valor opcional e, como ``Option<T>`` é genérico, podemos usar essa abstração, independentemente do tipo do valor opcional.

```
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

A enumeração ``Result`` é genérica em dois tipos, ``T`` e ``E``, e tem duas variantes: ``Ok``, que contém um valor do tipo ``T``, e ``Err``, que contém um valor do tipo ``E``. Essa definição torna conveniente usar a enumeração ``Result`` em qualquer lugar temos uma operação que pode ser bem-sucedida (retornar um valor de algum tipo T) ou falhar (retornar um erro de algum tipo E). Na verdade, é isso que usamos para abrir um arquivo, onde`` T`` foi preenchido com o tipo ``std::fs::File`` quando o arquivo foi aberto com sucesso e ``E`` foi preenchido com o tipo ``std:: io::Erro`` quando houve problemas ao abrir o arquivo.

**Ao reconhecer situações em seu código com várias definições de struct ou enum que diferem apenas nos tipos de valores que contêm, você pode (e deve!) evitar a duplicação usando tipos genéricos.**

Vale a pena ressaltar que você também pode utilizar genéricos na outra formatação de ``struct``. No exemplo abaixo, usaremos genéricos para montar um modelo RGB e HSI

```
// RGB tem valores de 0 à 255;
struct Color_RGB (u8, u8, u8);

// Se você for utilizar um novo modelo de cores
// você poderá utilizar genéricos para definir 
// como essas novas estruturas de cores serão.
struct Color_New<F, G, H> (F, G, H);
```

## Genéricos em Impl!

Podemos implementar métodos em structs e enums e também usar tipos genéricos em suas definições. Neste primeiro exemplo, veremos como criar ``impl`` simples que utiliza genéricos em sua composição. Iremos comentar mais sobre as estruturas no segundo exemplo:

```
struct Pilha<T> {
    dados: Vec<T>,
}

impl<T> Pilha<T> {
    fn nova() -> Self {
        Pilha { dados: Vec::new() }
    }

    fn adiciona(&mut self, item: T) {
        self.dados.push(item);
    }

    fn retira(&mut self) -> Option<T> {
        self.dados.pop()
    }

    fn tamanho(&self) -> usize {
        self.dados.len()
    }
}

fn main() {
    let mut pilha = Pilha::<i32>::nova();
    pilha.adiciona(1);
    pilha.adiciona(2);
    pilha.adiciona(3);
    println!("Tamanho da pilha: {}", pilha.tamanho());

    while let Some(item) = pilha.retira() {
        println!("Item retirado: {}", item);
    }
}
```

Em primeiro lugar, criamos a estrutura ``Pilha`` e depois implementamos ``implt<T>``, que contém métodos que operam na pilha. Na função ``main``, criamos uma ``Pilha`` que armazena valores ``i32``, especificando ``Pilha::<i32>::nova()``. Então, empilhamos alguns valores na pilha, imprimimos seu tamanho e desempilhamos todos os valores da pilha.

Vamos ao segundo exemplo, onde definimos um método chamado ``x`` em ``Point<T>`` que retorna uma referência aos dados no campo ``x``.

```
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };
    println!("p.x = {}", p.x());
}
```

Observe que temos que declarar ``T`` logo após ``impl`` para que possamos usar ``T`` para especificar que estamos implementando métodos do tipo ``Point<T>``. Ao declarar ``T`` como um tipo genérico após ``impl``, Rust pode identificar que o tipo entre os colchetes em ``Point`` é um tipo genérico em vez de um tipo concreto. Poderíamos ter escolhido um nome diferente para este parâmetro genérico do parâmetro genérico declarado na definição de ``struct``, mas usar o mesmo nome é convencional. Os métodos escritos dentro de um ``impl`` que declara o tipo genérico serão definidos em qualquer instância do tipo, não importa qual tipo concreto acabe substituindo o tipo genérico.

Também podemos especificar restrições em tipos genéricos ao definir métodos no tipo. Poderíamos, por exemplo, implementar métodos apenas em instâncias ``Point<f32>`` em vez de instâncias ``Point<T>`` com qualquer tipo genérico. Veja esse exemplo abaixo:

```
// Point deve ser f64
impl Point<f64> {
    fn distance_from_origin(&self) -> f64 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

Este código significa que o tipo ``Point<f32>`` terá um método distance_from_origin; outras instâncias de ``Point<T>`` em que ``T`` não é do tipo ``f64`` não terão esse método definido.

Parâmetros de tipo genérico em uma definição de ``struct`` nem sempre são os mesmos que você usa nas mesmas assinaturas de método de struct. O exemplo abaixo usa os tipos genéricos ``X1`` e ``Y1`` para a estrutura ``Point`` e ``X2, Y2`` para a assinatura do método mixup para tornar o exemplo mais claro. O método cria uma nova instância de ``Point`` com o valor ``x`` do próprio Point (do tipo X1) e o valor y do Point passado (do tipo Y2).

```
struct Point<X1, Y1> {
    x: X1,
    y: Y1,
}

impl<X1, Y1> Point<X1, Y1> {
    fn mixup<X2, Y2>(self, other: Point<X2, Y2>) -> Point<X1, Y2> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 5, y: 10.4 };
    let p2 = Point { x: "Hello", y: 'c' };

    let p3 = p1.mixup(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}

```

O objetivo deste exemplo é demonstrar uma situação em que alguns parâmetros genéricos são declarados com impl e outros são declarados com a definição do método. Aqui, os parâmetros genéricos X1 e Y1 são declarados após impl porque acompanham a definição de struct. Os parâmetros genéricos X2 e Y2 são declarados após a mistura de fn, porque são relevantes apenas para o método.

## E a performace do código?

Inicialmente tendemos a acreditar que os tipos genéricos acarretam em uma perda de performace na compilação e execução devido a criação de novas estruturas, mas isso não ocorre!

O Rust realiza isso executando a monomorfização do código usando genéricos em tempo de compilação. Monomorfização é o processo de transformar código genérico em código específico, preenchendo os tipos concretos que são usados ​​quando compilados. Nesse processo, o compilador faz o oposto das etapas que usamos para criar a função genérica: o compilador examina todos os locais onde o código genérico é chamado e gera código para os tipos concretos com os quais o código genérico é chamado.

Como Rust compila código genérico em código que especifica o tipo em cada instância, não pagamos nenhum custo de tempo de execução pelo uso de genéricos. Quando o código é executado, ele funciona exatamente como se tivéssemos duplicado cada definição manualmente. O processo de monomorfização torna os genéricos do Rust extremamente eficientes em tempo de execução.

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/6rcTSxPJ6Bw/0.jpg)](https://www.youtube.com/watch?v=6rcTSxPJ6Bw)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD28/README.md)

## REFERÊNCIAS BIBLIOGRÀFICAS

[1] - Generic Data Types. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch10-01-syntax.html>. Acesso em 17 de fevereiro de 2023.

[2] - "O código gerado neste trabalho foi produzido com a ajuda do ChatGPT, um modelo de linguagem treinado pela OpenAI, que utiliza redes neurais para gerar texto natural em diversos idiomas e contextos. Para mais informações sobre o ChatGPT, consulte a documentação oficial da OpenAI."

Imagem - https://www.policymed.com/wp-content/uploads/2013/04/6a00e5520572bb8834017c3875ac22970b.jpg