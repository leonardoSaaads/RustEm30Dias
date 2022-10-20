# **Módulos - parte 1**

## Para que serve Módulos?

Os programas que escrevemos até agora foram em um módulo em um arquivo. À medida que um projeto cresce, você deve organizar o código dividindo-o em vários módulos e, em seguida, vários arquivos. Um pacote pode conter várias caixas e, opcionalmente, uma caixa biblioteca. À medida que um pacote cresce, você pode extrair peças em caixas separadas que se tornam dependências externas. Este capítulo abrange todas essas técnicas. Para projetos muito grandes que compõem um conjunto de pacotes interrelacionados que evoluem juntos, o ``Cargo`` fornece espaços de trabalho. Vamos definir alguns conceitos: 

- **Pacotes**: Um recurso que o Cargo permite construir, testar e compartilhar caixas.
- **Caixas**: Uma árvore de módulos que produz uma biblioteca ou executável.
- **Módulos**: Deixe você controlar a organização, o escopo e a privacidade dos caminhos.
- **Caminhos**: Uma maneira de nomear um item, como uma estrutura, função ou módulo.

Da mesma forma que você extrai uma lógica das linhas de código em uma função, você pode extrair funções (e outros códigos, como structs e enums) em diferentes módulos. Um módulo é um *namespace* que contém definições de funções ou tipos, e você pode escolher se essas definições são visíveis fora de seu módulo (público) ou não (privado).

- A palavra-chave ``mod`` declara um novo módulo.
- Por padrão, as funções, tipos, constantes e módulos são privados. A palavra-chave ``pub``    torna um item público e, portanto, visível fora do seu *namespace*.
- A palavra-chave ``use`` traz módulos, ou as definições dentro dos módulos, ao escopo.

___

## Pacotes e Caixas.

Uma **caixa** é a menor quantidade de código que o compilador Rust considera de cada vez. Mesmo se você executar ``rustc`` em vez de ``Cargo`` e passar um único arquivo de código fonte, o compilador considera esse arquivo como uma caixa. Caixas podem conter módulos, e os módulos podem ser definidos em outros arquivos que são compilados com a caixa. Uma caixa pode vir em uma das duas formas: uma caixa binária ou uma caixa de biblioteca. Caixas binárias são programas que você pode compilar para um executável - cada um deve ter uma função chamada ``main`` que define o que acontece quando o executável é executado -  e caixas de biblioteca não têm uma função ``main``, e não compilam para um executável. Em vez disso, as caixas de biblioteca definem a funcionalidade destinada a ser compartilhada com vários projetos.

Um **pacote** é uma ou mais caixas que fornecem um conjunto de funcionalidades. Um pacote contém um arquivo ``Cargo.toml`` que descreve como construir essas caixas. O Cargo é, na verdade, um pacote que contém a caixa binária para a ferramenta de linha de comando que você está usando para construir seu código. 

Já fizemos isso antes, mas para criarmos um pacote usamos ocomando ``cargo new`` seguido do nome escolhido para o pacote.:

```
$ cargo new my-project
     Created binary (application) `my-project` package
$ ls my-project
Cargo.toml
src
$ ls my-project/src
main.rs
```

O Cargo segue uma convenção de que ``src/main.rs`` é a raiz da caixa de uma caixa binária com o mesmo nome do pacote. A propósito, a sigla ``src`` significa System Resource Controller, ou seja, Controlador de recursos do sistema, em português. Da mesma forma, o Cargo sabe que se o diretório do pacote contém ``src/lib.rs``, o pacote contém uma caixa de biblioteca com o mesmo nome do pacote, e ``src/lib.rs`` é sua raiz de caixa. Cargo passa os arquivos raiz da caixa para construir a biblioteca ou binário.

Tenha em mente que ao criar um novo pacote com ``cargo new``, essa será a arvore que o Cargo irá forncer:

```
Nome Do Projeto   // Nome do Projeto.
├── .gitignore    // Quais arquivos ignorar ao enviar ao Git.
├── Cargo.toml    // Como construir essas caixas.
└── src           // Controlador de recursos do sistema
    └── main.rs   // Arquivo Main
```

Isso será útil para quando falarmos e estabelecermos módulos. 

___

Nessa seção, falaremos sobre módulos e outras partes do sistema de módulos, ou seja, caminhos que permitem nomear itens; a palavra-chave use que traz um caminho para o escopo; e a palavra-chave pub para tornar os itens públicos. Também discutiremos a palavra-chave as, pacotes externos e o operador glob.


## REFERÊNCIAS BIBLIOGRÁFICAS:

[1] - https://doc.rust-lang.org/book/ch07-01-packages-and-crates.html

[2] - https://doc.rust-lang.org/book/ch07-02-defining-modules-to-control-scope-and-privacy.html

[3] - https://doc.rust-lang.org/book/ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html

[4] - 