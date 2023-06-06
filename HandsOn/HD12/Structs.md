# **Structs**

EXERCÍCIOS PROPOSTOS:

**01** - Criar uma struct para representar um ponto em um espaço tridimensional e implementar funções para calcular a distância entre dois pontos.

**02** - Criar uma struct para representar uma data (dia, mês e ano) e implementar funções para validar a data e calcular a diferença em dias entre duas datas.

**03** - Criar uma struct para representar um retângulo e implementar funções para calcular sua área e perímetro.

**04** - Criar uma struct para representar um estudante com nome, idade e notas e implementar funções para calcular a média das notas e verificar se o estudante foi aprovado ou reprovado. (critério para passar: média maior que 7)

**05** - Criar uma struct para representar um carro com marca, modelo, ano e quilometragem e implementar funções para calcular o valor de revenda do carro com base em sua idade e quilometragem.

___

RESPOSTAS DOS EXERCÍCIOS:

```
\\ 01

use std::f64;

struct Ponto3D {
    x: f64,
    y: f64,
    z: f64,
}

impl Ponto3D {
    fn distancia(&self, outro: &Ponto3D) -> f64 {
        let dx = self.x - outro.x;
        let dy = self.y - outro.y;
        let dz = self.z - outro.z;
        f64::sqrt(dx * dx + dy * dy + dz * dz)
    }
}

fn main() {
    let p1 = Ponto3D { x: 1.0, y: 2.0, z: 3.0 };
    let p2 = Ponto3D { x: 4.0, y: 5.0, z: 6.0 };
    println!("A distância entre p1 e p2 é {}", p1.distancia(&p2));
}

```

**02**

```
\\02 

struct Data {
    dia: u32,
    mes: u32,
    ano: u32,
}

impl Data {
    // Função para verificar se a data é válida
    fn eh_valida(&self) -> bool {
        // Verifica se o mês está entre 1 e 12
        if self.mes < 1 || self.mes > 12 {
            return false;
        }

        // Calcula o número de dias no mês
        let dias_no_mes = match self.mes {
            4 | 6 | 9 | 11 => 30,
            2 if self.eh_ano_bissexto() => 29,
            2 => 28,
            _ => 31,
        };

        // Verifica se o dia está entre 1 e o número de dias no mês
        if self.dia < 1 || self.dia > dias_no_mes {
            return false;
        }

        true
    }

    // Função para verificar se o ano é bissexto
    fn eh_ano_bissexto(&self) -> bool {
        (self.ano % 4 == 0 && self.ano % 100 != 0) || (self.ano % 400 == 0)
    }

    // Função para calcular a diferença em dias entre duas datas
    fn diferenca_em_dias(&self, outra: &Data) -> i32 {
        // Função auxiliar para calcular o número de dias desde o ano 0 até a data
        let dias_desde_cristo = |data: &Data| -> i32 {
            let mut dias = data.dia as i32;

            for mes in 1..data.mes {
                dias += match mes {
                    4 | 6 | 9 | 11 => 30,
                    2 if data.eh_ano_bissexto() => 29,
                    2 => 28,
                    _ => 31,
                };
            }

            dias + data.ano as i32 * 365 + (data.ano as i32 -1) /4 - (data.ano as i32 -1) /100 + (data.ano as i32 -1) /400
        };

        // Calcula a diferença em dias entre as duas datas
        dias_desde_cristo(self) - dias_desde_cristo(outra)
    }
}

fn main() {
    let d1 = Data { dia:10, mes:10, ano:2022 };
    let d2 = Data { dia:20, mes:10, ano:2022 };
    
    println!("A diferença em dias entre d1 e d2 é {}", d1.diferenca_em_dias(&d2));
}

```

**03**

```
// 03
struct Retangulo {
    largura: u32,
    altura: u32,
}

impl Retangulo {
    // Função para calcular a área do retângulo
    fn area(&self) -> u32 {
        self.largura * self.altura
    }

    // Função para calcular o perímetro do retângulo
    fn perimetro(&self) -> u32 {
        2 * (self.largura + self.altura)
    }
}

fn main() {
    let retangulo = Retangulo { largura: 10, altura: 20 };
    
    println!("A área do retângulo é {}", retangulo.area());
    println!("O perímetro do retângulo é {}", retangulo.perimetro());
}
```

**04**

```
// 04

struct Estudante {
    nome: String,
    idade: u32,
    notas: Vec<f64>,
}

impl Estudante {
    // Função para calcular a média das notas do estudante
    fn media(&self) -> f64 {
        let soma: f64 = self.notas.iter().sum();
        soma / self.notas.len() as f64
    }

    // Função para verificar se o estudante foi aprovado ou reprovado
    fn aprovado(&self) -> bool {
        self.media() >= 7.0
    }
}

fn main() {
    let estudante = Estudante { nome: "João".to_string(), idade: 20, notas: vec![8.0, 9.0, 7.5] };
    
    println!("A média das notas do estudante é {}", estudante.media());
    
    if estudante.aprovado() {
        println!("O estudante foi aprovado!");
    } else {
        println!("O estudante foi reprovado!");
    }
}
```

**05**

```
// 05

use std::cmp;

struct Carro {
    marca: String,
    modelo: String,
    ano: u32,
    quilometragem: u32,
}

impl Carro {
    // Função para calcular o valor de revenda do carro com base em sua idade e quilometragem
    fn valor_revenda(&self, ano_atual: u32) -> f64 {
        let idade = ano_atual - self.ano;
        let mut valor = 20_000.0;

        // Desvalorização por idade
        valor -= 1_000.0 * idade as f64;

        // Desvalorização por quilometragem
        valor -= 0.2 * self.quilometragem as f64;

        // Valor mínimo de revenda é 5.000
        cmp::max(valor as i32, 5_000) as f64
    }
}

fn main() {
    let carro = Carro { marca: "Toyota".to_string(), modelo: "Corolla".to_string(), ano: 2015, quilometragem: 60_000 };
    
    println!("O valor de revenda do carro é R${:.2}", carro.valor_revenda(2022));
}
```