# **Lidando com Arquivos**

A melhor maneira de aprender a lidar com arquivos em Rust é praticando. Neste tutorial, exploraremos como manipular diferentes tipos de arquivos usando a linguagem de programação Rust. Antes de começarmos, é importante preparar o ambiente virtual para essa tarefa. Para isso, vamos criar um novo diretório usando o comando ``cargo``:

```
cargo new lendo_arquivos
cd lendo_arquivos
```

Isso criará um novo diretório chamado ``lendo_arquivos`` com a estrutura básica de um projeto Rust e você entrará nesse diretório. Agora estamos prontos para começar a explorar como lidar com arquivos em Rust.

A primeira tarefa é fazer o ``lendo_arquivos`` aceitar dois argumentos de linha de comando: **o caminho do arquivo e uma string para pesquisar**. Queremos executar nosso programa com cargo run, dois hífens para indicar que os argumentos seguintes são para o nosso programa e não para o cargo, uma string para pesquisar e um caminho para um arquivo a ser pesquisado, assim:

```
cargo run -- string_desejada example_filename.txt
```

para realizar essa terafa, para permitir que o ``lendo_arquivos`` leia os valores dos argumentos de linha de comando que passamos para ele, precisaremos da função ``std::env::args`` fornecida na biblioteca padrão do Rust. Essa função retorna um iterador dos argumentos de linha de comando passados para o ``lendo_arquivos``. Como vimos anteriormente, os iterators produzem uma série de elementos que podem ser reunidos usando `.collect()`.

```
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();
    dbg!(args);
}
```

Primeiro, colocamos o módulo ``std::env`` no escopo com uma instrução ``use`` para que possamos usar sua função ``args``. Observe que a função ``std::env::args`` está aninhada em dois níveis de módulos.

Na primeira linha de ``main``, chamamos env::args e imediatamente usamos ``collect`` para transformar o iterador em um vetor contendo todos os valores produzidos pelo iterador. Podemos usar a função ``collect`` para criar vários tipos de coleções, então anotamos explicitamente o tipo de ``args`` para especificar que queremos um vetor de strings. Embora raramente precisemos anotar tipos no Rust, coletar é uma função que você frequentemente precisa anotar porque o Rust não é capaz de inferir o tipo de coleção que você deseja.

Por fim, imprimimos o vetor usando a macro de depuração.

___

**NOTA: A função args**

std::env é um módulo em Rust que fornece funções para lidar com o ambiente do processo atual. Isso inclui funções para acessar variáveis ​​de ambiente e argumentos de linha de comando, bem como funções para trabalhar com o diretório atual do processo. ``std::env::args`` e ``std::env::args_os`` são funções em Rust que retornam os argumentos com os quais o programa foi iniciado (normalmente passados ​​através da linha de comando). A primeira função retorna um iterador sobre Strings e entra em pânico se um dos argumentos não for unicode válido. A segunda função retorna um iterador sobre OsStrings e nunca entra em pânico. Essas funções são importantes porque permitem que você acesse os argumentos da linha de comando em seu programa Rust.

___

Vamos tentar executar o código primeiro sem argumentos e depois com dois argumentos:

```
cargo run && cargo run -- alguma_coisa Show
```

Você verá que aparecerá algo como:

```
C:\\\\ler_arquivos>cargo run && cargo run -- alguma_coisa Show
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target\debug\ler_arquivos.exe`
[src\main.rs:5] args = [
    "target\\debug\\ler_arquivos.exe",
]
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target\debug\ler_arquivos.exe alguma_coisa Show`
[src\main.rs:5] args = [
    "target\\debug\\ler_arquivos.exe",
    "alguma_coisa",
    "Show",
]
```

