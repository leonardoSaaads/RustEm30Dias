# **Tratamento de Erros 01**

A maioria dos idiomas não distingue entre esses dois tipos de erros e manipula ambos da mesma forma, utilizando mecanismos como exceções. Ferrugem não tem Exceções. Em vez disso, ele tem o tipo ``Result<T, E>`` para erros recuperáveis e a macro ``panic!`` que interrompe a execução quando o programa encontra um erro irrecuperável. Este capítulo aborda o chamado ``panic!`` primeiro e depois ``Result<T, E>`` para devolução de valores.

![](/Imagens/HD22/cesar_error.png)

## **DEU ``Panic!`` NO SISTEMA!!!**

Na maioria das vezes, erros irão acontecer naturalmente a medida que seus projetos ficam grandes. Não á como escapar dessa dura realidade. Apesar de Rust ser uma linguagem performática e muito segura, algumas lógicas entram em conflito e, desses conflitos, emergem erros! 

Mas, interessantemente, Rust possui o macro ``panic!`` ( As macros possuem exclamação no final - ex: ``panic!``, ``print!``, etc). Na prática, existem duas maneiras de causar um pânico: executando uma ação que faz com que nosso código entre em pânico (como acessar um array após o final) ou chamando explicitamente o método ``panic!`` macro. Em ambos os casos, causamos pânico em nosso programa. Por padrão, esses panics imprimirão uma mensagem de falha, desenrolarão, limparão a pilha e sairão.

___

### Desenrolando a Stack ou Abortando em Resposta a um Pânico

Por padrão, quando ocorre um pânico, o programa começa a desenrolar, o que significa que o Rust volta para a Stack e limpa os dados de cada função que encontra. No entanto, essa caminhada de volta e limpeza é muito trabalhosa. Rust, portanto, permite escolher a alternativa de abortar imediatamente, o que encerra o programa sem limpeza.

A memória que o programa estava usando precisará ser limpa pelo sistema operacional. Se em seu projeto você precisar tornar o binário resultante o menor possível, você pode mudar de unwinding para abortar em um pânico adicionando panic = 'abort' às seções [profile] apropriadas em seu arquivo Cargo.toml. Por exemplo, se você deseja abortar em pânico no modo de liberação, adicione isto:

```
[profile.release]
panic = 'abort'
```
___


Vamos testar um código simples para irmos nos acostumando aos poucos com esse atributo. Veja o exemplo abaixo:

```
fn main() {
    panic!("crash and burn");
}
```

Você verá que a saída será a seguinte:

```
Finished dev [unoptimized + debuginfo] target(s) in 0.59s
Running `target/debug/playground`
thread 'main' panicked at 'crash and burn', src/main.rs:2:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

A chamada para o ``pânic!`` causa a mensagem de erro contida nas duas últimas linhas. A primeira linha mostra nossa mensagem de pânico e o local em nosso código-fonte onde ocorreu o pânico: ``src/main.rs:2:5`` indica que é a segunda linha, quinto caractere do nosso arquivo ``src/main.rs``. Em outros casos, o ``pânic!``pode estar no código que nosso código chama, e o nome do arquivo e o número da linha relatados pela mensagem de erro serão o código de outra pessoa onde o ``panic!`` macro é chamada, não a linha do nosso código que eventualmente levou ao ``pânic!`` acionar.

Vejamos outro exemplo para ver como é quando um ``pânic!`` chamada vem de uma biblioteca devido a um bug em nosso código, em vez de nosso código chamando a macro diretamente.

### Usando um ``pânic!``  p/ rastreamento

Vejamos o exemplo de um código:

```
fn main() {
    let vetor = vec![1, 2, 3, 40, 78];
    vetor[69]; // Não tem no vetor
}
```

Aqui, estamos tentando acessar o 70º elemento do nosso vetor (que está no índice 69 porque a indexação começa em zero), mas o vetor tem apenas 5 elementos. Nesta situação, Rust entrará em pânic!. Usar [] deve retornar um elemento, mas se você passar um índice inválido, não há nenhum elemento que o Rust possa retornar aqui que esteja correto.

Em C, tentar ler além do final de uma estrutura de dados é um comportamento indefinido. Você pode obter o que quer que esteja no local da memória que corresponda a esse elemento na estrutura de dados, mesmo que a memória não pertença a essa estrutura. Isso é chamado de *buffer overread* e pode levar a vulnerabilidades de segurança se um invasor for capaz de manipular o índice de forma a ler dados que não deveriam ser permitidos e que são armazenados após a estrutura de dados.

Para proteger seu programa desse tipo de vulnerabilidade, se você tentar ler um elemento em um índice que não existe, o Rust interromperá a execução e se recusará a continuar. Veja abaixo:

```

