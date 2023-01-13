# **Hash Maps & Binary Tree Map**

## **Hash Maps**

A última das nossas coleções comuns é o hash map. O tipo ``HashMap <K, V>`` armazena um mapeamento de chaves do tipo ``K`` para valores do tipo ``V``. Veremos mais a frente sobre a formatação ``<K, V>`` com genéricos, mas tenha em mente que podem ser quais típos de dados. Ele faz isso através de um **hashing function**, que determina como ele coloca essas chaves e valores em memória. Muitas linguagens de programação diferentes suportam este tipo de estrutura de dados, mas muitas vezes com um nome diferente: hash, map, object, hash table ou associative array, apenas para citar alguns.

Os Hash maps são úteis para quando você deseja poder procurar dados sem uso de índice, como você pode com vetores, mas usando uma chave que pode ser de qualquer tipo. Por exemplo, em um jogo, você poderia acompanhar a pontuação de cada equipe em um hash map onde cada chave é o nome de uma equipe e os valores são cada pontuação da equipe. Dado um nome da equipe, você pode recuperar sua pontuação.

Você verá posteriormente que as duas estruturas são bem semelhantes e terão grande impacto para a análise de dados em Rust. Ademais, vale ressaltar que a lib ``std::collections`` em Rust está em constante atualização, assim sendo, é válido sempre dar uma olhada.

## **Criando Hash Maps**

Podemos criar um HashMap vazio com ``new``, e adicionar elementos com ``insert``. Neste exemplo abaixo, iremos fazer a carga máxima oferecida (Erlang) de duas regiões. Vamos utilizar duas regiões: A região_A terá um número de 5.084 e a região_B terá um número de 13.18.

```
// Realiza a importação.
use std::collections::HashMap;

// Cria o Hash Map
let mut scores = HashMap::new();

// Faz a inserção 
scores.insert(String::from("regiao_A"), 5.084);
scores.insert(String::from("regiao_B"), 13.18);
```

Assim como os vetores, os hash maps armazenam seus dados na heap. Este HashMap tem chaves do tipo String e valores do tipo f32. Como vetores, os hash maps são homogêneos: **todas as chaves devem ter o mesmo tipo e todos os valores devem ter o mesmo tipo.**

Outra maneira de construir um hash map é usando o método collect em um vetor de tuplas, onde cada tupla consiste de uma chave e seu valor. O método collect reúne dados em vários tipos de coleção, incluindo HashMap. Por exemplo, se tivéssemos os nomes das regiões e os valores Erlang em dois vetores separados, podemos usar o método ``zip`` para criar um vetor de tuplas onde “Regiao_A” é emparelhado com 5.084, e assim por diante. Então podemos usar o método collect para transformar esse vetor de tuplas em um HashMap:

```
// Realiza a importação.
use std::collections::HashMap;

// Valores utilizados
let teams  = vec![String::from("Regiao_A"), String::from("Regiao_B")];
let initial_scores = vec![5.084, 13.18];

// Zipa os valores nos vetores e cria o Hash Map
let scores: HashMap<_, _> = teams.iter().zip(initial_scores.iter()).collect();
```

A anotação de tipo ``HashMap <_, _>`` é necessária aqui porque é possível collect em muitas estruturas de dados diferentes, e Rust não sabe qual você deseja, a menos que você especifique. Para os parâmetros de tipo, para os tipos de chave e valor, no entanto, usamos underscores e Rust pode inferir os tipos que o hash map contém com base nos tipos de dados no vetor.

## **Hash Maps e Ownership** 

Para os tipos que implementam a ``Copy`` trait, como f32, os valores são copiados no hash map. Para valores owned como ``String``, os valores serão movidos e o hash map será o owner desses valores:

```
use std::collections::HashMap;

let field_name = String::from("Favorite color");
let field_value = String::from("Blue");

let mut map = HashMap::new();
map.insert(field_name, field_value);
```

Não poderíamos usar as ligações ``field_name`` e ``field_value`` depois que foram transferidos para o hash map com a chamada para ``insert``.

Se inserimos referências a valores no hash map, os próprios valores não serão movido para o hash map. Os valores que as referências apontam devem ser válido pelo menos enquanto o hash map seja válido, no entanto. Falaremos mais sobre esses problemas na seção Lifetimes.

## Acessando valores de Hash Maps.

Podemos obter um valor do hash map fornecendo a chave para o método ``get``:

```
// Realiza a importação.
use std::collections::HashMap;

// Cria o Hash Map
let mut scores = HashMap::new();

// Faz a inserção 
scores.insert(String::from("regiao_A"), 5.084);
scores.insert(String::from("regiao_B"), 13.18);

let nome_regiao = String::from("regiao_B");
let erlang_B = scores.get(&nome_regiao).copied().unwrap_or(0.001);
```