Observe que o primeiro valor no vetor é ``target/debug/ler_arquivos``, que é o nome do nosso binário. Isso corresponde ao comportamento da lista de argumentos em C, permitindo que os programas usem o nome pelo qual foram chamados em sua execução. Geralmente é conveniente ter acesso ao nome do programa caso você queira imprimi-lo em mensagens ou alterar o comportamento do programa com base em qual alias de linha de comando foi usado para invocar o programa. Mas para os propósitos deste capítulo, vamos ignorá-lo e salvar apenas os dois argumentos de que precisamos.

## Salvando os valores de argumento em variáveis

Podemos ver que ele está acessando o lugar que passamos para ele, todavia ele está apenas acessando e não está realizando mais nenhuma operação. Tendo isso em mente, iremos agora salvar os valores dos dois argumentos em variáveis para que possamos usar os valores em todo o resto do programa.

```
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();

    let query = &args[1];
    let file_path = &args[2];

    println!("Procurando por:\n{}", query);
    println!("No arquivo:\n{}", file_path);
}
```

Como vimos quando imprimimos o vetor, o nome do programa ocupa o primeiro valor no vetor em ``args[0]``, então estamos começando os argumentos no índice 1. O primeiro argumento que o ``lendo_arquivos`` usa é a cadeia de caracteres que estamos procurando, então colocamos uma referência ao primeiro argumento na consulta de variáveis. O segundo argumento será o caminho do arquivo, então colocamos uma referência ao segundo argumento na variável ``file_path``.

Imprimimos temporariamente os valores dessas variáveis para provar que o código está funcionando como pretendemos.

```
$$ cargo run -- test exemplo.txt
   Compiling ler_arquivos v0.1.0 (C:...\ler_arquivos)
    Finished dev [unoptimized + debuginfo] target(s) in 0.72s
     Running `target\debug\ler_arquivos.exe test exemplo.txt`
Procurando por:
test
No arquivo:
exemplo.txt
```

Ótimo, o programa está funcionando! Os valores dos argumentos de que precisamos estão sendo salvos nas variáveis certas!

# **Lendo Arquivos**

Primeiro, precisamos de um arquivo de exemplo para testá-lo: usaremos um arquivo com uma pequena quantidade de texto em várias linhas com algumas palavras repetidas. Crie um arquivo chamado ``poema.txt`` no nível raiz ``(C:...\ler_arquivos\poema.txt)`` do seu projeto. Você pode usar esse abaixo, por exemplo: 

<div align=“center”> Círculo vicioso<br><br> Bailando no ar, gemia inquieto vagalume:<br> “Quem me dera que eu fosse aquela loira estrela<br> Que arde no eterno azul, como uma eterna vela!”<br> Mas a estrela, fitando a lua, com ciúme:<br> “Pudesse eu copiar-te o transparente lume,<br> Que, da grega coluna à gótica janela,<br> Contemplou, suspirosa, a fronte amada e bela”<br> Mas a lua, fitando o sol com azedume:<br> “Mísera! Tivesse eu aquela enorme, aquela<br> Claridade imortal, que toda a luz resume”!<br> Mas o sol, inclinando a rútila capela:<br> Pesa-me esta brilhante auréola de nume…<br> Enfara-me esta luz e desmedida umbela…<br> Por que não nasci eu um simples vagalume?”…<br> </div>


Com o texto no lugar, edite ``src/main.rs`` e adicione código para ler o arquivo, conforme mostrado abaixo: 

```
use std::env;
use std::fs;

fn main() {
    // --snip--
    let args: Vec<String> = env::args().collect();

    let query = &args[1];
    let file_path = &args[2];

    println!("Procurando por:\n{}", query);
    println!("No arquivo:\n{}", file_path);

    let contents = fs::read_to_string(file_path)
        .expect("Não foi possível abrir o Arquivo");

    println!("Com o texto:\n{contents}");
}
```

Note que fizemos pequenas modificações simples no código, mas são mudanças muito importantes! Trazemos uma parte relevante da biblioteca padrão com uma declaração de uso: precisamos de ``std::fs`` para manipular arquivos.

