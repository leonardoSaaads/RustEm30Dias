# **Hash Maps & Binary Tree Map**

A última das nossas coleções comuns é o hash map. O tipo ``HashMap <K, V>`` armazena um mapeamento de chaves do tipo ``K`` para valores do tipo ``V``. Ele faz isso através de um hashing function, que determina como ele coloca essas chaves e valores em memória. Muitas linguagens de programação diferentes suportam este tipo de estrutura de dados, mas muitas vezes com um nome diferente: hash, map, object, hash table ou associative array, apenas para citar alguns.

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
let erlang = scores.get(&team_name);
```

