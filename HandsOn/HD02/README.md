# **Hello World & Hello Cargo**

Dentre os conceitos iniciais do Rust, podemos citar que:

- ``rustup`` é um instalador para a linguagem de programação de sistemas Rust.

- ``rustc`` é o compilador para a linguagem de programação Rust, fornecido pelo próprio projeto. Os compiladores pegam seu código-fonte e produzem código binário, seja como uma biblioteca ou executável.

- ``Cargo`` é o sistema de compilação e gerenciador de pacotes do Rust. Com esta ferramenta, você obterá uma compilação repetível porque permite que os pacotes Rust declarem suas dependências no manifesto, Cargo. tom.

Depois que o Rust é instalado via rustup, quando uma nova versão do Rust é lançada, é fácil atualizar para a versão mais recente. No seu shell, execute o seguinte script de atualização:

```
rustup update
```

Para desinstalar Rust e rustup, execute o seguinte script de desinstalação do seu shell:

```
rustup self uninstall
```

A instalação do Rust também inclui uma cópia local da documentação, para que você possa lê-la offline. Execute o comando 

```
rustup doc
```

para abrir a documentação local em seu navegador local.

![](/Imagens/HD02/RustupDoc.png)


## **Hello World**

Para começar a aprender Rust, iremos criar um novo diretório chamado **AprendendoRust**. Vale salientar que para a linguaguem de programação, não há diferença onde a pasta fica hospedada, contudo, para fins didáticos, sugerimos que a pasta fique em sua pasta Home, isto é, no Desktop. Você pode utilizar os comandos abaixos para realizar essa tarefa no Linux, macOS, PowerShell no Windows.

```
mkdir ~/aprendendorust
cd ~/aprendendorust
mkdir hello_world
cd hello_world
```

Você também pode utilizar os comandos no CMD do Windows para essa tarefa.

```
mkdir "%USERPROFILE%\aprendendorust"
cd /d "%USERPROFILE%\aprendendorust"
mkdir hello_world
cd hello_world
```

Agora, crie um programa chamado ``hello_world.rs``. Note que a extenção do Rust é dada por ``.rs``. Além disso, existe uma convenção de usar "_" como separador. Um exemplo disso é usar ``programa_um.rs`` ao invés de ``programaum.rs``.

Abra o arquivo criado com um editor e transcreva o código abaixo DE **Hello World**:

```
fn main() {
    println!("Hello, world!");
}
```

No Linux ou macOS, digite os seguintes comandos para compilar e executar o arquivo:

```
rustc main.rs
./main
```

No Windows, digite o comando ``.\main.exe`` em vez de ``./main``:

```
rustc main.rs
.\main.exe
```

Se **Hello, World!** apareceu em seu terminal, parabéns! Você escreveu oficialmente um programa Rust.Agora, você será um aspirante a programador em Rust e possui um potencial enorme. Siga os próximos Hands-On para continuar a sua jornada :)

**Algumas Observações Importantes!**

- O programa criado executa uma função chamada "main". Dentro dessa função main, é executado o comando ``println!("Hello, world!")`` que chama a macro do Rust - As macros fornecem funcionalidade semelhante às funções, mas sem o custo de tempo de execução. No entanto, há algum custo em tempo de compilação, pois as macros são expandidas durante o tempo de compilação.

- Compilar e executar são etapas separadas: Se você estiver mais familiarizado com uma linguagem dinâmica, como Ruby, Python ou JavaScript, talvez não esteja acostumado a compilar e executar um programa como etapas separadas. Rust é uma linguagem compilada com antecedência, o que significa que você pode compilar um programa e dar o executável para outra pessoa, e eles podem executá-lo mesmo sem ter o Rust instalado. Se você fornecer a alguém um arquivo .rb, .py ou .js, ele precisará ter uma implementação Ruby, Python ou JavaScript instalada (respectivamente). Mas nessas linguagens, você só precisa de um comando para compilar e executar seu programa. Tudo é uma troca no design de linguagem.


## **Hello Cargo**

Cargo é o sistema de compilação e gerenciador de pacotes do Rust. A maioria dos "Rustaceans" usa essa ferramenta para gerenciar seus projetos Rust porque o Cargo lida com muitas tarefas para você, como construir seu código, baixar as bibliotecas das quais seu código depende e construir essas bibliotecas. (Chamamos as bibliotecas que seu código precisa de dependências.)

Você pode verificar qual a sua versão do Cargo utilizando-se o código abaixo:

```
cargo --version
```
a saída deve ser semelhante a tela abaixo:

![](/Imagens/HD02/CargoVersion.png)