Em geral, a nova instrução ``fs::read_to_string`` usa o ``file_path``, abre esse arquivo e retorna um ``std::io::Result<String>`` conteúdo do arquivo. Depois disso, adicionamos novamente um ``println`` temporário! Instrução que imprime o valor do conteúdo depois que o arquivo é lido, para que possamos verificar se o programa está funcionando até agora.

Veja como fica a saída do Arquivo: 

```
$$ cargo run -- poema poema.txt 
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target\debug\ler_arquivos.exe poema poema.txt` 
Procurando por:
poema
No arquivo:    
poema.txt      
Com o texto:   
Círculo vicioso

Bailando no ar, gemia inquieto vagalume:
“Quem me dera que eu fosse aquela loira estrela
Que arde no eterno azul, como uma eterna vela!”
Mas a estrela, fitando a lua, com ciúme:
“Pudesse eu copiar-te o transparente lume,
Que, da grega coluna à gótica janela,
Contemplou, suspirosa, a fronte amada e bela”
Mas a lua, fitando o sol com azedume:
“Mísera! Tivesse eu aquela enorme, aquela
Claridade imortal, que toda a luz resume”!
Mas o sol, inclinando a rútila capela:
Pesa-me esta brilhante auréola de nume…
Enfara-me esta luz e desmedida umbela…
Por que não nasci eu um simples vagalume?”…
```

Excelente! O código leu e, em seguida, imprimiu o conteúdo do arquivo. Mas o código tem algumas falhas. No momento, a função principal tem múltiplas responsabilidades: geralmente, as funções são mais claras e fáceis de manter se cada função for responsável por apenas uma ideia. O outro problema é que não estamos lidando com erros da melhor forma como poderíamos.

## Melhorando a modularidade e usando tratamento de erros

O problema organizacional de alocar a responsabilidade de várias tarefas para a função principal é comum a muitos projetos binários. Como resultado, a comunidade Rust desenvolveu diretrizes para dividir as preocupações separadas de um programa binário quando main começa a ficar grande. Este processo tem os seguintes passos:

- Divida seu programa em main.rs e lib.rs e **mova a lógica** do seu programa para lib.rs.

- Contanto que sua lógica de análise de **linha de comando seja pequena**, ela pode permanecer em main.rs.

- Quando a lógica de análise da **linha de comando começar a ficar complicada**, extraia-a de main.rs e mova-a para lib.rs.

- O código que permanecer em main.rs será pequeno o suficiente para verificar sua exatidão lendo-o.

Vamos retrabalhar nosso programa seguindo este processo.

### Extraindo o analisador de argumentos

Extrairemos a funcionalidade para analisar argumentos em uma função que ``main`` chamará para se preparar para mover a lógica de análise da linha de comando para ``src/lib.rs``. O novo início de ``main`` que chama uma nova função ``parse_config``, que definiremos em ``src/main.rs`` por enquanto.

```
use std::env;
use std::fs;

fn main() {
    let args: Vec<String> = env::args().collect();

    let (query, file_path) = analisa_config(&args);

    // --snip--

    println!("Procurando por:\n{}", query);
    println!("No arquivo:\n{}", file_path);

    let contents = fs::read_to_string(file_path)
        .expect("Não foi possível abrir o Arquivo");

    println!("Com o texto:\n{contents}");
}

fn analisa_config(args: &[String]) -> (&str, &str) {
    let query = &args[1];
    let file_path = &args[2];

    (query, file_path)
}
```

Ainda estamos coletando os argumentos da linha de comando em um vetor, mas em vez de atribuir o valor do argumento no índice 1 à variável ``query`` e o valor do argumento no índice 2 à variável ``file_path`` dentro da função principal, passamos o vetor inteiro para o função ``analisa_config``. A função ``analisa_config`` contém a lógica que determina qual argumento vai em qual variável e passa os valores de volta para main.

### Agrupar Valores de Configuração

Podemos dar outro pequeno passo para melhorar ainda mais a função ``analisa_config``. No momento, estamos retornando uma tupla, mas imediatamente quebramos essa tupla em partes individuais novamente. Isso é um sinal de que talvez ainda não tenhamos a abstração certa.

Outro indicador que mostra que há espaço para melhorias é a parte de configuração de ``analisa_config``, o que implica que os dois valores que retornamos estão relacionados e fazem parte de um valor de ``configuração``.

```
use std::env;
use std::fs;