Aqui, ``score`` terá o valor associado a ``região_B``, e o resultado será 13.18. O método get retorna um ``Option<&V>;`` se não houver valor para essa chave no mapa de hash,`` get`` retornará ``None``. Este programa manipula a opção chamando copiado para obter uma ``Option<i32>`` em vez de uma ``option<&i32>`` e, em seguida, ``unwrap_or`` para definir a pontuação Erlang como 0.001 (Menor valor na tabela Erlang) se as pontuações não tiverem uma entrada para a chave.

![](/Imagens/HD20/tabela_erlang.png)

Podemos iterar sobre cada par de chave/valor em um mapa de hash de maneira semelhante à que fazemos com vetores, usando um loop ``for``. Veja um exemplo abaixo onde iremos fazer uma relação entre os bits e sua respectiva fase, em graus, na modulação QAM-16. Relembre-se da imagem com a modulação QAM-16 abaixo:


![](/Imagens/HD20/QAM.png)


```
fn main() {
    use std::collections::HashMap;

    let mut qam = HashMap::new();

    // Algumas relações para o QAM-16
    qam.insert(String::from("1000"), 135);
    qam.insert(String::from("1001"), 157);
    qam.insert(String::from("1010"), 113);
    qam.insert(String::from("1011"), 135);
    qam.insert(String::from("1100"), 225);
    qam.insert(String::from("1101"), 203);
    qam.insert(String::from("1110"), 247);
    qam.insert(String::from("1111"), 225);

    for (key, value) in &qam {
        println!("{key}: {value}");
    }
}
```

Veja a saída do programa:

```
1100: 225
1010: 113
1111: 225
1101: 203
1000: 135
1110: 247
1011: 135
1001: 157
```

## Ownership em Hash Maps

É preciso tomar cuidado com Hash Maps devido a propriedade de Ownership. Quando colocamos os valores de entrada/saída, sem o devido cuidado com ownersip, poderemos ter sérios problemas. Para tipos que implementam a característica ``Copy``, como ``i32``, os valores são copiados para o mapa de hash. Para valores de propriedade como ``String``, os valores serão movidos e o mapa de hash será o proprietário desses valores. Veja o exemplo:

```
    use std::collections::HashMap;

    let field_name = String::from("Favorite color");
    let field_value = String::from("Blue");

    let mut map = HashMap::new();
    map.insert(field_name, field_value);
    // field_name e field_value são inválidos a partir deste ponto, tente usar
    // algum desses parâmetros e você verá que surgirá um erro!
```

Não podemos usar as variáveis ​​field_name e field_value depois que elas foram movidas para o mapa de hash com a chamada para ``insert``. Se inserirmos referências a valores no mapa de hash, os valores não serão movidos no mapa de hash. Os valores para os quais as referências apontam devem ser válidos para at pelo menos enquanto o mapa de hash for válido. Falaremos mais sobre essas questões em o "Validando referências com Tempo de vida".

## Atualizando um mapa de hash

Embora o número de pares de chave e valor seja crescente, cada chave pode ter apenas um valor associado a ele de cada vez. Quando você deseja alterar os dados em um mapa de hash, você tem que decidir como manipule o caso quando uma chave já tem um valor atribuído. Você pode substituir o valor antigo com o valor novo, desconsiderando completamente o valor antigo. Você poderia manter o valor antigo e ignorar o novo valor, adicionando apenas o novo valor se o a chave ainda não tem um valor. Ou você pode combinar o valor antigo e o novo valor. Vejamos como fazer cada um desses!

### Substituindo um Valor

Se inserirmos uma chave e um valor em um mapa de hash e, em seguida, inserirmos essa mesma chave com um valor diferente, o valor associado a essa chave será substituído. Veja o exemplo:

```
fn main() {
    use std::collections::HashMap;

    let mut scores = HashMap::new();

    scores.insert(String::from("Primeiro"), 1);
    scores.insert(String::from("Segundo"), 2);
    scores.insert(String::from("Terceiro"), 4);
    scores.insert(String::from("Terceiro"), 3);

    println!("{:?}", scores);
}

// A saída deve ser:
// {"Segundo": 2, "Terceiro": 3, "Primeiro": 1}
```

### Adicionando uma chave e um valor somente se uma chave não estiver presente

É comum verificar se uma determinada chave já existe no mapa de hash com um valor, em seguida, execute as seguintes ações: se a chave existir no hash map, o valor existente deve permanecer do jeito que está. Se a chave não existir, insira-o e um valor para ele.

Os mapas de hash têm uma API especial para isso chamada ``entry`` que usa a chave que você usa deseja verificar como um parâmetro. Veja o exemplo abaixo:

```
fn main() {
    use std::collections::HashMap;

    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);

    scores.entry(String::from("Yellow")).or_insert(50);
    scores.entry(String::from("Blue")).or_insert(50);

    println!("{:?}", scores);
}
```

### Atualizando um valor com base no valor antigo

Outro caso de uso comum para mapas de hash é procurar o valor de uma chave e, em seguida, atualize-o com base no valor antigo.

