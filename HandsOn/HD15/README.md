# **Módulos - Parte 1**
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

## Pacotes e Caixas.

Uma **caixa** é a menor quantidade de código que o compilador Rust considera de cada vez. Mesmo se você executar ``rustc`` em vez de ``Cargo`` e passar um único arquivo de código fonte, o compilador considera esse arquivo como uma caixa. Caixas podem conter módulos, e os módulos podem ser definidos em outros arquivos que são compilados com a caixa. Uma caixa pode vir em uma das duas formas: uma caixa binária ou uma caixa de biblioteca. Caixas binárias são programas que você pode compilar para um executável - cada um deve ter uma função chamada ``main`` que define o que acontece quando o executável é executado -  e caixas de biblioteca não têm uma função ``main``, e não compilam para um executável. Em vez disso, as caixas de biblioteca definem a funcionalidade destinada a ser compartilhada com vários projetos.

Um **pacote** é uma ou mais caixas que fornecem um conjunto de funcionalidades. Um pacote contém um arquivo ``Cargo.toml`` que descreve como construir essas caixas. O Cargo é, na verdade, um pacote que contém a caixa binária para a ferramenta de linha de comando que você está usando para construir seu código. 

Já fizemos isso antes, mas para criarmos um pacote usamos ocomando ``cargo new`` seguido do nome escolhido para o pacote.:

```
$ cargo new my-project
    ┗ Created binary (application) `my-project` package
$ ls my-project
	┗ Cargo.toml
	┗ src
$ ls my-project/src
	┗ main.rs
```

O Cargo segue uma convenção de que ``src/main.rs`` é a raiz da caixa de uma caixa binária com o mesmo nome do pacote. A propósito, a sigla ``src`` significa System Resource Controller, ou seja, Controlador de recursos do sistema, em português. Da mesma forma, o Cargo sabe que se o diretório do pacote contém ``src/lib.rs``, o pacote contém uma caixa de biblioteca com o mesmo nome do pacote, e ``src/lib.rs`` é sua raiz de caixa. Cargo passa os arquivos raiz da caixa para construir a biblioteca ou binário.

___

Nessa seção, falaremos sobre módulos e outras partes do sistema de módulos, ou seja, caminhos que permitem nomear itens; a palavra-chave ``use`` que traz um caminho para o escopo; e a palavra-chave ``pub`` para tornar os itens públicos. Também discutiremos a palavra-chave as, pacotes externos e outros operadores.

Nesta sequência, iremos utilizar uma série de comandos e mostrar como eles são repercutidos em nosso pacote. Para melhor aprendizado, todos os códigos serão comentados.

- **1º Criando uma Biblioteca**

CARGO NEW: Tenha em mente que ao criar um novo pacote com ``cargo new (Nome Do Projeto) --lib``, essa será a arvore que o Cargo irá forncer:

```
Nome Do Projeto   // Nome do Projeto.
├── .gitignore    // Quais arquivos ignorar ao enviar ao Git.
├── Cargo.toml    // Como construir essas caixas.
└── src           // Controlador de recursos do sistema
    └── lib.rs    // Arquivo Main
```

Quando usamos ``--lib`` significa que iremos lidar com uma biblioteca. Tanto que se abrirmos o arquivo ``lib.rs``, veremos que há um arquivo *root* dado por:

```
pub fn add(left: usize, right: usize) -> usize {
    left + right
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_works() {
        let result = add(2, 2);
        assert_eq!(result, 4);
    }
}
```

Poderemos mudar esse arquivo para inserir ``mod``, como veremos abaixo.

- **2º Usando Módulos com MOD** 

Diferentemente de outras linguagens de programação, Rust não utiliza um arquivo como módulo. Nesta linguagem de programação, você pode ter um único arquivo com centenas e até milhares de módulos e submodulos em um único arquivo. Em python, por exemplo, ao criarmos um script *carros.py*, ele será considerado um módulo. Veja o que diz a documentação oficial de Python:

*"A module is a file containing Python definitions and statements. The file name is the module name with the suffix .py appended. Within a module, the module’s name (as a string) is available as the value of the global variable __name__."*

No arquivo *root* (raiz, em português), você pode declarar novos módulos; digamos, você declara um módulo “animais” com o comando``mod animais;``. O compilador procurará o código do módulo nestes locais, respectivamente:
- Inline, entre colchetes que substituem o ponto e vírgula seguindo o ``mod animais``.
- No arquivo src/garden.rs
- No arquivo src/garden/mod.rs