fn main() {
    let args: Vec<String> = env::args().collect();

    let (query, file_path) = analisa_config(&args);

    // --snip--

    println!("Procurando por:\n{}", query);
    println!("No arquivo:\n{}", file_path);

    let contents = fs::read_to_string(file_path)
        .expect("Não foi possível abrir o Arquivo");

    println!("Com o texto:\n{contents}");
}

struct Cofig{
    query: String,
    file_path: String,
}

fn analisa_config(args: &[String]) -> (&str, &str) {
    let query = &args[1];
    let file_path = &args[2];

    Config{query, file_path}
}
```

Adicionamos uma estrutura chamada ``Config`` definida para ter campos chamados ``query`` e ``file_path``. A assinatura de ``analisa_config`` agora indica que retorna um valor de configuração. No corpo de ``parse_config``, onde costumávamos retornar fatias de string que referenciam valores de String em args, agora definimos ``Config`` para conter valores de String pertencentes.

___
**OBS: As vantagens e desvantagens de usar o clone**
Há uma tendência entre muitos Rustaceans de evitar o uso de clones para corrigir problemas de propriedade por causa de seu custo de tempo de execução. Usualmente utilizamos iterators ou closures. (Você verá como isso possui impacto logo logo). Em vez disso, você pode usar o método ``.get()`` para obter uma referência ao elemento em vez de cloná-lo. O método ``.get()`` retorna um ``Option<&T>``, que é um tipo de opção que pode ser ``Some(&T)`` ou ``None``. Se o índice estiver fora dos limites do vetor, o método ``.get()`` retornará ``None``.
___

Agora nosso código transmite mais claramente que ``query`` e ``file_path`` estão relacionados e que seu propósito é configurar como o programa funcionará. Qualquer código que usa esses valores sabe como localizá-los na instância de configuração nos campos nomeados para sua finalidade.

### Criando um Construtor para Config

Portanto, agora que o objetivo da função ``parse_config`` é criar uma instância de ``Config``, podemos alterar ``parse_config`` de uma função simples para uma função chamada new associada à estrutura Config. Fazer essa alteração tornará o código mais idiomático. Da mesma forma, alterando ``parse_config`` para uma nova função associada a ``Config``, poderemos criar instâncias de ``Config`` chamando ``Config::new``

```
use std::env;
use std::fs;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::new(&args);

    println!("Procurando por:\n{}", query);
    println!("No arquivo:\n{}", file_path);

    let contents = fs::read_to_string(file_path)
        .expect("Não foi possível abrir o Arquivo");

    println!("Com o texto:\n{contents}");

    // --snip--
}

struct Config {
    query: String,
    file_path: String,
}

impl Config {
    fn new(args: &[String]) -> Config {
        // Muitos programadores evitam de fazer esse tipo de código:
        // let query = args[1].clone();
        // let file_path = args[2].clone();
        // Os motivos para evitarem foram exposto acima
        let query = args.get(1).unwrap().to_owned();
        let file_path = args.get(2).unwrap().to_owned();
        Config { query, file_path }
    }
}
```


Atualizamos main onde estávamos chamando ``parse_config`` para chamar ``Config::new``. Mudamos o nome de ``parse_config`` para new e o movemos dentro de um bloco ``impl``, que associa a nova função com ``Config``.

## Lidando um pouco com erros

Agora vamos trabalhar para melhorar nosso tratamento de erros. A primeira coisa que podemos fazer é usar o tratamento para fornecer uma mesnagem de erro caso o tamanho de args seja menor que 3. Em outras palavras, adicionamos uma verificação na nova função que verificará se a fatia é longa o suficiente antes de acessar os índices 1 e 2. Se a fatia não for longa o suficiente, o programa entra em pânico e exibe uma mensagem de erro melhor.

```
use std::env;
use std::fs;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::new(&args);

    println!("Procurando por:\n{}", config.query);
    println!("No arquivo:\n{}", config.file_path);

    let contents = fs::read_to_string(&config.file_path)
        .expect("Não foi possível abrir o arquivo");

    println!("Com o texto:\n{contents}");

    // --snip--
}

