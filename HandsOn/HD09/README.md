# **Slices (Fatias)**

Semelhantemente ao Python, Rust possui a capacidade de manipulação de Strings, Arrays e outros objetos. Essa capacidade de manipulação de "fatias" de dados tornam a linguagem mais dinâmica e possibilita que fatias de dados possam ser extraidos. Um exemplo disso é quando pegamos a primeira palavra de uma String e a tornamos no formato Title (primeira palavra maiúscula).

Uma slice de string é uma referência para uma parte de uma String, e tem a seguinte forma:

```
fn main() {
let s = String::from("texto longo");

let texto = &s[0..5];
let longo = &s[6..11];
}
```

Isto é similar a pegar uma referência à String inteira, mas com uma fatia de 0 à 5. Em vez de uma referência à String inteira, trata-se de uma referência a uma porção da String. A sintaxe é a seguinte: ``início..fim`` representa um range (uma faixa) que começa em ``início`` e continua até, mas não incluindo, ``fim``.

![](/Imagens/HD09/Slice.png)

Com a sintaxe de range do Rust (``..``), se você quiser começar com o primeiro elemento (índice zero), você pode omitir o valor antes dos dois pontos. Em outras palavras, estas formas são equivalentes:

```
fn main() {
let s = String::from("texto");

let slice = &s[0..2];   // opção 1
let slice = &s[..2];    // opção 2 (mesmo resultado)
}
```

Outra forma de selecionar a string inteira é utilizando Slice; Logo, essas duas formas são equivalentes:

```
fn main() {
let s = String::from("texto");

let tamanho = s.len();

let slice = &s[0..tamanho];
let slice = &s[..];
}
```