Neste primeiro momento com Cargo, iremos criar um projeto novo para demonstrar suas funções. Utilize o código abaixo para cria um novo diretório e projeto chamado hello_cargo.

```
cargo new hello_cargo
cd hello_cargo
```

![](/Imagens/HD02/HelloCargo.png)

Você verá que Cargo gerou dois arquivos e um diretório para nós: um arquivo Cargo.toml e um diretório src com um arquivo main.rs dentro. Uma observação importante a se falar é que ele também inicializou um novo repositório Git junto com um arquivo ``.gitignore``. Os arquivos Git não serão gerados se você executar o cargo new dentro de um repositório Git existente; você pode substituir esse comportamento usando ``cargo new --vcs=git``.

Abra o arquivo ``Cargo.toml`` e você verá uma saida semelhante ao código abaixo:

```
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

[dependencies]
```

Este arquivo está no formato .toml(Tom's Obvious, Minimal Language), que é o formato de configuração do Cargo.

A primeira linha, ``[package]``, é um cabeçalho de seção que indica que as instruções a seguir estão configurando um pacote. À medida que adicionarmos mais informações a este arquivo, adicionaremos outras seções.

As próximas três linhas definem as informações de configuração que o Cargo precisa para compilar seu programa: o nome, a versão e a edição do Rust a ser usada.

A última linha, ``[dependencies]``, é o início de uma seção para você listar qualquer uma das dependências do seu projeto.

Se você abrir a o arquivo localizado na pasta ``src``, verá que é basicamente o "Hello, World!" do Rust! Isso ocorre pois o Cargo generaliza e adota um arquivo "Hello, World!" sempre que algo novo é feito. Além disso, ele adota que esse arquivo fique na pasta ``scr`` - que significa Source Code (Código-Fonte, em português). O Cargo espera que seus arquivos de origem fiquem dentro do diretório ``src``. O diretório do projeto de nível superior é apenas para arquivos README, informações de licença, arquivos de configuração e qualquer outra coisa não relacionada ao seu código.

Agora vamos ver o que é diferente quando construímos e executamos o "Hello, world!" programa com Cargo! No diretório hello_cargo, construa seu projeto digitando o seguinte comando:

```
cargo build
```

Você verá algo semelhante:

![](/Imagens/HD02/CargoBuild1.png)

e depois

![](/Imagens/HD02/CargoBuild2.png)

Este comando cria um arquivo executável em target/debug/hello_cargo (ou target\debug\hello_cargo.exe no Windows) em vez de em seu diretório atual. Você pode executar o executável com este comando:

```
./target/debug/hello_cargo # Linux / macOS
```

ou

```
.\target\debug\hello_cargo.exe # Windows
```

Rodar o ``Cargo build`` pela primeira vez faz com que seja criado um arquivo chamado ``Cargo.lock``. Este arquivo acompanha as versões exatas das dependências em seu projeto. Como esse projeto criado não tem dependências, então o arquivo é um pouco esparso. Você nunca precisará alterar esse arquivo manualmente; A Cargo gerencia seu conteúdo para você automaticamente.

Uma alternativa ao ``Cargo build`` é o ``Cargo run``. O Cargo run serve para compilar o código e, em seguida, executar o executável resultante em um único comando. Isso faz com que tempo seja poupado, pois não será mais necessário compilar para depois abrir o arquivo executável. Na verdade, Usar o comando *run* é mais conveniente do que ter que se lembrar de executar a *build* e, em seguida, usar todo o caminho para o binário, então a maioria dos desenvolvedores usa o comando *run*.

![](/Imagens/HD02/CargoRun.png)

Cargo também fornece um comando chamado ``Cargo check``. Este comando verifica rapidamente seu código para garantir que ele seja compilado, mas não produz um executável.

![](/Imagens/HD02/CargoCheck.png)


**Recaptulando:**

- Podemos criar um projeto usando ``cargo new``.
- Podemos construir um projeto usando ``cargo build``.
- Podemos construir e executar um projeto em uma única etapa usando o ``cargo run``.
- Podemos construir um projeto sem produzir um binário para verificar erros usando o ``cargo check``.
- Em vez de salvar o resultado da compilação no mesmo diretório que nosso código, o Cargo o armazena no diretório *target/debug*.

Para mais informações sobre o Cargo, acesse https://doc.rust-lang.org/cargo/

### Acesse o código Hello, World! clicando [aqui](/Codes/HelloWorld.rs)

## **REFERÊNCIAS BIBLIOGRÁFICAS**

[1] - Hello, World!. The Rust Programming Language - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch01-02-hello-world.html>. Acesso em 09/09/2022.

[2] - Hello, Cargo!. The Rust Programming Language - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch01-03-hello-cargo.html>. Acesso em 09/09/2022.