Compiling panic v0.1.0 (file:///projects/panic)
Finished dev [unoptimized + debuginfo] target(s) in 0.27s
    Running `target/debug/panic`
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 69', src/main.rs:4:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Esse erro aponta para a linha 4 de nosso ``main.rs``, onde tentamos acessar o índice 69. A próxima linha de observação nos diz que podemos definir a variável de ambiente ``RUST_BACKTRACE`` para obter um backtrace exatamente do que aconteceu para causar o erro. Um *backtrace* é uma lista de todas as funções que foram chamadas para chegar a este ponto. *Backtraces* em Rust funcionam como em outras linguagens: a chave para ler o *backtrace* é começar do topo e ler até ver os arquivos que você escreveu. Esse é o local onde o problema se originou. As linhas acima desse ponto são o código que seu código chamou; as linhas abaixo são códigos que chamaram seu código.

### A importância do ``unwrap`` e ``expect``!

Ao escrever um exemplo para ilustrar algum conceito, incluir também um código robusto de tratamento de erros pode tornar o exemplo menos claro. Nos exemplos, entende-se que uma chamada para um método como ``unwrap``, que pode entrar em pânico, é um espaço reservado para a maneira como você deseja que seu aplicativo lide com erros, que podem diferir com base no que o restante do seu código está fazendo.

Em outras palavras, ``unwrap`` algo em Rust é algo como "Coloque o resultado da execução e, se houve um erro, entre em pânico e pare o programa". Não é raro ver essa execução em vários projeto. Dê uma pequena olhada no Github e veja que vários projetos utilizam essa chamada para proteção! Deste jeito, preste atenção neste assunto, pois é muito importante!!

Veja um exemplo:

```
use std::env;

fn main() {
    let mut argv = env::args();
    let arg: String = argv.nth(1).unwrap(); // erro 1
    let n: i32 = arg.parse().unwrap();      // erro 2
    println!("{}", 2 * n);
}
```

Você não precisa entender esse código a fundo, mas tenha em mente que se você der a este programa zero argumentos (erro 1) ou se o primeiro argumento não é um inteiro (erro 2), o programa entrará em pânico como no primeiro exemplo.

Da mesma forma, os métodos ``unwrap`` e ``expect`` são muito úteis durante a prototipagem, antes que você esteja pronto para decidir como lidar com os erros. Eles deixam marcadores claros em seu código para quando você estiver pronto para tornar seu programa mais robusto.

Se uma chamada de método falhar em um teste, você deseja que todo o teste falhe, mesmo que esse método não seja a funcionalidade em teste. Porque ``pânic!`` é como um teste é marcado como uma falha, chamar ``unwrap`` ou ``expect`` é exatamente o que deve acontecer.

Veja outro exemplo:

```
fn aprox_log(x: f32) -> f32 {
    if x <= 0. {
        panic!("Não há log negativo: {}!!", x);
    }
    if x > 0. && x < 10.{
        return 0.5;
    }
    if x > 10. && x < 100.{
        return 1.5;
    }
    2.0
}

fn main() {
    println!("{}", aprox_log(11.));  // Ok
    println!("{}", aprox_log(300.)); // Ok
    println!("{}", aprox_log(-11.)); // Erro!! Não há log negativo
}
```

Além disso, podemos simplificar o conceito de ``expect`` grosseiramente como um "unwrap com mensagem". Usar ``expect`` em vez de ``unwrap`` e fornecer boas mensagens de erro pode transmitir sua intenção e facilitar o rastreamento da origem de um pânico. A sintaxe de expect é assim:

```
use std::fs::File;

fn main() {
    let greeting_file = File::open("hello.txt")
        .expect("hello.txt should be included in this project");
}
```

Usamos expect da mesma forma que unwrap: para retornar o manipulador de arquivo ou chamar o panic! macro. A mensagem de erro usada por expect em sua chamada para panic! será o parâmetro que passamos para esperar, ao invés do panic padrão! mensagem que desempacotar usa. Aqui está o que parece:

```
thread 'main' panicked at 'hello.txt should be included in this project: Os {
code: 2, kind: NotFound, message: "No such file or directory" }',
src/main.rs:5:10
```

No código de qualidade de produção, a maioria dos Rustaceans escolhe ``expect`` em vez de ``unwrap`` e fornecer mais contexto sobre por que a operação deve sempre ser bem-sucedida.

## Erros com Result

A maioria dos erros não é grave o suficiente para exigir que o programa pare completamente. Às vezes, quando uma função falha, é por um motivo que você pode facilmente interpretar e responder. Por exemplo, se você tentar abrir um arquivo e que operação falha porque o arquivo não existe, talvez você queira criar o em vez de encerrar o processo.

a enum ``Result``(Que recebe dois tipos genéricos - veremos mais a frente) é definida como tendo duas variantes, ``Ok`` e ``Err``, da seguinte forma:

```
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

O que você precisa saber agora é que ``T`` representa o tipo do valor que será retornado em caso de sucesso dentro da variante ``Ok``, e ``E`` representa o tipo de erro que será retornado em caso de falha dentro da variante ``Err``. Como ``Result`` tem esses parâmetros de tipo genérico, podemos usar o tipo ``Result`` e as funções definidas nele em muitas situações diferentes em que o valor de sucesso e o valor de erro que queremos retornar podem ser diferentes.

Vamos chamar uma função que retorna um valor ``Result`` porque a função pode falhar. Já vimos acima que a função ``File::open`` necessita desse arquivo (por isso usamos expect).

```
use std::fs::File;

fn main() {
    let abrir_arquivo = File::open("hello.txt");
}

```

O tipo de retorno de ``File::open`` é ``Result<T, E>``. O parâmetro genérico ``T`` foi preenchido pela implementação de ``File::open`` com o tipo de valor de sucesso, ``std::fs::File``, que é um identificador de arquivo. O tipo de ``E`` usado no valor do erro é ``std::io::Error``. Esse tipo de retorno significa que a chamada para ``File::open`` pode ser bem-sucedida e retornar um identificador de arquivo do qual podemos ler ou gravar. A chamada de função também pode falhar: por exemplo, o arquivo pode não existir ou podemos não ter permissão para acessar o arquivo. A função ``File::open`` precisa ter uma maneira de nos dizer se foi bem-sucedida ou falhou e, ao mesmo tempo, fornecer o identificador do arquivo ou informações de erro. Essa informação é exatamente o que a enum``Result`` transmite.

- 👌 No caso em que ``File::open`` for bem-sucedido, o valor na variável ``abrir_arquivo`` será uma instância de ``Ok`` que contém um identificador de arquivo.

- 🤦 No caso de falha, o valor em ``abrir_arquivo`` será uma instância de ``Err`` que contém mais informações sobre o tipo de erro ocorrido.

Para essa situação, é tulizado o operador ``Match`` (Hands-On 14) para validar a operação. Veja bem:

```
use std::fs::File;

fn main() {
    let abrir_arquivo = File::open("hello.txt");

    let work_file = match abrir_arquivo {
        // Se okay, retorna o arquivo
        Ok(file) => file,
        // Se der erro, ative panic! e pare o programa.
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```

Quando o resultado for ``Ok``, esse código retornará o valor do arquivo interno da variante ``Ok`` e, em seguida, atribuiremos esse valor de identificador de arquivo à variável ``work_file``. Após a partida, podemos usar o identificador de arquivo para leitura ou gravação.

O outro braço da correspondência lida com o caso em que obtemos um valor ``Err`` de ``File::open``. Neste exemplo, escolhemos chamar o ``pânic!`` macro.

**Mas a abordagem sempre será assim?!?**

A resposta para essa pergunta é NÃO! Podemos lidar diferentes tipos de erros. Se desejarmos tomar ações diferentes para diferentes motivos de falha, podemos montar uma estrutura para isso. Veja o exemplo: se ``File::open`` falhou porque o arquivo não existe, queremos criar o arquivo e retornar o identificador para o novo arquivo. Se ``File::open`` falhou por qualquer outro motivo - por exemplo, porque não tínhamos permissão para abrir o arquivo - ainda queremos que o código entre em ``pânic!`` da mesma forma que vimos acima. Para isso adicionamos uma expressão de correspondência interna, mostrado abaixo.

```
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let abrir_arquivo = File::open("hello.txt");

    let work_file = match abrir_arquivo {
        // Se okay, retorna o arquivo
        Ok(file) => file,
        // Se deu erro, analise o tipo de erro
        Err(error) => match error.kind() {
            // Se não achou o arquivo, crie ele e valide sua criação.
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            // Caso seja outra coisa, acione a macro panic!
            other_error => {
                panic!("Problem opening the file: {:?}", other_error);
            }
        },
    };
}
```

A enum ``io::ErrorKind`` é fornecida pela biblioteca padrão e possui variantes que representam os diferentes tipos de erros que podem resultar de uma operação io. A variante que queremos usar é ``ErrorKind::NotFound``, que indica que o arquivo que estamos tentando abrir ainda não existe. Portanto, correspondemos em ``abrir_arquivo``, mas também temos uma correspondência interna em ``error.kind()``.

A condição que queremos verificar na correspondência interna é se o valor retornado por ``error.kind()`` é a variante ``NotFound`` da enum ``ErrorKind``. Se for isso mesmo, tentamos criar o arquivo com ``File::create``. No entanto, como ``File::create`` também pode falhar, precisamos de um segundo braço na expressão de correspondência interna.

___

**OBS IMPORTANTE**

Apesar de ``matchs`` serem ótimos, eles não são tão usados para essa parte de erros. Os opradores Closures(Veremos no último Hands-On) são mais usados por serem funções anônimas que você pode salvar em uma variável ou passar como argumentos para outras funções. Eles são precidos, para quem já viu Python, com as funções "lambdas" pythonicas.

Veja um exemplo abaixo de como são usados para o teratamento de erros:

```
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let greeting_file = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {:?}", error);
            })
        } else {
            panic!("Problem opening the file: {:?}", error);
        }
    });
}
```

Embora esse código tenha o mesmo comportamento do código com match, ele não contém nenhuma expressão correspondente e é mais fácil de ler.

___

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/wM6o70NAWUI/0.jpg)](https://www.youtube.com/watch?v=wM6o70NAWUI)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD23/README.md)

## REFERÊNCIAS BIBLIOGRÀFICAS

[1] - Error Handling. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch09-00-error-handling.html>. Acesso em 17 de janeiro de 2023.

Imagem - Criação Própria.
