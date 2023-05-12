# **Iterators: Dando um *Boost* em seu código**

Iteradores são uma ferramenta utilizada em programação para percorrer sequências de dados de forma ordenada, executando uma tarefa específica em cada item dessa sequência. Em Rust, os iteradores são preguiçosos, o que significa que eles não são executados imediatamente, mas somente quando você chama um método que consome o iterador.

Por exemplo, como vimos anteriormente, é criado um iterador sobre os itens de um vetor usando o método ``iter()`` do tipo ``Vec<T>``. No entanto, esse código por si só não faz nada útil, pois apenas cria o iterador, mas não o utiliza de fato.

Para utilizar o iterador e executar uma tarefa em cada item da sequência, é necessário chamar um método que consuma o iterador, como ``map()``, ``filter()``, ``fold()``, entre outros. Esses métodos são responsáveis por aplicar a lógica de iteração em cada item do iterador e, quando necessário, determinar quando a sequência chegou ao fim.

Ao utilizar os métodos dos iteradores em Rust, você não precisa se preocupar em implementar a lógica de iteração manualmente, pois isso é feito pelos próprios métodos dos iteradores. Isso torna o código mais simples e legível, além de evitar erros comuns que podem ocorrer ao implementar a lógica de iteração manualmente.

Em Rust, os iteradores são preguiçosos, o que significa que eles não têm efeito até que você ligue métodos que consomem o iterador para usá-lo. Por exemplo, o código abaixo:

```
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();
```

No exemplo da abaixo, separamos a criação do iterador do uso do iterador no loop ``for``. Quando o loop ``for`` é chamado usando o iterador em ``v1_iter``, cada elemento no iterador é usado em uma iteração do loop, que imprime cada valor.

```
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();

    for val in v1_iter {
        println!("Got: {}", val);
    }
```

Em linguagens que não possuem iteradores fornecidos por suas bibliotecas padrão, você provavelmente escreveria essa mesma funcionalidade iniciando uma variável no índice 0, usando essa variável para indexar no vetor para obter um valor e incrementando o valor da variável em um loop até atingir o número total de itens do vetor.

Os iteradores lidam com toda essa lógica para você, reduzindo o código repetitivo que você poderia estragar. **Os iteradores oferecem mais flexibilidade para usar a mesma lógica com muitos tipos diferentes de sequências, não apenas estruturas de dados nas quais você pode indexar, como vetores. Vamos examinar como os iteradores fazem isso.**

## O ``Iterator`` Trait e o próximo método

Todos os iteradores implementam uma característica chamada ``Iterator`` que é definida na biblioteca padrão. A definição do traço é assim:

```
pub trait ``Iterator`` {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
    // methods with default implementations elided
}

```

Observe que esta definição usa uma nova sintaxe: ``type Item`` e ``Self::Item``, que estão definindo um tipo associado a esta característica. Se você desejar saber mais sobre esse tópico, você pode aprender mais clicando[Aqui](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html) - Advanced Traits. Por enquanto, tudo o que você precisa saber é que este código diz que a implementação da característica ````Iterator```` requer que você também defina um tipo de ``Item``, e esse tipo de ``Item`` é usado no tipo de retorno de o próximo método. Em outras palavras, o tipo ``Item`` será o tipo retornado do iterador.

A característica ````Iterator```` requer apenas que os implementadores definam um método: o ``next`` método, que retorna um item do iterador por vez agrupado em ``Some`` e, quando a iteração termina, retorna ``None``.

Podemos chamar o próximo método diretamente nos iteradores; O código abaixo demonstra quais valores são retornados de chamadas repetidas para ``next`` no iterador criado a partir do vetor.

```
#[test]
fn ``iterator``_demonstration() {
    let v1 = vec![1, 2, 3];
    let mut v1_iter = v1.iter();
    assert_eq!(v1_iter.next(), Some(&1));
    assert_eq!(v1_iter.next(), Some(&2));
    assert_eq!(v1_iter.next(), Some(&3));
    assert_eq!(v1_iter.next(), None);
}
```

Observe que precisávamos tornar ``v1_iter`` mutável: chamar o próximo método em um iterador altera o estado interno que o iterador usa para acompanhar onde ele está na sequência. Em outras palavras, esse código consome, ou esgota, o iterador. Cada chamada para ``next`` consome um item do iterador. Não precisamos tornar ``v1_iter`` mutável quando usamos um loop ``for`` porque o loop se apropriou de ``v1_iter`` e o tornou mutável nos bastidores.

Observe também que os valores que obtemos das chamadas para ``next`` são referências imutáveis ​​aos valores no vetor. O método ``iter`` produz um iterador sobre referências imutáveis. Se quisermos criar um iterador que tome posse de v1 e retorne valores próprios, podemos chamar ``into_iter`` em vez de ``iter``. Da mesma forma, se quisermos iterar sobre referências mutáveis, podemos chamar ``iter_mut`` em vez de ``iter``.

## Métodos que consomem o iterador

O atributo ``Iterator`` possui vários métodos diferentes com implementações padrão fornecidas pela biblioteca padrão; você pode saber mais sobre esses métodos procurando na documentação da API da biblioteca padrão para o traço ``Iterator``. Alguns desses métodos chamam o próximo método em sua definição, e é por isso que você precisa implementar o próximo método ao implementar o atributo ``Iterator``.

Os métodos que chamam a seguir são chamados de adaptadores de consumo, porque chamá-los consome o iterador. Um exemplo é o método ``sum``, que se apropria do iterador e itera pelos itens chamando repetidamente ``next``, consumindo assim o iterador. À medida que itera, ele adiciona cada item a um total corrente e retorna o total quando a iteração é concluída. O código abaixo tem um teste que ilustra o uso do método ``sum``:

