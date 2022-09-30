# **Referencias e Empréstimos(Borrowing)**

Uma referência, como um ponteiro, armazena o endereço de um objeto localizado em outro lugar na memória. Ao contrário de um ponteiro, uma referência depois de inicializada não pode ser feita para se referir a um objeto diferente ou definir como nulo.[1]

Veja, no exemplo aaixo, uma referência para um objeto como parâmetro.

```
fn main() {
    let s1 = String::from("texto");       // Define uma String
    let tamanho = calcula_tamanho(&s1);   // Vê o tamanho da String
    
    // Note que, sem a referência, não seria possível usar o param s1
    println!("O tamanho de '{}' é {}.", s1, tamanho);
}

fn calcula_tamanho(s: &String) -> usize {
    s.len()  // retorna o tamanho da String
}
```

Esses símbolos ``&`` indicam referências, e eles permitem que você se refira a algum valor **sem tomar posse dele**. note que passamos ``&s1`` para ``calcula_tamanho``, e na sua definição, temos ``&String`` em vez de apenas ``String``. Em outras palavras, temos os seguintes passos:

1) ``s1`` aponta para uma memória ``String`` alocada na *heap*.
2) ``tamanho`` utiliza a função ``calcula_tamanho``.
3) ``s``(variável da função ``calcula_tamanho``) recebe como referência ``s1`` (que está apontado para uma memória na *heap*).

**OBS 01:** A sintaxe ``&s1`` nos permite criar uma referência que se refere ao valor ``s1``, mas não o possui. Como ela não o possui, o valor a que ela aponta não será destruído quando a referência sair de escopo (visto no Hands-On anterior). Da mesma forma, a assinatura da função usa ``&String`` para indicar que o tipo do parâmetro ``s`` é uma referência. 

A imagem abaixo mostra isso.

![](/Imagens/HD08/Referencia.png)

Colocar **referências como parâmetros de funções é chamado de borrowing (do inglês, empréstimo)**. Assim como na vida real, se uma pessoa possui alguma coisa, você pode pegar emprestado dela. Quando você termina de usar, você deve devolver.

Algo muito importante a ser dito sobre referências é que ela tem características de imutabilidade. Em Rust, quando uma referência é passada para uma função, não há como alterar, apenas obter caracteísticas daquela entidade passada. O Exemplo abaixo irá mostrar:

```
fn main() {
    let s = String::from("texto");

    modifica(&s);
}

fn modifica(uma_string: &String) {
    uma_string.push_str(" longo");
}
```

a função ``modifica`` tenta mudar a string ``s``, mas como dito anteriormente, não há como mudar a referência. O compilador Rust identifica esse erro e sugere a modificação:

```
7 | fn modifica(uma_string: &String) {
  |                         ------- use `&mut String` here to make mutable
8 |     uma_string.push_str(" longo");
  |     ^^^^^^^^^^ cannot borrow as mutable
```

Com isso, entramos em outro ponto - nas referências mutáveis.

## Referências Mutáveis

Como o nome já diz, referências mutáveis podem mudar uma dada variável. São importantes em contextos em que voçê não deseja ter posse de uma variável, mas precisa realizar uma dada transformação.

O exemplo anterior pode ser refeito da seguinte maneira:

```
fn main() {
    // variável s é mutável
    let mut s = String::from("texto");
    modifica(&mut s);
}

    // pega uma referência mutável.
fn modifica(uma_string: &mut String) {
    uma_string.push_str(" longo");
}
```




## REFERÊNCIAS BIBLIOGRÁFICAS

Referências (C++) - Microsoft Ignite. Disponível em: <https://learn.microsoft.com/pt-br/cpp/cpp/references-cpp?view=msvc-170>. Acesso em 28/09/2022.