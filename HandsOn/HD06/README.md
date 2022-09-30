# **EXERCICIOS 01**

Para realizar essa série de exercícios, recomandamos que você utilize o site [Rust Playground](https://play.rust-lang.org/). Nesse site, você pode utilizar os códigos sem que seja necessário a implementação em seu computador. Se torna divertido e fácil para testar funcionalidades e exercícios.

![](/Imagens/HD06/RustPlayground.png)

Com isso, agora podemos iniciar os exercícios sem amiores esforços. Além disso, quase todos os exercícios foram retirados do livro **Rust by Practice** - disponível aqui [Rust by Practice](https://practice.rs/why-exercise.html)

Vamos lá!!

## VARIÁVEIS

**QUESTÃO 01**

```
// PREENCHA OS ESPAÇOS EM BRANCO PARA FAZER O CÓDIGO COMPILAR
fn main() {
    let __ x = 1;
    __ += 2; 
    
    assert_eq!(x, 3);
    println!("Success!");
}
```


**QUESTÃO 02**

```

// Corrija o erro abaixo com a menor quantidade de modificação.
fn main() {
    let x: i32 = 10;
    {
        let y: i32 = 5;
        println!("O valor de x é {} e o valor de y é {}", x, y);
    }
    println!("O valor de x é {} e o valor de y é {}", x, y); 
}
```


**QUESTÃO 03**

```
// Corrija o erro com o uso da função define_hello
fn main() {
    println!("{}, world", x); 
}

fn define_hello() {
    let x = "hello";
}

```


**QUESTÃO 04**

```
// Apenas modifique a posição do `assert_eq!` para fazer o `println!` funcionar.
fn main() {
    let x: i32 = 5;
    {
        let x = 12;
        assert_eq!(x, 5);
    }

    assert_eq!(x, 12);

    let x = 42;
    println!("{}", x); // Printa "42".
}
```


**QUESTÃO 05**

```
// Remova uma linha no código para fazer ele compilar
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


**QUESTÃO 06**

```
// Corrija o erro abaixo com a menor quantidade de modificações
fn main() {
    let (x, y) = (1, 2);
    x += 2;

    assert_eq!(x, 3);
    assert_eq!(y, 2);

    println!("Success!");
}
```

## Numbers, Char & Bool

**QUESTÃO 01**

```
// Realize uma mudança e faça o código funcionar. (OBS: '' == "" ?)
fn main() {
    let c1 = "中";
    print_char(c1);
} 

fn print_char(c : char) {
    println!("{}", c);
}
```


**QUESTÃO 02**

```
// Faça println! funcionar
fn main() {
    let _f: bool = false;

    let t = true;
    if !t {
        println!("Success!");
    }
} 
```

**QUESTÃO 03**

```
// Faça o mínimo de modificaçãos no programa para que ele funcione
fn main() {
    let f = true;
    let t = true && false;
    assert_eq!(t, f);

    println!("Success!");
}
```


**QUESTÃO 04**

```
// Faça o programa funcionar, sem modificar a função `implicitly_ret_unit` !
fn main() {
    let _v: () = ();

    let v = (2, 3);
    assert_eq!(v, implicitly_ret_unit());

    println!("Success!");
}

fn implicitly_ret_unit() {
    println!("I will return a ()");
}

// Não utilize essa função
fn explicitly_ret_unit() -> () {
    println!("I will return a ()");
}
```

**QUESTÃO 05**

```
// Modifique `4` para fazer o código funcionar (Dica: Qual é o tamanho do tipo dessa unidade?)
use std::mem::size_of_val;
fn main() {
    let unit: () = ();
    assert!(size_of_val(&unit) == 4);

    println!("Success!");
}
```


**QUESTÃO 06**

```
//  Troque ? pela sua resposta de qual tipo a variável x deve ser.
// Em seguida, rode o código e veja se você acertou.

fn main() {
    let x = 1_000.000_1;   // ?
    let y: f32 = 0.12;     // f32
    let z: f64 = 0.01_f64; // f64

    print_type_of(&x);
}

// Não mexer aqui! 
fn print_type_of<T>(_: &T) {
    println!("{}", std::any::type_name::<T>())
}
```


## Expressões

**QUESTÃO 01**

```
// // Realize o mínimo de modificações para fazer o programa funcionar.
fn main() {
    let s = sum(1 , 2);
    assert_eq!(s, 3);

    println!("Success!");
}

fn sum(x: i32, y: i32) -> i32 {
    x + y;
}
```


**QUESTÃO 02**

```
// Realize o mínimo de modificações para fazer o programa funcionar.
fn main() {
   let v = (let x = 3);

   assert!(v == 3);

   println!("Success!");
}
```

## Funções

**QUESTÃO 01**

```
// Implemente uma função que calcula o fatorial de um número inteiro
fn main() {
    let x: i32 = 1;
    let y: i32 = 3;
    let z: i32 = 8;
    let final_test = 32;

    assert!(fatorial(x) == 1);
    assert!(fatorial(y) == 6);
    assert!(fatorial(z) == 40320);
    assert!(fatorial(final_test) == 39916800);
    println!("Success!");
}

//faça sua função aqui
fn fatorial(num: i32) -> i32{

}
```

**QUASTÃO 02**

```
// Troque i32 por um outro tipo
fn main() {
   print();
}

fn print() -> i32 {
   println!("Success!");
}
```

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️ [Clique Aqui](/HandsOn/HD07/README.md)