Em ``lib.rs``, exclua a programação vigente e coloque o seguinte módulo:

```
// Criando um módulo
mod animais{
    // Cria uma função dentro do módulo animais.
    // OBS: essa função é privada por default.
    fn cachorro(){
        println!("au au au!!!");
    }

    // Cria uma função dentro do módulo animais.
    // OBS: essa função é privada por default.
    fn gato(){
        println!("miau miau miau!!!");
    }

    // Cria uma função dentro do módulo animais.
    // OBS: essa função é privada por default.
    fn boi(){
        println!("muuu muuu muuu!!!");
    }
}
```

Após a palavra-chave ``mod``, colocamos o nome do módulo, ``animais`` e, em seguida, um bloco de código entre chaves. Tudo dentro deste bloco está dentro do *namespace* ``animais``.  Isso significa que se nós quisermos chamar uma função do código fora do módulo animais, nós precisaremos especificar o módulo e usar a sintaxe do namespace ``::``, assim: ``animais::gato()`` em vez de apenas ``gato()``.

Também podemos ter múltiplos módulos, lado a lado, no mesmo arquivo lib.rs. Por exemplo, para ter mais um módulo ``fazenda`` que possui uma função chamada ``marreco`` , podemos adicioná-lo como mostrado abaixo:

```
// Criando um módulo
mod animais{
    // Cria uma função dentro do módulo animais.
    // OBS: essa função é privada por default.
    fn cachorro(){
        println!("au au au!!!");
    }

    // outros animais...
}

mod fazenda{
    fn marreco(){
        println!("quac quac!!!");
    }
}
```

Agora, temos uma função ``animais::cachorro()`` e uma função ``fazenda::marreco()``. Estas podem ter funcionalidades completamente diferentes, e os nomes das funções não estão em conflito entre si porque estão em módulos diferentes.

Nesse caso, como estamos construindo uma biblioteca, o arquivo que serve como ponto de entrada para construir nossa biblioteca é src/lib.rs. No entanto, em relação a criação de módulos, não há nada de especial sobre src/lib.rs. Poderíamos também criar módulos em src/main.rs para um crate binário da mesma forma que nós criamos módulos em src/lib.rs para o crate de biblioteca. 

Na verdade, podemos colocar módulos dentro de módulos, o que pode ser útil à medida que seus módulos crescem para manter juntas funcionalidades relacionadas e separar funcionalidades não relacionadas. A escolha de como você organiza seu código depende do que você pensa sobre a relação entre as partes do seu código. Por exemplo, o código animais e as funções imbutidas podem ter mais sentido para os usuários de nossa biblioteca se eles estivessem dentro do *namespace* fazenda, como é visto abaixo:

```
mod fazenda{
    mod animais{
        fn cachorro(){
            println!("au au au!!!");
        }
        fn marreco(){
            println!("quac quac!!!");
        }
    // outros animais ...
    }

    fn colocar_comida(){
        println!("Colocando comida para os animais...");
    }
}
```

Agora temos as funções ``fazenda::animais::cachorro()`` e ``fazenda::animais::marreco()``;Novamente, as duas funções denominadas dentro de ``animais`` não conflitam com a função ``colocar_comida``, tendo em vista que estão em diferentes *namespaces*. Podemos notar que há uma organização que lembram os galhos ou ramos (*branches*, em inglês) de uma arvore.

Desta forma, os módulos formam uma estrutura de dados que lembram uma arvore. O conteúdo de src/lib.rs está no nível superior mais alto, e os submódulos estão em níveis mais baixos. Aqui está a nossa organização quando pensada de forma de ramos:

```
lib.rs                         // root.
   └── fazenda                 // módulo fazenda.
       └── animais             // módulo animais.
           └── cachorro        // função cachorro.
           └── marreco         // função marreco.
        └── colocar_comida     // função de repor a comida.
```

Projetos mais complicados podem ter muitos módulos, é necessário organizá-los logicamente para mantê-los sob controle. O que "logicamente" significa em seu projeto fica a seu critério, e depende do que você e os usuários da sua biblioteca pensam sobre o domínio do seu projeto.

## REFERÊNCIAS BIBLIOGRÁFICAS:

[1] - https://doc.rust-lang.org/book/ch07-01-packages-and-crates.html

[2] - https://doc.rust-lang.org/book/ch07-02-defining-modules-to-control-scope-and-privacy.html

[3] - https://doc.rust-lang.org/book/ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html

[4] - 