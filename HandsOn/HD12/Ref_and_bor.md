# **Exercícios de Referências e Empréstimos**

* EXERCÍCIOS PROPOSTOS: 

- O que é uma referência em Rust e como ela é criada?
- Qual é a diferença entre uma referência mutável e uma referência imutável?
- O que é o conceito de empréstimo (borrowing) em Rust e como ele se relaciona com as regras de propriedade?


**APLICAÇÃO 01** - Empréstimo.

```
fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);
    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

Neste exemplo, a função calculate_length pega uma referência para uma String como argumento ao invés de tomar posse do valor. Isso permite que a função main continue usando a variável s1 depois que a função calculate_length é chamada.

**APLICAÇÃO 02** - Swap de valores:

```
fn swap(a: &mut i32, b: &mut i32) {
    let temp = *a;
    *a = *b;
    *b = temp;
}

fn main() {
    let mut x = 5;
    let mut y = 10;
    println!("x: {}, y: {}", x, y);
    swap(&mut x, &mut y);
    println!("x: {}, y: {}", x, y);
}
```

**APLICAÇÃO 03** - Contador de palavras:

```
use std::io;

fn count_words(s: &str) -> usize {
    s.split_whitespace().count()
}

fn main() {
    let mut input = String::new();
    println!("Digite uma frase:");
    io::stdin().read_line(&mut input).unwrap();
    let word_count = count_words(&input);
    println!("A frase tem {} palavras.", word_count);
}
```

**APLICAÇÃO 04** - Maior elemento:

```
fn largest(numbers: &[i32]) -> &i32 {
    let mut largest = &numbers[0];
    for number in numbers {
        if number > largest {
            largest = number;
        }
    }
    largest
}

fn main() {
    let numbers = vec![1, 2, 3, 4, 5];
    let result = largest(&numbers);
    println!("O maior número é: {}", result);
}
```

**APLICAÇÃO 05** - Concatenação de strings:

```
fn concatenate(s1: &str, s2: &str) -> String {
    format!("{}{}", s1, s2)
}

fn main() {
    let s1 = "Hello";
    let s2 = "World";
    let result = concatenate(s1, s2);
    println!("{}", result);
}
```

**APLICAÇÃO 06** - Copy():

```
#[derive(Debug, Copy, Clone)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p1 = Point { x: 1, y: 2 };
    let p2 = p1;
    println!("p1: {:?}, p2: {:?}", p1, p2);
}
```

**APLICAÇÃO 07** - Clone():

```
#[derive(Debug, Clone)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p1 = Point { x: 1, y: 2 };
    let p2 = p1.clone();
    println!("p1: {:?}, p2: {:?}", p1, p2);
}
```
___

RESPOSTAS DOS EXERCÍCIOS PROPOSTOS: 

Uma referência em Rust permite que você acesse um valor sem tomar posse dele. Ela é criada usando o operador & antes de uma variável. Por exemplo: ``let x = 5; let y = &x;`` cria uma referência y para o valor de x.

A diferença entre uma referência mutável e uma referência imutável é que a primeira permite que você altere o valor ao qual ela se refere, enquanto a segunda não. Uma referência mutável é criada usando o operador &mut antes de uma variável mutável. Por exemplo: ``let mut x = 5; let y = &mut x;`` cria uma referência mutável y para o valor de x.

O conceito de empréstimo (borrowing) em Rust se refere ao ato de passar uma referência para uma função ou método, permitindo que ela acesse um valor sem tomar posse dele. Isso se relaciona com as regras de propriedade porque, enquanto um valor é emprestado, o dono original não pode modificá-lo ou movê-lo até que o empréstimo termine.