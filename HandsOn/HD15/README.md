# **Módulos - Parte 1 (``Mod``, ``pub`` e ``use``)**
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

**Criando uma Biblioteca**

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

## Usando Módulos com ``mod``

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

Para mostrar ao Rust onde encontrar um item em uma árvore de módulo, usamos um caminho da mesma forma que usamos um caminho ao navegar em um sistema de arquivos. Para chamar uma função, precisamos saber seu caminho.

Um caminho pode tomar duas formas:

- Um caminho absoluto é o caminho completo a partir de uma raiz de caixa; para código de uma caixa externa, o caminho absoluto começa com o nome da caixa, e para o código da caixa atual, ele começa com o ``crate``. 

- Um caminho relativo começa a partir do módulo atual e usa ``self``,``super`` ou um identificador no módulo atual. Ambos os caminhos absolutos e relativos são seguidos por um ou mais identificadores separados por cólons duplos (``::``).

Veja um exemplo:

```
// Absolute path
crate::fazenda::animais::cachorro();
// Relative path
fazenda::animais::cachorro();
```

No caminho absoluto, começamos com o ``crate``, a raiz da árvore de módulos do nosso programa. Com o caminho relativo, não é feito esse procedimento.

Se você planeja compartilhar sua grade de biblioteca para que outros projetos possam usar seu código, sua API pública é seu contrato com os usuários de sua grade que determina como eles podem interagir com seu código. Há muitas considerações sobre o gerenciamento de alterações em sua API pública para tornar mais fácil para as pessoas dependerem de sua grade. Essas considerações estão fora do escopo deste livro; se você estiver interessado neste tópico, consulte [The Rust API Guidelines](https://rust-lang.github.io/api-guidelines/).

## Métodos Públicos com ``pub``

Como foi falado anteriormente, os métodos definidos em módulos são privados por *Default*. Isso significa que, caso você tente chamar um método em um módulo que não esteja público, um erro será exibido. Vamos retornar ao exemplo da fazenda. Iremos criar uma função main e iremos tentar chamar a função ``colocar_comida``.

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

fn main(){
    fazenda::colocar_comida();
}
```

Teremos o seguinte erro:

```
error[E0603]: function `colocar_comida` is private
  --> src/main.rs:18:14
   |
18 |     fazenda::colocar_comida();
   |              ^^^^^^^^^^^^^^ private function
   |
note: the function `colocar_comida` is defined here
  --> src/main.rs:12:5
   |
12 |     fn colocar_comida(){
   |     ^^^^^^^^^^^^^^^^^^^

For more information about this error, try `rustc --explain E0603`.
error: could not compile `playground` due to previous error
```

Devido a isso, temos utilizar métodos públicos. Para controlar se as interfaces podem ser usadas entre os módulos, o Rust verifica cada uso de um item para ver se deve ser permitido ou não. É aqui que os avisos de privacidade são gerados ou, de outra forma, "você usou um item privado de outro módulo e não teve permissão para isso".

Com duas exceções para métodos privados, temos: Itens associados em um ``pub Trait`` são públicos por padrão; As variantes de enumeração em um ``pub enum`` também são públicas por padrão. Quando um item é declarado como ``pub``, significa que ele **pode ser considerado acessível ao mundo exterior**. Por exemplo:

```
// Quando colocamos pub, essa função poderá ser acessível fora do namespace "fazenda".
pub fn colocar_comida(){
    println!("Colocando comida para os animais...");
}
```

Isso fará com que o código compile corretamente. Note que ``pub`` são um pilar fundamental para a criação de módulos e bibliotecas - tendo em vista que eles são controladores de segurança. A palavra-chave ``pub`` em um módulo apenas permite que o código em seus módulos ancestrais se refiram a ela, não acesse seu código interno. Como os módulos são contêineres, não há muito que possamos fazer apenas tornando o módulo público; precisamos ir além e optar por tornar um ou mais itens do módulo públicos também.

No geral, estas são as regras para a visibilidade do item:

- Se um item for público, ele pode ser acessado através de qualquer um dos seus módulos pais.

- Se um item é privado, ele só pode ser acessado por seu módulo pai imediato e qualquer um dos módulos filhos do pai.

Também podemos usar pub para designar structs e enums como públicos, mas há alguns detalhes extras para o uso de pub com structs e enums. Se usarmos pub antes de uma definição de struct, tornamos o struct público, mas os campos do struct ainda serão privados.

Veja o exemplo abaixo:

```
mod back_of_house {
    pub struct Breakfast {
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    // Order a breakfast in the summer with Rye toast
    let mut meal = back_of_house::Breakfast::summer("Rye");
    // Change our mind about what bread we'd like
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // The next line won't compile if we uncomment it; we're not allowed
    // to see or modify the seasonal fruit that comes with the meal
    // meal.seasonal_fruit = String::from("blueberries");
}
```

Veja mais em relação em métodos públicos clicando-se [aqui](https://doc.rust-lang.org/book/ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html)

## Utilizando modulos com ``use``

Adicionar ``use`` e um caminho em um escopo é semelhante a criar um link simbólico no sistema de arquivos. Ao adicionar use ``crate::front_of_house::hosting`` na raiz da grade, ``hosting`` agora é um nome válido nesse escopo, como se o módulo de hospedagem tivesse sido definido na raiz da grade. Os caminhos trazidos ao escopo com o uso também verificam a privacidade, como qualquer outro caminho. Observe que o ``use`` cria apenas o atalho para o escopo específico em que o ``use`` ocorre.

Em Python, usamos uma formatação semelhante a seguinte:

```
import numpy.fft.fft as algum_nome
```

Em rust, a operação é semelhante. Usamos, quase que com própositos iguais em relação ao python, a seguinte formatação:

```
use numpy::fft::fft as algum_nome;
```

A palavra-chave use de Rust encurta as chamadas de função longas, trazendo os módulos e a função que deseja chamar para o escopo. A palavra-chave use traz apenas o que especificamos no escopo: ela não leva os filhos dos módulos ao escopo. Veja um exemplo abaixo:

```
pub mod primeiro {
    pub mod segundo {
        pub mod terceiro {
            pub fn funcao_0(){
                // realiza alguma função
                println!("Operação Completa!");
            }
            pub fn funcao_1(){
                // realiza outra funcao
                println!("Operação Completa!");
            }
        }
    }
}

use primeiro::segundo::terceiro;

fn main() {
    // note que ainda precisamos puxar a função 0
    terceiro::funcao_0();

    // mesma coisa para a função 1
    terceiro::funcao_1();
}
```

Como as enums também formam uma espécie de *namespace*, assim como os módulos, podemos trazer as variantes de uma enum para o escopo com ``use`` também. Para qualquer tipo de declaração de use se você estiver trazendo vários itens de um namespace para o escopo, você pode listá-los usando chaves e vírgulas na última posição, assim:

```
enum TrafficLight {
    Red,
    Yellow,
    Green,
}

use TrafficLight::{Red, Yellow};

fn main() {
    let red = Red;
    let yellow = Yellow;
    let green = TrafficLight::Green;
}

```

## Trazendo implementações com *gLob*

Para trazer todos os itens de um *namespace* para o escopo ao mesmo tempo, podemos usar a sintaxe ``*`` que é chamada de operador *glob*. Você deve usar globs com moderação: eles são convenientes, mas isso pode também trazer mais itens do que se esperava e causar conflitos de nomeação.

Veja um exemplo:

```
enum Point{
    x,
    y,
    z,
}

use Point::*;  // Traz toda as características contidas 
               // contidas no namespace Point

fn main() {
    let x_position = x;
    let y_position = y;
    let z_position = z;
}
```

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/969j0qnJGi8/0.jpg)](https://www.youtube.com/watch?v=969j0qnJGi8)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️[Clique Aqui](/HandsOn/HD16/README.md)

## REFERÊNCIAS BIBLIOGRÁFICAS:

[1] - Packages and Crates. The Rust Programming Language - doc.rust-lan.org. Disponível em: <https://doc.rust-lang.org/book/ch07-01-packages-and-crates.html>. Acesso em 20/10/2022

[2] - Defining Modules to Control Scope and Privacy. The Rust Programming Language - doc.rust-lan.org. Disponível em: <https://doc.rust-lang.org/book/ch07-02-defining-modules-to-control-scope-and-privacy.html>. Acesso em 20/10/2022

[3] - Paths for Referring to an Item in the Module Tree. The Rust Programming Language - doc.rust-lan.org. Disponível em: <https://doc.rust-lang.org/book/ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html>. Acesso em 20/10/2022

[4] - Visibility and Privacy. The Rust Reference  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/reference/visibility-and-privacy.html>. Acesso em 22/10/2022