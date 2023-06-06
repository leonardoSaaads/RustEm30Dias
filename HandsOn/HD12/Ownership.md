# **Ownership**

**APLICAÇÃO 01** - Telecomunicações

```
struct Telecommunications {
    data: Vec<u8>,
    destination: String,
}

impl Telecommunications {
    fn send(&self) {
        // código para enviar os dados para o destino especificado
    }
}

fn main() {
    let message = Telecommunications {
        data: vec![1, 2, 3],
        destination: "example.com".to_string(),
    };
    message.send();
}
```

**APLICAÇÃO 02** - Automobilismo

```
struct Car {
    make: String,
    model: String,
    year: u16,
}

impl Car {
    fn new(make: &str, model: &str, year: u16) -> Self {
        Self {
            make: make.to_string(),
            model: model.to_string(),
            year,
        }
    }

    fn print_details(&self) {
        println!("{} {} {}", self.make, self.model, self.year);
    }
}

fn main() {
    let car = Car::new("Ford", "Mustang", 1967);
    car.print_details();
}
```

**APLICAÇÃO 03** - Engenharia civil

```
struct Building {
    name: String,
    height: f32,
    location: String,
}

impl Building {
    fn new(name: &str, height: f32, location: &str) -> Self {
        Self {
            name: name.to_string(),
            height,
            location: location.to_string(),
        }
    }

    fn print_details(&self) {
        println!("{} is {} meters tall and is located in {}", self.name, self.height, self.location);
    }
}

fn main() {
    let building = Building::new("Burj Khalifa", 828.0, "Dubai");
    building.print_details();
}
```

**APLICAÇÃO 04** - Lendo arquivos .json

```
use serde::{Deserialize, Serialize};
use std::fs;

#[derive(Serialize, Deserialize)]
struct Person {
    name: String,
    age: u8,
}

fn main() {
    let data = fs::read_to_string("data.json").expect("Unable to read file");
    let person: Person = serde_json::from_str(&data).expect("JSON was not well-formatted");
    println!("{} is {} years old", person.name, person.age);
}
```

PROPOSTA DE EXERCÍCIO 01 - Defina uma struct chamada `Pessoa` que tenha três campos: `nome`, `idade` e `cidade`. Em seguida, crie uma instância dessa struct e atribua valores aos seus campos. Por fim, imprima os valores dos campos da instância criada.

___

Resposta dos exercícios propostos:

```
// RESPOSTA EXERCÍCIO 01 
struct Pessoa {
    nome: String,
    idade: u8,
    cidade: String,
}

fn main() {
    let pessoa = Pessoa {
        nome: String::from("João"),
        idade: 30,
        cidade: String::from("São Paulo"),
    };

    println!("Nome: {}", pessoa.nome);
    println!("Idade: {}", pessoa.idade);
    println!("Cidade: {}", pessoa.cidade);
}
```