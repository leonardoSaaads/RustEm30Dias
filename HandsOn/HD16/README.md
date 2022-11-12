# **Módulos - Parte 2 (Pacotes Externos, ``super`` e Separando Módulos em Diferentes Arquivos)**


## Utilizando-se ``super``

Podemos construir caminhos relativos que começam no módulo pai, em vez do módulo atual ou da raiz da grade, usando ``super`` no início do caminho. Isso é como iniciar um caminho de sistema de arquivos com a sintaxe ``..``. O uso de ``super`` nos permite referenciar um item que sabemos estar no módulo pai, o que pode facilitar a reorganização da árvore do módulo quando o módulo estiver intimamente relacionado ao pai, mas o pai pode ser movido para outro lugar na árvore do módulo algum dia.

Vamos ao exemplo de uma pizzaria:

```
fn fazer_pizza(){
    println!("Preparando Pizza...")
}

mod pizza{
    fn ketchup(){}       // Função de ketchup na Pizza
    fn mostarda(){}      // Função de mostarda na Pizza

    fn adicionar_ketchup(){
        // Função para adicionar ketchup
        ketchup();
        super::fazer_pizza();
    }
    
    fn adicionar_mostarda(){
        // Função para adicionar mostarda
        mostarda();
        super::fazer_pizza();
    }
}
```

As funções ``ketchup`` e ``mostarda`` estão no módulo ``pizza``, então podemos usar ``super`` para ir para o módulo pai de ``pizza``, que neste caso é ``crate``, ou seja, a raiz. A partir daí, procuramos por ``fazer_pizza`` e o encontramos. Sucesso! Achamos que o módulo``pizza`` e a função ``fazer_pizza`` provavelmente permanecerão no mesmo relacionamento um com o outro e serão movidos juntos caso decidamos reorganizar a árvore de módulos do engradado. Portanto, usamos ``super``, então teremos menos lugares para atualizar o código no futuro se esse código for movido para um módulo diferente.

As vezes, quando precisamos carregar vários códigos que estão no módulo pai, utilizamos o operador *glob* para essa tarefa. No código anaixo, temos um exemplo dessa situação.

```
// Acessa a conta de um banco.
fn acessar_conta(){}

// Sair da conta de um banco.
fn sair_conta(){}

// autenticador para verificar se a conta
// é da pessoa que está acessando.
fn autenticador(){}

// acessa a conta bancária e verifica
// as suas características
fn conta_bancaria(){}

// faz transferência de dinheiro para outra conta.
fn fazer_transferencia(){}

mod banco{
    fn controle_de_conta(){
        // importa todas as operações 
        // do módulo pai
        super::*;
    }
}
```

## Bibliotecas externas

Os membros da comunidade Rust disponibilizaram muitos pacotes em [crates.io](crates.io), e colocar qualquer um deles em seu pacote envolve as mesmas etapas: listá-los no arquivo *Cargo.toml* do seu pacote e usar o uso para trazer itens de suas caixas para o escopo.

Observe que a biblioteca ``std`` padrão também é uma caixa externa ao nosso pacote. Como a biblioteca padrão é fornecida com a linguagem Rust, não precisamos alterar *Cargo.toml* para incluir ``std``. Mas precisamos nos referir a ele com uso para trazer itens de lá para o escopo do nosso pacote. Por exemplo, com HashMap usaríamos esta linha:

```
use std::collections::HashMap;
```

Neste momento, itemos mostrar como é feita a importação de bibliotecas externas. Quando você estiver trabalhando em projetos rust, há um arquivo chamado *Cargo.toml* - Este arquivo contém metadados como nome, versão e dependências para pacotes, que são chamados de "caixas" em Rust - e ele será uma pela central para a importção de bibliotecas externas. É no uso de crates externos que Cargo realmente brilha. 

Lembre-se que quando utilizamos o comando ``cargo new nome_do_projeto --lib``, temos a seguinte estrutura:

```
Nome Do Projeto   // Nome do Projeto.
├── .gitignore    // Quais arquivos ignorar ao enviar ao Git.
├── Cargo.toml    // Como construir essas caixas.
└── src           // Controlador de recursos do sistema
    └── lib.rs    // Arquivo Main
```

Abra o arquivo *Cargo.toml* e adicione esta linha no final, abaixo do cabeçalho da seção ``[dependencies]`` que o Cargo criou para você. Em seguida, salve o aquivo.

```
[dependencies]

syn = "1.0.100"
```

No arquivo Cargo.toml, tudo que vem depois de um cabeçalho é parte de uma seção que segue até o início de outra. A seção ``[dependencies]`` é onde você diz ao Cargo de quais crates externos o seu projeto depende, e quais versões desses crates você exige. Neste caso, especificamos o crate ``syn`` com a versão semântica ``1.0.100``. O número 1.0.100 é, na verdade, uma forma curta de escrever 1.0.80, que significa "qualquer versão que tenha uma API pública compatível com a versão 1.0.80".

Quando você entrar na pasta e executar o comando ``cargo build``, você verá algo semelhante a:

```
>>> cargo build

Compiling unicode-ident v1.0.5
Compiling proc-macro2 v1.0.47
Compiling quote v1.0.21
Compiling syn v1.0.103
Compiling projeto_rust v0.1.0  (file:///projects/projeto_rust)
Finished dev [unoptimized + debuginfo] target(s) in 1.73s
```

