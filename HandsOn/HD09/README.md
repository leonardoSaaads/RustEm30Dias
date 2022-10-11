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

Slices são poderosos em manipualção de dados. Podemos utilizar eles para separar fatias de dados dentro de Strings. No exemplo abaixo, iremos criar uma função que retorna a primeira palavra.

```
fn main(){
    let texto = String::from("Belíssimo dia, meu amigo!!!");
    let primeira = primeira_palavra(&texto);
    
    println!("{}", primeira)
}

fn primeira_palavra(s: &String) -> &str {
    // convete cada letra em um byte e adiciona em um array. Exemplo: "papel" -> [112, 97, 112, 101, 108];
    let bytes = s.as_bytes();  

    // laço for sobre um array de bytes que possui o índice e o item.
    // Caso o item seja igual ao espaço, a primeira palavra tem o índice equivalente 
    // ao local de onde estava o primeiro espaço.
    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];  // retorna esse resultado desse escopo
        }
    }
    // Utiliza o resultado retornado como retorno da função.
    &s[..]
}
```

Neste código, foram introduzido algum operadores como ``iter()`` e ``enumerate()``. Neste momento, você não precisa se preocupar com isso, mas apenar ter em mente que o operador ``iter()`` retorna cada elemento de um vetor/array e que o operador ``enumarate()``, semelhante ao comando em python, cria uma tupla contendo o índice e o item. O primeiro elemento da tupla é o índice, e o segundo elemento é uma referência ao valor. 

Além de ``Strings``, podemos utilizar slices em outros tipos de dados. Se tomarmos um ``array``, por exemplo, consguimos manipular suas informações igualmente. No código abaixo, pegaremos uma parte de um ``array`` e iremos pegar um intervalo de dados:

```
fn main() {
let x = [0, 1, 2, 3, 4, 5];
let slice = &x[3..1];

println!("{:?}", slice);  // Modelo de print para array.
}
```

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️ [Clique Aqui](/HandsOn/HD10/README.md)

## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Slices. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch04-03-slices.html>. Acesso em 07/10/2022.
