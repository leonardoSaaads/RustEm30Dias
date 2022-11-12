# **Vetores**

O primeiro tipo de coleção que vamos olhar são ``Vec <T>``, também conhecidos como *vetores*. Vetores permitem armazenar mais de um valor em uma única estrutura de dados que coloca todos os valores um ao lado do outro na memória. Vetores **só podem armazenar valores do mesmo tipo**. Em outras palavras, eles são úteis quando você tem uma lista de itens da mesma espécie, como números enfileirados, letras em uma frase ou dados criados para serem utilizados em uma aplicação de *machine learning*.

## Criando um vetor

Para criarmos um vetor, primeiramente teremos de criar uma variável e especifirmacmos com que tipo de dado estamos lidando. Veja abaixo como criamos um vetor um novo vetor vazio:

```
let v: Vec<i32> = Vec::new();
```

Observe que adicionamos uma anotação de tipo aqui. Como não estamos inserindo nenhum valor nesse vetor, Rust não sabe que tipo de elementos pretendemos armazenar. esse é um ponto importante. Vetores são implementados usando genéricos; Por enquanto, saiba que o tipo Vec<T> fornecido pela biblioteca padrão pode conter qualquer tipo. Quando criamos um vetor para conter um tipo específico, podemos especificar o tipo entre colchetes angulares. Na Listagem acima, dissemos a Rust que o ``Vec<T>`` em ``v`` conterá elementos do tipo ``i32``.

Com mais frequência, você criará um ``Vec<T>`` com valores iniciais e o Rust inferirá o tipo de valor que deseja armazenar, portanto, raramente é necessário fazer essa anotação de tipo. O Rust fornece convenientemente o ``vec!`` macro, que criará um novo vetor que contém os valores que você fornecer. Veja abaixo como criar um novo Vec<i32> que contém os valores 1, 2 e 3. O tipo inteiro é i32 porque esse é o tipo inteiro padrão, conforme discutimos na seção “Tipos de dados”.

```
let v = vec![1, 2, 3];
```

Como demos valores iniciais de ``i32``, Rust pode inferir que o tipo de ``v`` é ``Vec<i32>``, e a anotação de tipo não é necessária. Em seguida, veremos como modificar um vetor.

## Modificando vetores

Para criar um vetor e adicionar elementos a ele, podemos usar o método ``push``. Veja abaixo um exemplo:

```
let mut v = Vec::new();
v.push(5);
v.push(6);
v.push(7);
v.push(8);
```

Como acontece com qualquer variável, se quisermos alterar seu valor, precisamos torná-la mutável usando a palavra-chave ``mut``, conforme discutido inicialemnte. Os números que colocamos dentro são todos do tipo ``i32``, e Rust deduz isso da data, então não precisamos da anotação ``Vec<i32>``.

## Lendo elementos de um vetor

Existem duas maneiras de referenciar um valor armazenado em um vetor: via indexação ou usando o método ``get``. Nos exemplos a seguir, anotamos os tipos de valores que são retornados dessas funções para maior clareza.

```
fn main() {
    let v = vec![1, 2, 3, 4, 5];

    let third: &i32 = &v[2];
    println!("The third element is {}", third);

    let third: Option<&i32> = v.get(2);
    match third {
        Some(third) => println!("The third element is {}", third),
        None => println!("There is no third element."),
    }
}
```

Observe alguns detalhes aqui. Usamos o valor de índice de 2 para obter o terceiro elemento porque os vetores são indexados por número, começando em zero. Usar & e [] nos dá uma referência ao elemento no valor do índice. Quando usamos o método get com o índice passado como argumento, obtemos uma Option<&T> que podemos usar com match.


[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/DSZqIJhkNCM/0.jpg)](https://www.youtube.com/watch?v=DSZqIJhkNCM?t=412)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD19/README.md)

## REFERÊNCIAS BIBLIOGRÁICAS

[1] - 

[2] - 

IMAGEM 01 -