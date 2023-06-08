## Enums

**APLICAÇÕES 01** - Formas Geométricas

```
enum Forma {
    Circulo(f64),
    Retangulo(f64, f64),
    Quadrado(f64),
}

fn area(forma: Forma) -> f64 {
    match forma {
        Forma::Circulo(raio) => std::f64::consts::PI * raio * raio,
        Forma::Retangulo(largura, altura) => largura * altura,
        Forma::Quadrado(lado) => lado * lado,
    }
}

fn main() {
    let c = Forma::Circulo(2.0);
    let r = Forma::Retangulo(2.0, 3.0);
    let q = Forma::Quadrado(2.0);

    println!("Área do círculo: {}", area(c));
    println!("Área do retângulo: {}", area(r));
    println!("Área do quadrado: {}", area(q));
}

```

**APLICAÇÕES 02** - Sistema de Mensagens

```
enum Mensagem {
    Sair,
    Mover { x: i32, y: i32 },
    Escrever(String),
    MudarCor(i32, i32, i32),
}

impl Mensagem {
    fn chamar(&self) {
        match self {
            Mensagem::Sair => println!("Saindo..."),
            Mensagem::Mover { x, y } => println!("Movendo para x: {}, y: {}", x, y),
            Mensagem::Escrever(texto) => println!("{}", texto),
            Mensagem::MudarCor(r, g, b) => println!("Mudando cor para R: {}, G: {}, B: {}", r, g, b),
        }
    }
}

fn main() {
    let m1 = Mensagem::Sair;
    let m2 = Mensagem::Mover { x: 10, y: 20 };
    let m3 = Mensagem::Escrever("Olá!".to_string());
    let m4 = Mensagem::MudarCor(255, 0, 0);

    m1.chamar();
    m2.chamar();
    m3.chamar();
    m4.chamar();
}
```

**APLICAÇÕES 03** - 

```
use std::fmt;

#[derive(Debug)]
enum Erro {
    DivisaoPorZero,
    NumeroNegativo,
    Outro(String),
}

impl fmt::Display for Erro {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            Erro::DivisaoPorZero => write!(f, "Erro: Divisão por zero!"),
            Erro::NumeroNegativo => write!(f, "Erro: Número negativo!"),
            Erro::Outro(mensagem) => write!(f, "Erro: {}", mensagem),
        }
    }
}

fn dividir(a: f64, b: f64) -> Result<f64, Erro> {
    if b == 0.0 {
        Err(Erro::DivisaoPorZero)
    } else {
        Ok(a / b)
    }
}

fn raiz_quadrada(x: f64) -> Result<f64, Erro> {
    if x < 0.0 {
        Err(Erro::NumeroNegativo)
    } else {
        Ok(x.sqrt())
    }
}

fn main() {
    let x = dividir(4.0, 2.0);
    match x {
        Ok(resultado) => println!("Resultado da divisão: {}", resultado),
        Err(erro) => println!("{}", erro),
    }

    let y = dividir(4.0, 0.0);
    match y {
        Ok(resultado) => println!("Resultado da divisão: {}", resultado),
        Err(erro) => println!("{}", erro),
    }

    let z = raiz_quadrada(4.0);
    match z {
        Ok(resultado) => println!("Resultado da raiz quadrada: {}", resultado),
        Err(erro) => println!("{}", erro),
    }

    let w = raiz_quadrada(-4.0);
    match w {
        Ok(resultado) => println!("Resultado da raiz quadrada: {}", resultado),
        Err(erro) => println!("{}", erro),
    }
}
```

Neste exemplo, definimos um enum chamado ``Erro`` que pode representar diferentes tipos de erros que podem ocorrer em nossa aplicação. Também implementamos a ``trait fmt::Display`` para o enum para que possamos exibir mensagens de erro personalizadas para cada tipo de erro.


**EXERCÍCIOS PROPOSTOS:**

Crie uma enum chamada Dispositivo que tenha as seguintes variantes: Celular, Computador, Tablet e Smartwatch. Cada variante deve armazenar informações específicas sobre o dispositivo. Por exemplo, a variante Celular pode armazenar informações sobre o sistema operacional e o tamanho da tela.

Em seguida, crie uma função que receba um vetor de dispositivos e retorne a quantidade de cada tipo de dispositivo presente no vetor. Por exemplo, se o vetor contiver 2 celulares, 1 computador e 1 tablet, a função deve retornar algo como: “Celulares: 2, Computadores: 1, Tablets: 1, Smartwatches: 0”.

Utilize a correspondência de padrões (match) para verificar o tipo de cada dispositivo no vetor e atualizar a contagem de cada tipo.

___

**RESPOSTA DOS EXERCÍCIOS:**

```
enum Dispositivo {
    Celular { sistema_operacional: String, tamanho_tela: f64 },
    Computador { sistema_operacional: String },
    Tablet { sistema_operacional: String },
    Smartwatch { marca: String },
}

fn contar_dispositivos(dispositivos: &[Dispositivo]) -> (u32, u32, u32, u32) {
    let mut celulares = 0;
    let mut computadores = 0;
    let mut tablets = 0;
    let mut smartwatches = 0;

    for dispositivo in dispositivos {
        match dispositivo {
            Dispositivo::Celular { .. } => celulares += 1,
            Dispositivo::Computador { .. } => computadores += 1,
            Dispositivo::Tablet { .. } => tablets += 1,
            Dispositivo::Smartwatch { .. } => smartwatches += 1,
        }
    }

    (celulares, computadores, tablets, smartwatches)
}

fn main() {
    let dispositivos = [
        Dispositivo::Celular {
            sistema_operacional: "Android".to_string(),
            tamanho_tela: 5.5,
        },
        Dispositivo::Computador {
            sistema_operacional: "Windows".to_string(),
        },
        Dispositivo::Tablet {
            sistema_operacional: "iOS".to_string(),
        },
        Dispositivo::Celular {
            sistema_operacional: "iOS".to_string(),
            tamanho_tela: 6.1,
        },
    ];

    let (celulares, computadores, tablets, smartwatches) = contar_dispositivos(&dispositivos);

    println!("Celulares: {}", celulares);
    println!("Computadores: {}", computadores);
    println!("Tablets: {}", tablets);
    println!("Smartwatches: {}", smartwatches);
}

```