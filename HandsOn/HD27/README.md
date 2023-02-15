# **Tipos Genéricos**

Usamos genéricos para criar definições para itens como assinaturas de função ou structs, que podemos usar com muitos tipos de dados concretos diferentes. Vamos primeiro ver como definir funções, structs, enums e métodos usando genéricos. Em seguida, discutiremos como os genéricos afetam o desempenho do código.

## Genéricos em Funções!

Podemos utilizar o tipo genérico para, como o nome já diz, generalizar uma ampla gama de variáveis. Especialmente em funções, os genéricos possuem uma importância grande, pois podem (e devem) previnir código repetitivo. Neste primeiro exemplo, desejamos fazer uma função que retorna um vetor com os valores da função seno no intervalo $[a, b]$ particionado por um valor &p&.

$$ \sin(x) $$

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

Veja que com pequenas modificações no código, podemos agora usar tanto métodos onde a, b e p são u32, i32, f32 e f64. Iremos ver outro exemplo a seguir. 

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

