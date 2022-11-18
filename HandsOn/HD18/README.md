# **Vetores**

O primeiro tipo de coleção que vamos olhar são ``Vec <T>``, também conhecidos como *vetores*. Vetores permitem armazenar mais de um valor em uma única estrutura de dados que coloca todos os valores um ao lado do outro na memória. Vetores **só podem armazenar valores do mesmo tipo**. Em outras palavras, eles são úteis quando você tem uma lista de itens da mesma espécie, como números enfileirados, letras em uma frase ou dados criados para serem utilizados em uma aplicação de *machine learning*.

![Vetores](https://qph.cf2.quoracdn.net/main-qimg-50bdb3b5f0e5f324b421c5a79ea968db)

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

Observe alguns detalhes aqui. Usamos o valor de índice de 2 para obter o terceiro elemento porque os vetores são indexados por número, começando em zero. Usar ``&`` e ``[]`` nos dá uma referência ao elemento no valor do índice. Quando usamos o método get com o índice passado como argumento, obtemos uma ``Option<&T>`` que podemos usar com ``match``.

A razão pela qual Rust fornece essas duas maneiras de referenciar um elemento é para que você possa escolher como o programa se comporta quando você tenta usar um valor de índice fora do intervalo de elementos existentes. Como exemplo, vamos ver o que acontece quando temos um vetor de cinco elementos e então tentamos acessar um elemento no índice 100 com cada técnica:

```
fn main() {
    let v = vec![1, 2, 3, 4, 5];

    let does_not_exist = &v[100];
    let does_not_exist = v.get(100);
}
```

O resultado de saída, desconsiderando o aviso de variáveis não utilizadas, será que o primeiro método fará com que o programa entre em pânico (``panic!``, veremos mais na frente) porque faz referência a um elemento inexistente. Este método é melhor usado quando você deseja que seu programa trave se houver uma tentativa de acessar um elemento após o final do vetor.

No segundo método, quando o método ``get`` recebe um índice que está fora do vetor, ele retorna ``None`` sem entrar em pânico. Você usaria este método se o acesso a um elemento além do alcance do vetor pudesse acontecer ocasionalmente em circunstâncias normais. Por exemplo, o índice pode vir de uma pessoa digitando um número. Se eles inserirem acidentalmente um número muito grande e o programa obtiver um valor ``None``, você poderá informar ao usuário quantos itens existem no vetor atual e dar a eles outra chance de inserir um valor válido. Isso seria mais fácil de usar do que travar o programa devido a um erro de digitação!

Quando o programa tem uma referência válida, o verificador de empréstimo aplica as regras de  Referencias e Empréstimos para garantir que essa referência e quaisquer outras referências ao conteúdo do vetor são válidas. Lembre-se da regra que afirma que você não pode ter referências mutáveis ​​e imutáveis ​​no mesmo escopo. Veja o exenplo abaixo:

```
    let mut v = vec![1, 2, 3, 4, 5];

    let first = &v[0];

    v.push(6);

    println!("The first element is: {}", first);
```

Em um primeiro momento, você pode achar que o conteúdo programado está consistente, contudo, quando compilamos e executamos o programa, temos a seguinte saída:

```
  |
4 |     let first = &v[0];
  |                  - immutable borrow occurs here
5 | 
6 |     v.push(6);
  |     ^^^^^^^^^ mutable borrow occurs here
7 | 
8 |     println!("The first element is: {}", first);
  |                                          ----- immutable borrow later used here
```

**Por que uma referência ao primeiro elemento deveria se preocupar com mudanças no final do vetor?** Este erro é devido à maneira como os vetores funcionam: como os vetores colocam os valores um ao lado do outro na memória, adicionar um novo elemento no final do vetor pode exigir a alocação de nova memória e copiar os elementos antigos para o novo espaço, se não houver não há espaço suficiente para colocar todos os elementos próximos uns dos outros onde o vetor está armazenado no momento. Nesse caso, a referência ao primeiro elemento estaria apontando para a memória desalocada. As regras de empréstimo impedem que os programas cheguem a essa situação.

## Iterando em vetores

Para acessar cada elemento em um vetor por vez, deveríamos iterar por todos os elementos em vez de usar índices para acessar um de cada vez. O código abaixo mostra como usar um loop for para obter referências imutáveis ​​para cada elemento em um vetor de valores ``i32`` e imprimi-los.

```
fn main() {
    let v = vec![100, 32, 57, 179, 106, 7, 65, 135, 22, 13, 26, 149];
    for i in &v {
        println!("{}", i);
    }
}
```

Também podemos iterar sobre referências mutáveis ​​para cada elemento em um vetor mutável para fazer alterações em todos os elementos. O loop for no código abaixo adicionará 50 a cada elemento.

```
fn main() {
    let mut v = vec![100, 32, 57];
    for i in &mut v {
        *i += 50;
    }
}
```

Para alterar o valor ao qual a referência mutável se refere, temos que usar o operador de desreferência ``*`` para obter o valor em ``i`` antes de podermos usar o operador ``+=``. A iteração sobre um vetor, seja imutável ou mutável, é segura por causa das regras do verificador de empréstimo.

## Usando um Enum para armazenar vários tipos

Os vetores só podem armazenar valores do mesmo tipo. Isso pode ser inconveniente; Definitivamente há casos de uso para a necessidade de armazenar uma lista de itens de diferentes tipos. Felizmente, as variantes de um enum são definidas sob o mesmo tipo de ``enum``, portanto, quando precisamos de um tipo para representar elementos de tipos diferentes, podemos definir e usar uma ``enum``!

Por exemplo, digamos que queremos obter valores de uma linha em uma planilha na qual algumas das colunas da linha contenham números inteiros, alguns números de ponto flutuante e algumas strings. Podemos definir uma ``enum`` cujas variantes conterão os diferentes tipos de valor, e todas as variantes do ``enum`` serão consideradas do mesmo tipo: o da ``enum``.

```
enum SpreadsheetCell {
        Int(i32),
        Float(f64),
        Text(String),
    }

    let row = vec![
        SpreadsheetCell::Int(3),
        SpreadsheetCell::Text(String::from("blue")),
        SpreadsheetCell::Float(10.12),
    ];
```

O Rust precisa saber quais tipos estarão no vetor em tempo de compilação para saber exatamente quanta memória na heap será necessária para armazenar cada elemento. Também devemos ser explícitos sobre quais tipos são permitidos neste vetor. Se Rust permitir que um vetor contenha qualquer tipo, haverá uma chance de que um ou mais tipos causem erros com as operações executadas nos elementos do vetor. Usar uma enum`` mais uma expressão de correspondência significa que o Rust garantirá em tempo de compilação que todos os casos possíveis sejam tratados.

Agora que discutimos algumas das formas mais comuns de usar vetores, certifique-se de revisar a [documentação da API](https://doc.rust-lang.org/std/vec/struct.Vec.html) para todos os muitos métodos úteis definidos em ``Vec<T>`` pela biblioteca padrão. Por exemplo, além de ``push``, um método ``pop`` remove e retorna o último elemento.]

Além disso, como qualquer outro ``struct`` um vetor é liberado assim quando sai do escopo.

```
fn main() {
    {
        let v = vec![1, 2, 3, 4];

        // do stuff with v
    } // <- v goes out of scope and is freed here
}
```

Quando o vetor é descartado, todo o seu conteúdo também é descartado, o que significa que os inteiros que ele contém serão limpos. O verificador de empréstimo garante que qualquer referência ao conteúdo de um vetor seja usada apenas enquanto o próprio vetor for válido.

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/Zs-pS-egQSs/0.jpg)](https://www.youtube.com/watch?v=Zs-pS-egQSs?t=30)


### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD19/README.md)

## REFERÊNCIAS BIBLIOGRÁICAS

[1] - Vectors. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch08-01-vectors.html>. Acesso em 07/10/2022.

IMAGEM 01 - https://qr.ae/pvQk54