# **Slices**

**APLICAÇÃO 01** - Primeira Metade:

```
fn first_half(numbers: &[i32]) -> &[i32] {
    let mid = numbers.len() / 2;
    &numbers[..mid]
}

fn main() {
    let numbers = [1, 2, 3, 4, 5, 6];
    let result = first_half(&numbers);
    println!("Primeira metade: {:?}", result);
}
```

**APLICAÇÃO 02** - Entendo Slices:

```
use std::mem;

// Esta função recebe uma slice como parâmetro.
fn analisar_slice(slice: &[i32]) {
    println!("Primeiro elemento da slice: {}", slice[0]);
    println!("A slice tem {} elementos", slice.len());
}

fn main() {
    // Array de tamanho fixo (a assinatura de tipo é desnecessária).
    let xs: [i32; 5] = [1, 2, 3, 4, 5];

    // Todos os elementos podem ser inicializados com o mesmo valor.
    let ys: [i32; 500] = [0; 500];

    // A indexação começa em 0.
    println!("Primeiro elemento do array: {}", xs[0]);
    println!("Segundo elemento do array: {}", xs[1]);

    // `len` retorna a quantidade de elementos no array.
    println!("Número de elementos no array: {}", xs.len());

    // Arrays são alocados na pilha.
    println!("O array ocupa {} bytes", mem::size_of_val(&xs));

    // Arrays podem ser automaticamente emprestados como slices.
    println!("Emprestar o array inteiro como uma slice.");
    analisar_slice(&xs);

    // Slices podem apontar para uma seção de um array.
    // Elas têm a forma [indice_inicial..indice_final].
    // `indice_inicial` é a primeira posição na slice.
    // `indice_final` é uma posição a mais que a última posição na slice.
    println!("Emprestar uma seção do array como uma slice.");
    analisar_slice(&ys[1 .. 4]);
}
```