```
fn main() {
    use std::collections::HashMap;

    let text = "hello world wonderful world";

    let mut map = HashMap::new();

    for word in text.split_whitespace() {
        let count = map.entry(word).or_insert(0);
        *count += 1;
    }

    println!("{:?}", map);

// O resultado deve ser
// {"world": 2, "hello": 1, "wonderful": 1}
}
```

 O código conta quantas vezes cada palavra aparece em algum texto. Usamos um mapa de hash com as palavras como chaves e incrementar o valor para acompanhar quantas vezes temos vi essa palavra. Se for a primeira vez que vemos uma palavra, primeiro inseriremos o valor 0.

O método ``split_whitespace`` retorna um iterador sobre subfatias, separadas por espaços em branco, do valor no texto. O método ``or_insert`` retorna uma referência mutável (``&mut V``) para o valor da chave especificada. Aqui armazenamos essa referência mutável na variável de contagem, portanto, para atribuir a esse valor, devemos primeiro desreferenciar a contagem usando o asterisco (``*``). A referência mutável sai do escopo no final do loop for, portanto, todas essas alterações são seguras e permitidas pelas regras de borrowing (empréstimo).

## **Binary Tree Map**

As Binary Trees representam um compromisso fundamental entre a eficiência do cache e a minimização real da quantidade de trabalho realizado em uma pesquisa. Em teoria, uma árvore de pesquisa binária (BST) é a escolha ideal para um mapa classificado, pois um BST perfeitamente equilibrado executa a quantidade mínima teórica de comparações necessárias para localizar um elemento (log2n). Em outras palavras, ela melhora a eficiência das pesquisas em extensos bancos de dados e é um método poderoso para quem deseja performace. 

Assim como Hash Maps, Binary Treez possuem sua importação e utilização de forma semelhente. Note abaixo como criamos uma Binary Tree:

### Criando uma Binary Tree Map(Adicionando e removendo elementos)

Criaremos uma binary tree e utilizaremos para os propóstitos já vistos em Hash Maps. Como Rust é uma linguaguem vastamente dinâmica, é possível realizar essas tarefas.

```
// Realiza a importação.
use std::collections::BTreeMap;

// Cria o Hash Map
let mut films = BTreeMap::new();

// Faz a inserção 
films.insert("Titanic", "Sensacional");
films.insert("Avatar", "Sensacional");
films.insert("Contato", "Bom");
films.insert("Justice Fals", "Ruim");
films.insert("Em busca do capacete", "Horrivel");
films.insert("Esse filme não existe","não sei");
films.remove("Esse filme não existe");
```

### Procurando por uma determinada Chave

Veja como podemos operar para buscar um determinado elementos na arvore binárias:

```
if !films.contains_key("Os Miseráveis") {
    println!("Temos {} resenhas, mas Les Misérables não é uma.",
             films.len());
}
```

O código primeiro checa se há um filme chamado 'Os Miseráveis' com ``films.contains_key("Os Miseráveis")``. Caso não haja esse filme, ele executa o comando ``print`` informando ue não houve um filme com esse nome.

Há também uma forma melhoe de realizar essas buscas. Neste caso, usaremos ``match`` e criaremos uma estrutura que retorna o nome do filme com a sua respectiva review. Esse método é mais robusto pois retorna uma frase caso tenha ou não um filme. Veja abaixo:

```
// look up the values associated with some keys.
let achar_filmes = ["Up!", "Avatar"];
for movie in &achar_filmes {
    match films.get(movie) {
       Some(review) => println!("{movie}: {review}"),
       None => println!("{movie} is unreviewed.")
    }
}
```

### Iterando sobre Binary Trees Maps

Também é possível iterar sobre Binary Trees de forma semelante a Hash Maps. Isso facilita as coisas e podemos ter um ganho de perfomace em extensos bancos de dados. Veja o exemplo abaixo:

```
for (movie, review) in &films {
    println!("{movie}: \"{review}\"");
}
```

Neste ponto, deveremos ter uma saída como:

```
Avatar: "Sensacional"
Contato: "Bom"
Em busca do capacete: "Horrivel"
Justice Fals: "Ruim"
Titanic: "Sensacional"
```

## Resumo

Binary Tress e Hash Maps são duas ferramentas excelentes para a manipulação de dados. Podemos fazer transformações e atribuir valores a certas operações. Com isso, há um ganho de performace e as operações não passam a ter uma grande complexidade.

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/80OQHtuNrXI/0.jpg)](https://www.youtube.com/watch?v=80OQHtuNrXI)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD21/README.md)

## REFERÊNCIAS BIBLIOGRÀFICAS

[1] - Struct std::collections::BTreeMap. BTreeMap - doc.rust-lang.org - <https://doc.rust-lang.org/stable/std/collections/struct.BTreeMap.html>. Acesso em 12 de janeiro de 2022.

[2] - Armazenando chaves com valores associados em mapas de hash. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch08-03-hash-maps.html#updating-a-hash-map>. Acesso em 12 de janeiro de 2022.

Imagem - http://www.eletr.ufpr.br/marlio/te241/aula3.pdf

Imagem 02 - https://www.passeidireto.com/arquivo/48613199/tabela-de-erlang
