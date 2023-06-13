## Strings 

**APLICAÇÕES 01** - Strings como Bytes:

```
fn main() {
    let string1 = "Olá Mundo".to_string();
    let bytes = string1.as_bytes();
    println!("{:?}", bytes);
}

```

**APLICAÇÕES 02** - Usando strings em chinês::

```
fn main() {
    let string1 = "你好，世界".to_string();
    println!("{}", string1);
}

```


**APLICAÇÕES 03** - Marcadores de posição:

```
fn main() {
    let nome = "João";
    let idade = 25;
    let string_formatada = format!("Meu nome é {} e eu tenho {} anos.", nome, idade);
    println!("{}", string_formatada);
}

```

**Aplicação 04** - Usando substrings:

```
fn main() {
    let string1 = "Olá, Mundo, Rust".to_string();
    let substrings: Vec<&str> = string1.split(",").collect();
    println!("{:?}", substrings);
}
```

**EXERCÍCIO PROPOSTO**: Faça um código que receba uma string contendo uma data no formato “dd/mm/aaaa” e deve retornar a data por extenso em português. Por exemplo, a entrada “01/01/2022” deve retornar “1 de janeiro de 2022”.

___

**RESOLUÇÃO**

```
fn main() {
    let data = "01/01/2022";
    let partes: Vec<&str> = data.split("/").collect();
    let dia = partes[0].parse::<i32>().unwrap();
    let mes = match partes[1] {
        "01" => "janeiro",
        "02" => "fevereiro",
        "03" => "março",
        "04" => "abril",
        "05" => "maio",
        "06" => "junho",
        "07" => "julho",
        "08" => "agosto",
        "09" => "setembro",
        "10" => "outubro",
        "11" => "novembro",
        _ => "dezembro"
    };
    let ano = partes[2];
    println!("{} de {} de {}", dia, mes, ano);
}

```