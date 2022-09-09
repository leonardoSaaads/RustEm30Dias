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



## **REFERÊNCIAS BIBLIOGRÁFICAS**

[1] - Hello, World!. The Rust Programming Language - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch01-02-hello-world.html>. Acesso em 09/09/2022.

[2] - Hello, Cargo!. The Rust Programming Language - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/book/ch01-03-hello-cargo.html>. Acesso em 09/09/2022.