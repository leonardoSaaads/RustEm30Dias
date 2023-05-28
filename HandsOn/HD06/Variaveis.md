# **VARIÁVEIS**

**APLICAÇÃO 01**

```
// Mudando o valor de x
fn main() {
    let mut x = 1;
    x += 2; 
    
    assert_eq!(x, 3);
    println!("Success!");
}
```


**APLICAÇÃO 02**

```
fn main() {
    // Usando namespaces diferentes
    let x: i32 = 10;
    let y: i32 = 5;
    {
        println!("O valor de x é {} e o valor de y é {}", x, y);
    }
    println!("O valor de x é {} e o valor de y é {}", x, y); 
}
```


**APLICAÇÃO 03**

```
fn main() {
    // juntando duas strings
    let x = define_hello();
    println!("{}, world", x); 
}

// O campo 'static você pode ver em lifetimes
// O campo & você pode ver em empréstimos
fn define_hello() -> &'static str {
    let x = "hello";
    return x;
}
```


**APLICAÇÃO 04**

```
fn main() {
    let x: i32 = 5;
    {// O x passa a ser 12 aqui
        let x = 12;
        assert_eq!(x, 12);
    }
    // Nesse namespace o x continua igual a 5
    assert_eq!(x, 5);

    let x = 42;
    // Printa "42".
    println!("{}", x);
}
```


**QUESTÃO  PORPOSTA 05**

* OBJETIVO: Remova uma linha no código para fazer ele compilar

```
fn main() {
    let mut x: i32 = 1;
    x = 7;
    // Shadowing
    let x = x; 
    x += 3;

    let y = 4;
    // Shadowing
    let y = "Eu também posso ser vinculado ao tipo texto!"; 

    println!("Success!");
}

```


**APLICAÇÃO 06**

```
fn main() {
    // criando duas variáveis
    let (mut x, y) = (1, 2);
    x += 2;

    assert_eq!(x, 3);
    assert_eq!(y, 2);

    println!("Success!");
}
```

## Numbers, Char & Bool

**APLICAÇÃO 01**

```
// Lembre-se sempre: "" é usado para string;
//                   '' e´usado para char;
fn main() {
    let c1 = '中';
    print_char(c1);
} 

fn print_char(c : char) {
    println!("{}", c);
}
```


**QUESTÃO PROPOSTA 02**

OBJETIVO: Faça ``println!`` funcionar

```
fn main() {
    let _f: bool = false;
    let t = true;
    if !t {
        println!("Success!");
    }
} 
```

**APLICAÇÃO 03**

```
fn main() {
    // Mostrando a lógica booleana
    let f = false;
    let t = true && false;
    assert_eq!(t, f);

    println!("Success!");
}
```


**APLICAÇÃO 04**

```
// LEMBRE-SE: u32 e i32 utilizam 4 bytes
use std::mem::size_of_val;
fn main() {
    let unit: i32 = 2;
    assert!(size_of_val(&unit) == 4);

    println!("Success!");
}
```


**APLICAÇÃO 06**

```
fn main() {
    // Verificando qual o tipo de x;
    let x = 1_000.000_1;    // ?
    let _y: f32 = 0.12;     // f32
    let _z: f64 = 0.01_f64; // f64
    print_type_of(&x);
}

// Não mexer aqui! 
fn print_type_of<T>(_: &T) {
    println!("{}", std::any::type_name::<T>())
}
```

## Expressões

**APLICAÇÃO 01**

```
// Realizando uma soma de dois números;
fn main() {
    let s = sum(1 , 2);
    assert_eq!(s, 3);

    println!("Success!");
}

fn sum(x: i32, y: i32) -> i32 {
    x + y
}
```


**QUESTÃO PROPOSTA 03**

OBJETIVO: Realize o mínimo de modificações para fazer o programa funcionar.

```
fn main() {
   let v = (let x = 3);

   assert!(v == 3);
   println!("Success!");
}
```

___

RESPOSTA DOS EXERCÍCIOS PROPOSTOS: 

1) PRIMEIRO

```
fn main() {
    let mut x: i32 = 1;
    x = 7;
    // Shadowing
    x += 3;

    let y = 4;
    // Shadowing
    let y = "Eu também posso ser vinculado ao tipo texto!"; 

    println!("Success!");
}
```

2) SEGUNDO

```
// Existem muitas formas de fazer essa questão funcionar.
// A maneira mais simples é apenas usar ! antes de true, pois
// deixará a variável t igual a false.
fn main() {
    let _f: bool = false;
    let t = !true;
    if !t {
        println!("Success!");
    }
} 
```

3) TERCEIRO

```
fn main() {
   let v = (let x = 3);

   assert!(v == 3);
   println!("Success!");
}
```

___

**Exercícios:**

**codewars -> /kata/5a00e05cc374cb34d100000d/train/rust**

OBJETIVO: Crie uma função que retorna uma matriz de inteiros de n a 1 onde n>0.
Exemplo : n=5 --> [5,4,3,2,1]