Talvez pra você apareçam versões diferentes (mas elas são todas compatíveis com o código, graças ao Versionamento Semântico!), e as linhas talvez apareçam em ordem diferente.

Agora que temos uma dependência externa, Cargo busca as versões mais recentes de tudo no registro, que é uma cópia dos dados do *Crates.io*. *Crates.io* é onde as pessoas do ecossistema Rust postam seus projetos open source para que os outros possam usar.

Após atualizar o registro, Cargo verifica a seção ``[dependencies]`` e baixa todas as que você não tem ainda. Neste caso, embora tenhamos listado apenas rand como dependência, o Cargo também puxou uma cópia da libc, porque rand depende da libc para funcionar. Depois de baixá-las, o Cargo as compila e então compila nosso projeto.

Quando você quiser atualizar um crate, o Cargo tem outro comando, update, que faz o seguinte:

1º - Ignora o arquivo *Cargo.lock* e descobre todas as versões mais recentes que atendem as suas especificações no *Cargo.toml*.

2º - Se funcionar, o Cargo escreve essas versões no arquivo Cargo.lock.

Ao executar o comando, você verá a seguinte mensagem:

```
>>> cargo update

Updating crates.io index
```

Por padrão, o Cargo vai procurar as versões maiores que 1.0.100 e menores que 1.1.0. Na próxima vez que você executar ``cargo build``, depois mudar para a versão mais nova, o Cargo vai atualizar o registro de crates disponíveis e reavaliar os seus requisitos sobre o ``syn`` de acordo com a nova versão que você especificou.

Há muito mais a ser dito sobre Cargo e o seu ecossistema que vai ser discutido no, mas por ora isto é tudo que você precisa saber. Cargo facilita muito reutilizar bibliotecas, de forma que os esudiosos da linguagem consigam escrever projetos menores que são montados a partir de diversos pacotes.

## Separando Módulos em Diferentes Arquivos

Até agora, todos os exemplos neste capítulo definiram vários módulos em um arquivo. Quando os módulos ficam grandes, você pode querer mover suas definições para um separado arquivo para tornar o código mais fácil de navegar.

Neste exemplo, criaremos uma novo projeto chamado "projeto_rust" com o comando ``cargo new projeto_rust``. Ao usarmos o comando, o cargo gerará os arquivos usuais. Adicone um aquivo chamado ``lib.rs`` em branco no diretório "projeto_rust". Com isso, ficaremos com os seguintes arquivos abaixo:

![](/Imagens/HD16/exemplo_projeto.png)

Como vimos anteriormente, podemos utilizar bibliotecas externas e bibliotecas que moldamos para expandirmos o nosso projeto. Iremos simular essa situação com a utilização dos dois tipos de bibliotecas. Primeiro, no arquivo ``cargo.toml`` adicione a dependência 

```
[dependencies]
chrono = "0.4"
```

Depois, em ``src/main.rs``, coloe o código abaixo:

```
use chrono::{Local};

// https://www.w3.org/TR/xml-entity-names/025.html
const DIGITS : [[&str; 11]; 7] = [
    ["┏━┓ ","  ╻  "," ┏━┓ ", " ┏━┓ "," ╻ ╻ "," ┏━┓ "," ┏   "," ┏━┓ "," ┏━┓ "," ┏━┓ ","   "],
    ["┃ ┃ ","  ┃  ","   ┃ ", "   ┃ "," ┃ ┃ "," ┃   "," ┃   ","   ┃ "," ┃ ┃ "," ┃ ┃ "," ╻ "],
    ["┃ ┃ ","  ┃  ","   ┃ ", "   ┃ "," ┃ ┃ "," ┃   "," ┃   ","   ┃ "," ┃ ┃ "," ┃ ┃ ","   "],
    ["┃ ┃ ","  ┃  "," ┏━┛ ", " ┣━┫ "," ┗━┫ "," ┗━┓ "," ┣━┓ ","   ┃ "," ┣━┫ "," ┗━┫ ","   "],
    ["┃ ┃ ","  ┃  "," ┃   ", "   ┃ ","   ┃ ","   ┃ "," ┃ ┃ ","   ┃ "," ┃ ┃ ","   ┃ ","   "],
    ["┃ ┃ ","  ┃  "," ┃   ", "   ┃ ","   ┃ ","   ┃ "," ┃ ┃ ","   ┃ "," ┃ ┃ ","   ┃ "," ╹ "],
    ["┗━┛ ","  ╹  "," ┗━━ ", " ┗━┛ ","   ╹ "," ┗━┛ "," ┗━┛ ","   ╹ "," ┗━┛ "," ┗━┛ ","   "],
];



fn main() {
    print!("\x1b[2J");
    print!("\x1b[?25l");
    loop {
        let t = Local::now();
        let time = t.format("%H:%M:%S").to_string();
        for row in &DIGITS {
            for c in time.chars() {
                let col = match c {
                    '0'..='9' => c as usize - '0' as usize,
                    ':' => 10,
                    _ => 10,
                };
                print!("{} ", row[col]);
            }
            println!();
        }
        std::thread::sleep(std::time::Duration::from_millis(999));
        print!("\x1b[7A");
    }
}

// Esse código está disponível em:
// https://github.com/simple-rust-projects/simple-rust-clock/blob/main/src/main.rs

// O Vídeo abaixo comenta um pouco sobre o código:
// https://www.youtube.com/watch?v=gX6EFBICIcY
```