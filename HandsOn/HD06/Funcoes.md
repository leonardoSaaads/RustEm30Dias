# **Funções**

**APLICAÇÃO 01**

```
// Uma função que calcula o fatorial de um número inteiro
fn main() {
    let x: u32 = 1;
    let y: u32 = 3;
    let z: u32 = 8;
    let final_test = 11;
    assert!(fatorial(x) == 1);
    assert!(fatorial(y) == 6);
    assert!(fatorial(z) == 40320);
    assert!(fatorial(final_test) == 39916800);
    println!("Success!");
}

fn fatorial(n: u32) -> u32 {
    if n == 0 {
        1
    } else {
        n * fatorial(n - 1)
    }
}
```

**APLICAÇÃO 02**

```
// Uma função em Rust que tome dois inteiros como argumentos e retorne a soma dos dois inteiros.

fn add(a: i32, b: i32) -> i32 {
   a + b
}
```

**APLICAÇÃO 03**

```
\\ Uma função no Rust que usa uma cadeia de caracteres como argumento e retorna a cadeia de caracteres em todas as letras maiúsculas.

fn to_uppercase(s: &str) -> String {
    s.to_uppercase()
}
```

**APLICAÇÃO 04**

```
// Uma função no Rust que tome uma cadeia de caracteres como argumento e retorne o número de caracteres na cadeia de caracteres.

fn count_characters(s: &str) -> usize {
    let mut count = 0;
    for i in s.chars() {
        count += 1;
    }
    count
}
```

**APLICAÇÃO PROPOSTA 01**

Pergunta: Escreva uma função no Rust que tome um número de telefone como uma cadeia de caracteres e retorne true se o número de telefone for válido e falso caso contrário. Um número de telefone válido é uma cadeia de caracteres de 10 dígitos.

(DICA: Você pode usar ``.is_digit(10)`` para verificar se um número vai se 0 até 9)



___

RESPOSTA DOS EXERCÍCIOS PROPOSTOS: 

1) PRIMEIRO

```
fn is_valid_phone_number(phone_number: &str) -> bool {
    if phone_number.len() != 10 {
        return false;
    }
    for c in phone_number.chars() {
        if !c.is_digit(10) {
            return false;
        }
    }
    true
}
```