```
#[test]
fn iterator_sum() {
    let v1 = vec![1, 2, 3];
    let v1_iter = v1.iter();
    let total: i32 = v1_iter.sum();
    assert_eq!(total, 6);
}
```

Não temos permissão para usar v1_iter após a chamada para sum porque sum assume a propriedade do iterador em que o chamamos.

## Métodos que produzem outros iteradores

Os adaptadores do iterador são métodos definidos na característica do iterador que não consomem o iterador. Em vez disso, eles produzem diferentes iteradores alterando algum aspecto do iterador original.

Um exemplo de chamada do mapa do método do adaptador do iterador, que usa um **closure** para chamar cada item à medida que os itens são iterados. O método ``map`` retorna um novo iterador que produz os itens modificados. O ``closure`` aqui cria um novo iterador no qual cada item do vetor será incrementado em 1:

```
let v1: Vec<i32> = vec![1, 2, 3];
v1.iter().map(|x| x + 1);
```

Para corrigir esse aviso (Warning) e consumir o iterador, usaremos o método ``collect``, que, como o nome diz, coleta os dados. Esse método consome o iterador e coleta os valores resultantes em um tipo de dados de coleção.

No código abaixo, coletamos os resultados da iteração sobre o iterador que é retornado da chamada para mapear em um vetor. Esse vetor acabará contendo cada item do vetor original incrementado em 1.

```
    let v1: Vec<i32> = vec![1, 2, 3];

    let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

    assert_eq!(v2, vec![2, 3, 4]);

```

Como ``map`` aceita um closure, podemos especificar qualquer operação que desejamos executar em cada item. Este é um ótimo exemplo de como os closure permitem que você personalize algum comportamento enquanto reutiliza o comportamento de iteração que o atributo Iterator fornece.

Você pode encadear várias chamadas para adaptadores iteradores para executar ações complexas de maneira legível. Mas como todos os iteradores são preguiçosos, você precisa chamar um dos métodos de adaptador de consumo para obter resultados de chamadas para adaptadores de iterador.

Um exemplo mais aplicavél no dia-a-dia seria para abrir um arquivo .txt e ler o que está escrito. Odemos utilizar outros m~etodos, mas com iteradores é possível fazer isso demodo simple e eficaz.

```
use std::fs::File;
use std::io::{BufRead, BufReader};

fn main() {
    // abra o arquivo para leitura usando um `BufReader` para ler linhas
    let file = File::open("arquivo.txt").unwrap();
    let reader = BufReader::new(file);

    // crie um iterador que lê cada linha do arquivo
    let lines = reader.lines().map(|line| line.unwrap());

    // itere sobre as linhas e exiba cada uma
    for line in lines {
        println!("{}", line);
    }
}
```

Outro exemplo mais ligada a parte estatística é:

```
fn main() {
    let numbers = vec![1.0, 2.0, 3.0, 4.0, 5.0];
    let n = numbers.len() as f64;

    // calcula a média dos números usando um iterador
    let mean = numbers.iter().sum::<f64>() / n;

    // calcula o desvio padrão dos números usando um iterador
    let variance = numbers.iter().map(|x| (x - mean).powi(2)).sum::<f64>() / n;
    let stddev = variance.sqrt();

    println!("Média: {}", mean);
    println!("Desvio padrão: {}", stddev);
}

```

## Usando closures que capturam seu ambiente

Muitos adaptadores iteradores aceitam closures como argumentos e, geralmente, os closures que especificaremos como argumentos para adaptadores iteradores serão closures que capturam seu ambiente.

Para este exemplo, usaremos o método ``filter`` que recebe um closure. O closure obtém um item do iterador e retorna um bool. Se o closure retornar true, o valor será incluído na iteração produzida pelo filtro. Se o closure retorna falso, o valor não será incluído.

Veja o exemplo abaixo:

```
#[derive(PartialEq, Debug)]
struct Shoe {
    size: u32,
    style: String,
}

fn shoes_in_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
    shoes.into_iter().filter(|s| s.size == shoe_size).collect()
}

//... 
// DADOS DE SAPATOS
//...
// Esse código pega somente aqueles sapatos cujo o tamnho é aquele especificado em shoes_in_size. 
```

A função ``shoes_in_size`` toma posse de um vetor de sapatos e um tamanho de sapato como parâmetros. Ele retorna um vetor contendo apenas sapatos do tamanho especificado.

No corpo de ``shoes_in_size``, chamamos ``into_iter`` para criar um iterador que se aproprie do vetor. Em seguida, chamamos o filtro para adaptar esse iterador em um novo iterador que **contém apenas elementos para os quais o closure retorna true**.

O closure captura o parâmetro ``shoe_size`` do ambiente e compara o valor com o tamanho de cada sapato, mantendo apenas os sapatos do tamanho especificado. Por fim, chamar ``collect`` reúne os valores retornados pelo iterador adaptado em um vetor que é retornado pela função.

O teste mostra que quando chamamos ``shoes_in_size``, recebemos de volta apenas sapatos que tenham o mesmo tamanho que o valor que especificamos.

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/4GcKrj4By8k/0.jpg)](https://www.youtube.com/watch?v=4GcKrj4By8k)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD32/README.md)

## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Processing a Series of Items with Iterators. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch13-02-iterators.html>. Acesso em 11/05/2023

[2] - BING. Conversa com o assistente de busca Bing. [S.l.], 11 jun. 2023. Disponível em: https://www.bing.com. Acesso em: 11 jun. 2023.