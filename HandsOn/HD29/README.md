# **Lifetimes** 

Em Rust, os lifetimes (tempo de vida) são uma característica fundamental do sistema de tipos, que ajuda a garantir a segurança da memória. O conceito de lifetimes é usado para garantir que referências a dados (ponteiros) são válidas por tempo suficiente para serem usadas com segurança.

Anotar tempo de vida não é nem mesmo um conceito que a maioria das outras linguagens de programação tem, então isso vai parecer desconhecido e novo. Embora não abordemos os tempos de vida em sua totalidade neste capítulo, discutiremos maneiras comuns de encontrar sintaxe lifetime para que você possa se sentir confortável com o conceito.

## Evitando referências penduradas com tempo de vida.

O principal objetivo do tempo de vida é evitar referências penduradas, que fazem com que um programa faça referência a dados diferentes dos dados que ele pretende referenciar. Em ourtras linguagens de programação, isso pode fazer sérios problemas, pois variáveis fora do escopo podem alterar todo o ambiente de trabalho - consequentemente aumentando o desperdício de tempo que você gasta debugando o programa. Veja um exemplo abaixo:

```
fn main() {
    // Iniciamos uma variável qualquer 
    let variavel_fora_escopo;

    {   
        // criamos outra variável 
        let temperatura = 24.5;
        // acoplamos essa variável na variável fora de escopo
        variavel_fora_escopo = &temperatura;
        println!("A temperatura é: {}", variavel_fora_escopo)
    }

    println!("variavel é: {}", variavel_fora_escopo);
}
```

O escopo externo declara uma variável chamada ``variavel_fora_escopo`` sem valor inicial e o escopo interno declara uma variável chamada ``temperatura`` com o valor inicial de 24.5. Dentro do escopo interno, tentamos definir o valor de ``variavel_fora_escopo`` como uma referência a ``temperatura``. Em seguida, o escopo interno termina e tentamos imprimir o valor em ``variavel_fora_escopo``. Esse código não será compilado porque o valor ao qual ``variavel_fora_escopo`` está se referindo saiu do escopo antes de tentarmos usá-lo. Então, como Rust determina que esse código é inválido? Ele usa um **verificador de empréstimo**.

## O verificador de empréstimos

O compilador Rust tem um verificador de empréstimos que compara escopos para determinar se todos os empréstimos são válidos. Deixando nosso exemplo mais elucidativo, o que o verificador de emprestimos faz, é verificar o tempo de vida dentro de cada namespace, como mostra a figura abaixa, semelhante ao exemplo utilizado acima:

```
fn main() {
    let r;                // ---------+-- 'a
                          //          |
    {                     //          |
        let x = 5;        // -+-- 'b  |
        r = &x;           //  |       |
    }                     // -+       |
                          //          |
    println!("r: {}", r); //          |
}                         // ---------+
```

Aqui, anotamos o tempo de vida de ``r`` com ``'a`` e o tempo de vida de ``x`` com ``'b``. Como você pode ver, o bloco ``'b`` interno é muito menor do que o bloco externo ``'a`` lifetime. No momento da compilação, Rust compara o tamanho das duas vidas e vê que ``r`` tem uma vida de ``'a``, mas que se refere à memória com uma vida de ``'b``. O programa é rejeitado porque ``'b`` é mais curto que ``'a``: o assunto da referência não vive tanto quanto a referência.

Para concertar o código feito acima, podemos simplismente utilizar o mesmo namespace, fazendo com que o tempo de vida não seja perdido! Veja abixo:

```
fn main() {
    let x = 5;            // ----------+-- 'b
                          //           |
    let r = &x;           // --+-- 'a  |
                          //   |       |
    println!("r: {}", r); //   |       |
                          // --+       |
}                         // ----------+
```

Aqui, ``x`` tem o tempo de vida ``'b``, que neste caso é maior que ``'a``. Isso significa que ``r`` pode fazer referência a ``x`` porque **Rust sabe que a referência em ``r`` sempre será válida enquanto ``x`` é válida.**

## Tempos de vida genéricos em funções

Vamos imaginar que você é funcionário de uma empresa de telecomunicações e você deseja saber o quanto um usuário da sua rede está gastando de tempo em ligações. Supondo que seu chefe queira ver quanto tempo a ``Alice`` está gastando no telefone, você utiliza o seguinte código:

```
fn main() {
    let calls = [
        Call {caller: "Bob", duration: 110  },
        Call {caller: "Bob", duration: 182  },
        Call {caller: "Alice", duration: 40 },
        Call {caller: "Alice", duration: 33 },
        Call {caller: "Alice", duration: 16 },
    ];

    // seu chefe pede para você saber quantos minutos a Alice
    // ficou no telefone nesse banco de dados
    // (na vida real esse banco de dados é extremamente grande)
        // usuário que você deseja saber a duração da chamada
    let user = "Alice";
    let total_duration_alice = calculate_total_duration(&calls, user);
    println!("Duração total das chamadas de {}: {} segundos", user, total_duration_alice);
}
```

Quando você consulta a base de dados da sua empresa, você nota que há um grande erro! Elas não utilizaram lifetimes - e devido a esse motivo ficou impossível utilizar o código. Veja como a empresa deixou os dados sem lifetimes:

