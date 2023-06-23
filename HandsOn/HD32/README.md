# **Lidando com Arquivos**

A melhor maneira de aprender a lidar com arquivos em Rust é praticando. Neste tutorial, exploraremos como manipular diferentes tipos de arquivos usando a linguagem de programação Rust. Antes de começarmos, é importante preparar o ambiente virtual para essa tarefa. Para isso, vamos criar um novo diretório usando o comando ``cargo``:

```
cargo new lendo_arquivos
cd lendo_arquivos
```

Isso criará um novo diretório chamado ``lendo_arquivos`` com a estrutura básica de um projeto Rust e você entrará nesse diretório. Agora estamos prontos para começar a explorar como lidar com arquivos em Rust.

A primeira tarefa é fazer o ``lendo_arquivos`` aceitar dois argumentos de linha de comando: **o caminho do arquivo e uma string para pesquisar**. Queremos executar nosso programa com cargo run, dois hífens para indicar que os argumentos seguintes são para o nosso programa e não para o cargo, uma string para pesquisar e um caminho para um arquivo a ser pesquisado, assim:

```
cargo run -- searchstring example-filename.txt
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
cargo run && cargo run -- Telecom Show
```

Você verá que aparecerá algo como:

```
C:\\\\ler_arquivos>cargo run && cargo run -- Telecom Show
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target\debug\ler_arquivos.exe`
[src\main.rs:5] args = [
    "target\\debug\\ler_arquivos.exe",
]
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target\debug\ler_arquivos.exe Telecom Show`
[src\main.rs:5] args = [
    "target\\debug\\ler_arquivos.exe",
    "Telecom",
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

Primeiro, precisamos de um arquivo de exemplo para testá-lo: usaremos um arquivo com uma pequena quantidade de texto em várias linhas com algumas palavras repetidas. Crie um arquivo chamado ``poema.txt`` no nível raiz (C:...\ler_arquivos\poema.txt) do seu projeto. Você pode usar esse abaixo, por exemplo: 

<p align=“center”> Círculo vicioso<br><br> Bailando no ar, gemia inquieto vagalume:<br> “Quem me dera que eu fosse aquela loira estrela<br> Que arde no eterno azul, como uma eterna vela!”<br> Mas a estrela, fitando a lua, com ciúme:<br> “Pudesse eu copiar-te o transparente lume,<br> Que, da grega coluna à gótica janela,<br> Contemplou, suspirosa, a fronte amada e bela”<br> Mas a lua, fitando o sol com azedume:<br> “Mísera! Tivesse eu aquela enorme, aquela<br> Claridade imortal, que toda a luz resume”!<br> Mas o sol, inclinando a rútila capela:<br> Pesa-me esta brilhante auréola de nume…<br> Enfara-me esta luz e desmedida umbela…<br> Por que não nasci eu um simples vagalume?”…<br> </p>

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
cargo run -- poema poema.txt 
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