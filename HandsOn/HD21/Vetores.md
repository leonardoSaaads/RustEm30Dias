## Vetores 

**APLICAÇÕES 01** - Criando e acessando elementos de um vetor:

```
fn main() {
    let vetor = vec![1, 2, 3, 4, 5];
    println!("Primeiro elemento: {}", vetor[0]);
    println!("Último elemento: {}", vetor[vetor.len() - 1]);
}

```

**APLICAÇÕES 02** - Adicionando e removendo elementos de um vetor:

```
fn main() {
    let mut vetor = vec![1, 2, 3];
    println!("Vetor original: {:?}", vetor);
    vetor.push(4);
    println!("Vetor após adicionar elemento: {:?}", vetor);
    vetor.pop();
    println!("Vetor após remover elemento: {:?}", vetor);
}

```

**APLICAÇÕES 03** - Filtragem do vetor

```
fn main() {
    let vetor = vec![1, 2, 3, 4, 5];
    let pares: Vec<i32> = vetor.into_iter().filter(|x| x % 2 == 0).collect();
    println!("Números pares: {:?}", pares);
}
```

**APLICAÇÕES 04** - Algoritmo de Ordenação *quicksort*.

```
fn quicksort(vetor: &mut [i32]) {
    if vetor.len() <= 1 {
        return;
    }

    let pivot_index = partition(vetor);

    let (left, right) = vetor.split_at_mut(pivot_index);
    quicksort(left);
    quicksort(&mut right[1..]);
}

fn partition(vetor: &mut [i32]) -> usize {
    let pivot = vetor[vetor.len() - 1];
    let mut i = 0;
    for j in 0..vetor.len() - 1 {
        if vetor[j] <= pivot {
            vetor.swap(i, j);
            i += 1;
        }
    }
    vetor.swap(i, vetor.len() - 1);
    i
}

fn main() {
    let mut vetor = vec![5, 3, 2, 4, 1];
    println!("Vetor original: {:?}", vetor);
    quicksort(&mut vetor);
    println!("Vetor ordenado: {:?}", vetor);
}

```

**EXERCÍCIO PROPOSTO**

Faça um código que receba um vetor de números inteiros e deve retornar o maior e o menor número do vetor. Por exemplo, a entrada [1, 2, 3, 4, 5] deve retornar Maior: 5, Menor: 1.

___

**RESOLUÇÃO**

```
fn main() {
    let vetor = vec![1, 2, 3, 4, 5];
    let mut maior = vetor[0];
    let mut menor = vetor[0];
    for &numero in &vetor {
        if numero > maior {
            maior = numero;
        }
        if numero < menor {
            menor = numero;
        }
    }
    println!("Maior: {}, Menor: {}", maior, menor);
}
```