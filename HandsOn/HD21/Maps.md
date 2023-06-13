## APLICAÇÕES HASHMAPS

**APLICAÇÕES 01** - Contando a frequência de palavras em um texto

```
use std::collections::HashMap;

fn main() {
    let text = "olá mundo olá";
    let mut map = HashMap::new();
    for word in text.split_whitespace() {
        let count = map.entry(word).or_insert(0);
        *count += 1;
    }
    println!("{:?}", map);
}

```

**APLICAÇÕES 02** - Armazenando uma tabela de preços de produtos

```
use std::collections::HashMap;

fn main() {
    let mut prices = HashMap::new();
    prices.insert("Maçã", 2.50);
    prices.insert("Laranja", 1.80);
    prices.insert("Banana", 1.20);
    println!("{:?}", prices);
}

```

**APLICAÇÕES 03** - Armazenando e acessando configurações de um aplicativo

```
use std::collections::HashMap;

fn main() {
    let mut settings = HashMap::new();
    settings.insert("background_color", "blue");
    settings.insert("font_size", "18");
    println!("{:?}", settings);
}

```

**APLICAÇÕES 04** - Pontuações de jogadores em um jogo

```
use std::collections::HashMap;

fn main() {
    let mut scores = HashMap::new();
    scores.insert("Jogador 1", 10);
    scores.insert("Jogador 2", 20);
    println!("{:?}", scores);
}

```

**APLICAÇÕES 05** - ID de uma empresa

```
use std::collections::HashMap;

#[derive(Debug)]
struct Employee {
    name: String,
    department: String,
    salary: f64,
}

fn main() {
    let mut employees = HashMap::new();
    employees.insert(
        1,
        Employee {
            name: "Alice".to_string(),
            department: "Engenharia".to_string(),
            salary: 80000.0,
        },
    );
    employees.insert(
        2,
        Employee {
            name: "Bob".to_string(),
            department: "Vendas".to_string(),
            salary: 60000.0,
        },
    );
    employees.insert(
        3,
        Employee {
            name: "Charlie".to_string(),
            department: "Marketing".to_string(),
            salary: 70000.0,
        },
    );
    println!("{:#?}", employees);
}
```

Cada funcionário é representado por uma estrutura Employee que contém informações como nome, departamento e salário. O hash map armazena essas estruturas usando um ID de funcionário como chave.

**QUESTÃO PROPOSTA**

Crie uma lista telefônica simples em Rust que permita ao usuário procurar o nome de uma pessoa usando seu número de telefone. Para resolver esta questão, você pode usar um hash map para armazenar a lista telefônica. O hash map deve armazenar pares de valores-chave, onde a chave é o número de telefone de uma pessoa e o valor é o nome dessa pessoa. Você deve escrever uma função que receba como parâmetro uma string representando um número de telefone e retorne o nome da pessoa associada a esse número de telefone. Se o número de telefone não estiver presente na lista telefônica, a função deve retornar uma mensagem indicando que o número de telefone não foi encontrado. Aqui está um exemplo de como a função pode ser chamada: 
```
rust let name = find_name_by_phone_number("555-1234");
println!("{}", name); // Deve imprimir "Alice", por exemplo.
``` 

Tente resolver esta questão por conta própria e veja se consegue criar uma lista telefônica simples usando um hash map em Rust.

___

**RESOLUÇÃO**

```
use std::collections::HashMap;

fn find_name_by_phone_number(phone_book: &HashMap<String, String>, phone_number: &str) -> String {
    match phone_book.get(phone_number) {
        Some(name) => name.to_string(),
        None => "Número de telefone não encontrado".to_string(),
    }
}

fn main() {
    let mut phone_book = HashMap::new();
    phone_book.insert("555-1234".to_string(), "Alice".to_string());
    phone_book.insert("555-5678".to_string(), "Bob".to_string());
    phone_book.insert("555-8765".to_string(), "Charlie".to_string());

    let name = find_name_by_phone_number(&phone_book, "555-1234");
    println!("{}", name); // Deve imprimir "Alice"
}
```