```
// Struct que representa uma chamada telefônica
struct Call {
    caller: &str,
    duration: u32,
}

fn calculate_total_duration(calls: &[Call], user: &str) -> u32 {
    calls                               //  +------------------------+
    .iter()                             //  | Toda essa parte acha   |
    .filter(|call| call.caller == user) //  | um usuário da lista    |
    .map(|call| call.duration)          //  | e calcula quanto tempo |
    .sum()                              //  | ele ficou no celular.  |
                                        //  +------------------------+
}
```

Quando o código foi definido na empresa, os engenheiros de software esqueceram do seguinte detalhe: não sabemos os tempos de vida concretos das referências que serão passadas, então não podemos olhar para os escopos para determinar se a referência que retornamos sempre será válida. O verificador de empréstimo também não pode determinar isso, porque não sabe como os tempos de vida são.

Para solucionar o problema, podemos utilizar a seguinte formatação:

```
// Struct que representa uma chamada telefônica
struct Call<'a> {
    caller: &'a str,
    duration: u32,
}

// Função que calcula a duração total das chamadas feitas por um determinado usuário
fn calculate_total_duration<'a>(calls: &[Call<'a>], user: &'a str) -> u32 {
    calls
        .iter()
        .filter(|call| call.caller == user)
        .map(|call| call.duration)
        .sum()
}
```

veja que apareceu um ``<'a>`` em ``struct Call<'a>``, ``caller: &'a str,``, ``calculate_total_duration<'a>`` e em ``calls: &[Call<'a>], user: &'a str``. Mas por que isso ocorreu?

Nessa versão corrigida, introduzimos um tempo de vida genérico ``'a`` na estrutura ``Call`` e na função ``calculate_total_duration``. O tempo de vida ``'a`` está relacionado à vida útil das referências ``&str``. A declaração ``struct Call<'a>`` indica que a estrutura ``Call`` possui uma referência ``caller`` com tempo de vida ``'a``. Da mesma forma, a função ``calculate_total_duration<'a>`` recebe uma fatia de ``Call`` com tempo de vida ``'a`` e uma referência user com tempo de vida ``'a``.

Veremos com mais detalhes isso nos tópicos abaixo.

## Sintaxe de anotação lifetimes

As anotações de lifetimes **não alteram o tempo de vida de qualquer uma das referências**. Em vez disso, elas descrevem os relacionamentos dos tempos de vida de referências múltiplas entre si sem afetar os tempos de vida. Assim como as funções podem aceitar qualquer tipo quando a assinatura especifica um parâmetro de tipo genérico, **as funções podem aceitar referências com qualquer tempo de vida especificando um parâmetro de tempo de vida genérico.**

Anotações de tempo de vida têm uma sintaxe um pouco incomum: **os nomes dos parâmetros de tempo de vida devem começar com um apóstrofo (')** e geralmente são todos minúsculos e muito curtos, como tipos genéricos. A maioria das pessoas usa o nome 'a para a primeira anotação da vida. Colocamos anotações de parâmetro de vida útil após o & de uma referência, usando um espaço para separar a anotação do tipo da referência.

Aqui estão alguns exemplos: uma referência a um i32 sem um parâmetro de tempo de vida, uma referência a um i32 que tem um parâmetro de tempo de vida chamado 'a, e uma referência mutável a um i32 que também tem o tempo de vida 'a.

```
&i32        // uma referência
&'a i32     // uma referência com um tempo de vida explícito
&'a mut i32 // uma referência mutável com um tempo de vida explícito
```

Uma anotação de tempo de vida por si só não tem muito significado, porque as anotações destinam-se a informar ao Rust como os parâmetros de tempo de vida genéricos de várias referências se relacionam entre si.

## Anotações lifetimes em assinaturas de função

Para usar anotações de tempo de vida em assinaturas de função, precisamos declarar os parâmetros de tempo de vida genéricos dentro de colchetes entre o nome da função e a lista de parâmetros, assim como fizemos com os parâmetros de tipo genérico.

Queremos que a assinatura expresse a seguinte restrição: a referência retornada será válida desde que ambos os parâmetros sejam válidos. Esta é a relação entre os tempos de vida dos parâmetros e o valor de retorno. Vamos nomear o tempo de vida ``'a`` e depois adicioná-lo a cada referência. Veja abaixo o exemplo de uma função que retorna a maior string.

```
fn maior_string<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

A assinatura da função agora diz ao Rust que, por algum tempo de vida ``'a``, a função recebe dois parâmetros, ambos os quais são fatias de string que duram pelo menos tanto quanto o tempo de vida ``'a``. A assinatura da função também informa ao Rust que a fatia de string retornada da função viverá pelo menos tanto quanto o tempo de vida ``'a``. Na prática, isso significa que o tempo de vida da referência retornada pela função ``maior_string`` é igual ao menor dos tempos de vida dos valores referenciados pelos argumentos da função. Esses relacionamentos são o que queremos que o Rust use ao analisar esse código.

Lembre-se, quando especificamos os parâmetros de tempo de vida nesta assinatura de função, não estamos alterando os tempos de vida de quaisquer valores transmitidos ou retornados. Em vez disso, estamos especificando que o verificador de empréstimo deve rejeitar quaisquer valores que não cumpram essas restrições. Observe que a função ```maior_string`` não precisa saber exatamente quanto tempo ``x`` e ``y`` viverão, apenas que algum escopo pode ser substituído por ``'a`` que satisfará essa assinatura.

