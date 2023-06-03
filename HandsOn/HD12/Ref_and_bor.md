# **Exercícios de Referências e Empréstimos**

**APLICAÇÃO 01** - Empréstimo.

```
fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);
    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

Neste exemplo, a função calculate_length pega uma referência para uma String como argumento ao invés de tomar posse do valor. Isso permite que a função main continue usando a variável s1 depois que a função calculate_length é chamada.