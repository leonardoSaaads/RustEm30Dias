## Matchs

**APLICAÇÕES 01** - Lidando com Options

```
fn quadrado(valor: Option<i32>) -> i32 {
    match valor {
        Some(x) => x * x,
        None => -1,
    }
}

fn main() {
    let x = Some(5);
    let y = None;

    println!("x: {}", quadrado(x)); // 25
    println!("y: {}", quadrado(y)); // -1
}
```

**APLICAÇÕES 02** - Abrindo arquivos

```
use std::fs;
use std::io;

fn ler_arquivo(nome_arquivo: &str) -> Result<String, io::Error> {
    fs::read_to_string(nome_arquivo)
}

fn main() {
    let nome_arquivo = "arquivo.txt";

    match ler_arquivo(nome_arquivo) {
        Ok(conteudo) => println!("Conteúdo do arquivo:\n{}", conteudo),
        Err(erro) => println!("Erro ao ler o arquivo: {}", erro),
    }
}
```

**APLICAÇÕES 03** - Saber se o número é positivo

```
fn descrever_numero(x: i32) -> String {
    match x {
        n if n > 0 => format!("{} é positivo", n),
        n if n < 0 => format!("{} é negativo", n),
        _ => "zero".to_string(),
    }
}

fn main() {
    let x = 5;
    let y = -3;
    let z = 0;

    println!("{}", descrever_numero(x)); // 5 é positivo
    println!("{}", descrever_numero(y)); // -3 é negativo
    println!("{}", descrever_numero(z)); // zero
}
```

**APLICAÇÕES 04** - Mecanica dos fluidos

```
enum Fluido {
    Agua,
    Oleo,
    Ar,
}

enum Regime {
    Laminar,
    Turbulento,
}

fn perda_de_carga(fluido: Fluido, regime: Regime, diametro: f64, velocidade: f64) -> f64 {
    match (fluido, regime) {
        (Fluido::Agua, Regime::Laminar) => 64.0 * velocidade / diametro,
        (Fluido::Agua, Regime::Turbulento) => 0.3164 * velocidade.powf(0.25) / diametro.powf(0.25),
        (Fluido::Oleo, Regime::Laminar) => 128.0 * velocidade / diametro,
        (Fluido::Oleo, Regime::Turbulento) => 0.3164 * velocidade.powf(0.3) / diametro.powf(0.3),
        (Fluido::Ar, Regime::Laminar) => 64.0 * velocidade / diametro,
        (Fluido::Ar, Regime::Turbulento) => 0.3164 * velocidade.powf(0.2) / diametro.powf(0.2),
    }
}

fn main() {
    let fluido = Fluido::Agua;
    let regime = Regime::Turbulento;
    let diametro = 0.1;
    let velocidade = 2.0;

    println!(
        "Perda de carga: {}",
        perda_de_carga(fluido, regime, diametro, velocidade)
    );
}
```


**APLICAÇÕES 05** - Eng. Civil

```
enum Solo {
    Argila,
    Areia,
}

enum Estaca {
    Metalica,
    Concreto,
}

fn capacidade_de_carga(solo: Solo, estaca: Estaca, comprimento: f64) -> f64 {
    match (solo, estaca) {
        (Solo::Argila, Estaca::Metalica) => 1000.0 * comprimento.powf(0.5),
        (Solo::Argila, Estaca::Concreto) => 1200.0 * comprimento.powf(0.5),
        (Solo::Areia, Estaca::Metalica) => 800.0 * comprimento.powf(0.5),
        (Solo::Areia, Estaca::Concreto) => 1000.0 * comprimento.powf(0.5),
    }
}

fn main() {
    let solo = Solo::Argila;
    let estaca = Estaca::Metalica;
    let comprimento = 10.0;

    println!(
        "Capacidade de carga: {}",
        capacidade_de_carga(solo, estaca, comprimento)
    );
}
```

**EXERCÍCIOS PROPOSTOS:**

Crie uma função chamada verdadeiro_ou_falso que receba um valor booleano e retorne uma string "verdadeiro" se o valor for true ou "falso" se o valor for false. Use match para fazer a correspondência de padrões no valor booleano e retornar a string apropriada.

___

**RESPOSTAS:**

```
fn verdadeiro_ou_falso(valor: bool) -> String {
    match valor {
        true => "verdadeiro".to_string(),
        false => "falso".to_string(),
    }
}

fn main() {
    let x = true;
    let y = false;

    println!("{}", verdadeiro_ou_falso(x)); // verdadeiro
    println!("{}", verdadeiro_ou_falso(y)); // falso
}
```