struct Config {
    query: String,
    file_path: String,
}

impl Config {
    fn new(args: &[String]) -> Config {
        if args.len() < 3 {
            panic!("Não há elementos suficientes");
        }

        let query = args.get(1).unwrap().to_owned();
        let file_path = args.get(2).unwrap().to_owned();

        Config { query, file_path }
    }
}

```
Chamamos ``.panic!()`` quando o argumento value estava fora do intervalo de valores válidos. Em vez de verificar um intervalo de valores aqui, estamos verificando se o comprimento de ``args`` é pelo menos 3 e o resto da função pode operar sob a suposição de que essa condição foi atendida. Se ``args`` tiver menos de três itens, essa condição será verdadeira, e chamamos a macro ``.panic!`` para encerrar o programa imediatamente. 

Mas fica a pergunta "podemos melhorar como lidamos com esses erros?!". Como você já deve imaginar, a resposta é sim. Podemos usar ``Result``, pois ele indica sucesso ou erro.

### Usando Result ao invés de panic!()

Em vez de usar ``panic()``, podemos retornar um valor ``Result`` que conterá uma instância ``Config`` no caso bem-sucedido e descreverá o problema no caso de erro. Também vamos mudar o nome da função de novo para compilar porque muitos programadores esperam que as novas funções nunca falhem.

Isso é ótimo, pois dessa maneira teremos maior generalidade para operacionalizar o nosso programa. Veja abixo como fica a formatação:

```
use std::env;
use std::fs;
use std::process;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::construtor(&args).unwrap_or_else(|err| {
        eprintln!("Erro ao analisar os argumentos: {}", err);
        process::exit(1);
    });

    println!("Procurando por:\n{}", config.query);
    println!("No arquivo:\n{}", config.file_path);

    let contents = fs::read_to_string(&config.file_path).unwrap_or_else(|err| {
        eprintln!("Não foi possível abrir o arquivo: {}", err);
        process::exit(1);
    });

    println!("Com o texto:\n{}", contents);

    // --snip--
}

struct Config {
    query: String,
    file_path: String,
}

impl Config {
    fn construtor(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("Não há elementos suficientes");
        }

        let query = args.get(1).unwrap().to_owned();
        let file_path = args.get(2).unwrap().to_owned();

        Ok(Config { query, file_path })
    }
}
```

Veja que fizemos algumas modificações importantes no nosso programa. A primeira coisa que fizemos foi importar ``std::process``, pois ``std::process::exit()`` pode encerrar o programa - o que é interessante caso aconteça algum erro. Devido a esse motivo, usamos closures nas variáveis ``config`` e ``contents`` que podem chamar ``exit()``. Note também que mudamos o nome ``Config::new`` para ``Config::construtor``, pois geralmente os programadores esperam que a função new sejam simples e não abram margem para erros. Além disso, implementamos Result. Em caso acertivo, Result retornará ``Config``, mas em caso de erros retornará a mensagem com erro do tipo ``&'static str``. 

Pa não deixarmos esse Hands-On muito grande, separamosele em duas partes.  Clique abaixo para acessar a segunda parte do Hands-On

# [Parte 02](/HandsOn/HD32/PARTE2.md)