## Modulos

**APLICAÇÃO 01** - Geometria

Este exemplo mostra como criar um módulo chamado geometria que contém funções para calcular a área de diferentes formas geométricas. O módulo é definido em um arquivo separado chamado geometria.rs e é usado no arquivo main.rs:

```
// geometria.rs
pub fn area_retangulo(largura: f64, altura: f64) -> f64 {
    largura * altura
}

pub fn area_circulo(raio: f64) -> f64 {
    std::f64::consts::PI * raio * raio
}
```

na função main:

```
// main.rs
mod geometria;

fn main() {
    let largura = 3.0;
    let altura = 4.0;
    println!(
        "Área do retângulo: {}",
        geometria::area_retangulo(largura, altura)
    );

    let raio = 2.0;
    println!("Área do círculo: {}", geometria::area_circulo(raio));
}
```

**APLICAÇÃO 01** - matemática

Este exemplo mostra como criar um módulo chamado matematica que contém funções para realizar operações matemáticas básicas. O módulo é definido diretamente no arquivo main.rs e é usado na função main:

```
mod matematica {
    pub fn somar(x: i32, y: i32) -> i32 {
        x + y
    }

    pub fn subtrair(x: i32, y: i32) -> i32 {
        x - y
    }

    pub fn multiplicar(x: i32, y: i32) -> i32 {
        x * y
    }

    pub fn dividir(x: i32, y: i32) -> i32 {
        x / y
    }
}

fn main() {
    let x = 10;
    let y = 5;

    println!("{} + {} = {}", x, y, matematica::somar(x, y));
    println!("{} - {} = {}", x, y, matematica::subtrair(x, y));
    println!("{} * {} = {}", x, y, matematica::multiplicar(x, y));
    println!("{} / {} = {}", x, y, matematica::dividir(x, y));
}
```

**EXERCÍCIO PROPOSTO PARA APRENDIZADO**

Neste exercício, você usará o módulo rand para gerar números aleatórios em Rust. O módulo rand não faz parte da biblioteca padrão do Rust, então você precisará adicioná-lo como uma dependência no arquivo Cargo.toml do seu projeto.

Abra o arquivo Cargo.toml do seu projeto e adicione a seguinte linha na seção

```
[dependencies]:
rand = "0.8.4"
```

Salve o arquivo Cargo.toml e execute o comando ``cargo build`` no terminal para baixar e compilar a dependência ``rand``. Abra o arquivo ``main.rs`` do seu projeto e adicione a seguinte linha no início do arquivo para importar o módulo rand:
```
use rand::Rng;
```


Agora você pode usar as funções do módulo rand em seu código. Por exemplo, aqui está um código que gera um número inteiro aleatório entre 1 e 10 e imprime o resultado:

```
fn main() {
    let numero_aleatorio = rand::thread_rng().gen_range(1..=10);
    println!("Número aleatório: {}", numero_aleatorio);
}
```

Execute o comando ``cargo run`` no terminal para compilar e executar o programa. Você deve ver um número inteiro aleatório entre 1 e 10 impresso na tela.