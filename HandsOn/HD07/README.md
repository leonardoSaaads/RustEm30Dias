# **Ownership (Posse)**

Ownership (posse) é a característica única do Rust que o torna especial, permite ter garantias de segurança de memória sem precisar de um garbage collector. Logo, é importante entender como funciona ownership no Rust.

Resumidamente, todos os programas têm que decidir de que forma vão usar a memória do computador durante a execução. Em Rust, é utilizado uma abordagem diferente no seguinte aspecto: a memória é gerenciada através de um sistema de posse, que tem um conjunto de regras verificadas em tempo de compilação. Nenhuma característica relacionada ao ownership implica qualquer custo em tempo de execução.

Como ownership é um conceito novo para muitos programadores, leva um pouco de tempo para se acostumar. Assim sendo, quanto mais você programar em Rust, mais familiaridade terá com esse tipo de conceito.

Antes de falarmos propriamente dito em Ownership, precisamos entender o que são as memórias *Heap e Stack*

![](/Imagens/HD07/HeandAndStack.png)

### Memória Stack

Alocação em Stack: A alocação acontece em blocos contíguos de memória. Chamamos de alocação de memória de *Stack* porque a alocação acontece na pilha de chamadas de função. O tamanho da memória a ser alocada é conhecido pelo compilador e sempre que uma função é chamada, suas variáveis recebem memória alocada na pilha. E sempre que a chamada de função acaba, a memória para as variáveis é deslocada. Tudo isso acontece usando algumas rotinas predefinidas no compilador. Um programador não precisa se preocupar com a alocação de memória e a desocupação de variáveis de pilha. Esse tipo de alocação de memória também conhecida como alocação temporária de memória porque assim que o método termina sua execução todos os dados pertencem a esse método sai automaticamente da pilha. Significa que qualquer valor armazenado no esquema de memória da pilha é acessível desde que o método não tenha concluído sua execução e atualmente em estado de execução.[1]

**Pontos-chave:**

* É um **esquema temporário de alocação de memória** onde os membros de dados só são acessíveis se o método que os continha estar atualmente em execução.
* Ele **aloca ou desaloque a memória automaticamente assim que o método correspondente concluir sua execução.**
* A **alocação de memória do Stack é considerada mais segura em comparação com a alocação de memória de heap porque os dados armazenados só podem ser acessados por segmento proprietário.**
* A alocação e a desalocação de memória é mais rápida em comparação com a alocação de memória heap.
* A memória de pilha **tem menos espaço de armazenamento** em comparação com a memória Heap.

### Memória Heap

Alocação em Heap: A memória é alocada durante a execução de instruções escritas por programadores. Note que o monte de nomes não tem nada a ver com a estrutura de dados da pilha. É chamado de *heap* porque é uma pilha de espaço de memória disponível para programadores para alocar e deslocar. Toda vez que fazemos um objeto, ele sempre cria no espaço *Heap* e as informações de referência a esses objetos são sempre armazenadas na memória *Stack*. A alocação de memória heap não é tão segura quanto a alocação de memória stack foi porque os dados armazenados neste espaço são acessíveis ou visíveis para todos os segmentos. Se um programador não manuseia bem essa memória, um vazamento de memória pode acontecer no programa.[1]

**Pontos-chave:**

* Este esquema de alocação de memória é diferente da alocação de espaço stack, aqui nenhum recurso de des alocação automática é fornecido.
* O **tempo de processamento (tempo de acesso) desta memória é bastante lento em comparação com a memória Stack.**
* A memória heap também **não é segura como memória stack porque os dados armazenados na memória Heap são visíveis para todos os segmentos.**
* O tamanho da **memória Heap é bastante maior em comparação com a memória Stack.**

quando colocamos dados na heap, nós pedimos um certo espaço de memória. O sistema operacional encontra um espaço vazio em algum lugar na heap que seja grande o suficiente, marca este espaço como em uso, e nos retorna um ponteiro, que é o endereço deste local. Este processo é chamado de alocar na heap, e às vezes se abrevia esta frase como apenas "alocação". Colocar valores na pilha não é considerado uma alocação. Como o ponteiro tem um tamanho fixo e conhecido, podemos armazená-lo na pilha, mas quando queremos os dados, de fato, temos que seguir o ponteiro.

Imagine que você está sentado em um restaurante. Quando você entra, você diz o número de pessoas que estão com você, o atendente encontra uma mesa vazia que acomode todos e os leva para lá. Se alguém do seu grupo chegar mais tarde, poderá perguntar onde vocês estão para encontrá-los.

Acessar dados na heap é mais lento do que acessar dados na Stack, porque você precisa seguir um ponteiro para chegar lá. Processadores de hoje em dia são mais rápidos se não precisarem pular tanto de um lugar para outro na memória. Continuando com a analogia, considere um garçom no restaurante anotando os pedidos de várias mesas. É mais eficiente anotar todos os pedidos de uma única mesa antes de passar para a mesa seguinte. Anotar um pedido da mesa A, depois um da mesa B, depois outro da mesa A, e outro da mesa B novamente seria um processo bem mais lento. Da mesma forma, um processador pode cumprir melhor sua tarefa se trabalhar em dados que estão próximos uns dos outros (assim como estão na pilha) em vez de dados afastados entre si (como podem estar na heap). Alocar um espaço grande na heap também pode levar tempo.

![](/Imagens/HD07/HeapStackGIF.gif)

### Fundamentos do Ownership

Com base no que foi esclarecido sobre *heap* e *stack*, podemos descrever melhor o comportamento de posse. Em primeiro lugar, devemos ter em mente as seguintes "Leis de Ownership":

```
1ª Lei) Cada valor em Rust possui uma variável que é dita seu owner (sua dona).
2ª Lei) Pode apenas haver um owner por vez.
3ª Lei) Quando o owner sai fora de escopo, o valor será destruído.
```

Assim como as três leis da robotica, essas três leis determinam como a linguagem Rust se relaciona com suas variáveis.

Como um primeiro exemplo de ownership, vamos olhar para o escopo de algumas variáveis. Um escopo é a área dentro de um programa para a qual um item é válido. Digamos que nós temos uma variável como esta:

```
// nada na memória aqui

fn main() {
let algo = "algo";  // um dado na memória dentro do escopo da função main
}

// nada na memória aqui
```

A variável é válida do ponto em que é declarada até o fim do atual escopo. Outro exemplo abaixo:

```
fn main() {
{                      // s não é válida aqui, ainda não está declarada.
    let s = "texto";   // s é válida deste ponto em diante.
                        // faz alguma coisa com s.
}                      // agora este escopo terminou, e s não é mais válida.
}
```

Em outras palavras, existem dois pontos no tempo que são importantes aqui:

1) Quando s entra no escopo, ela é válida.
2) Permanece dessa maneira até que ela saia de escopo.

Neste ponto, a relação entre escopos e quando variáveis são válidas é similar a outras linguagens de programação. Agora vamos construir sobre este entendimento, apresentando o tipo String.

## As strings (Uma Introdução)

Para demonstrarmos as regras de Ownership, iremos olhar para como o Rust entende as ``strings``. TYeremos de olhar para esse tipo com um modelo diferenciado pois os tipos abordados na seção "Tipos de Dados" **são todos armazenados na memória Stack, e retirados dela quando seu escopo termina, mas queremos ver dados que são armazenados na memória Heap e explorar como o Rust faz para saber quando limpar esses dados.**

Já vimos strings literais, em que um valor de string é fixado pelo código do nosso programa. Strings literais são convenientes, mas nem sempre são adequadas para situações em que queremos usar texto. Um motivo é que elas são imutáveis. Outro é que nem todos os valores de string são conhecidos enquanto escrevemos nosso código: por exemplo, o que fazer se queremos obter uma entrada do usuário e armazená-la? Para essas situações, Rust tem um segundo tipo de strings, String. Este tipo é alocado na heap, e como tal, é capaz de armazenar uma quantidade de texto que é desconhecida em tempo de compilação.

Iremos utilizar o tipo ``String`` (para uma string previamente definida), o operador ``::`` (indica o caminho do *namespace*) e o ``from`` (Usado para fazer conversões de valor para valor enquanto consome o valor de entrada.). Mas não precisa se preocupar com essas informações neste momento, iremos explorar mais futuramente. 

```
fn main() {
let s = String::from("Algo escrito por aqui ...");
}
```

Este tipo de string pode ser alterada:

```
fn main() {
let mut s = String::from("Algo escrito por aqui ...");  // Cria String
s.push_str(" Que o vento levou!"); // push_str() adiciona um literal à String
println!("{}", s);                 // Isso vai exibir a junção das duas strings
}
```

A utilização na memória é responsável pela capacidade de alteração desses valores. Em outras palavras, no caso do tipo ``String``, sabemos o seu conteúdo em tempo de compilação, então o texto é injetado diretamente para dentro do executável final, o que faz strings literais serem rápidas e eficientes - Memória Stack. Essas propriedades provêm apenas devido a sua imutabilidade. Infelizmente, não podemos colocar um segmento de memória dentro do binário para cada texto cujo tamanho é desconhecido em tempo de compilação, e cujo tamanho pode mudar ao longo da execução do programa.

Com o tipo ``String``, para poder acomodar um trecho mutável e expansível de texto, precisamos alocar uma quantidade de memória na heap, que é desconhecida em tempo de compilação, para manter o seu conteúdo. Isto significa que:

- A memória deve ser solicitada ao sistema operacional em tempo de execução.

- Precisamos de uma forma de retornar esta memória ao sistema operacional quando tivermos finalizado nossa ``String``.

quando chamamos ``String::from``, sua implementação solicita a memória de que precisa. Isso é meio que universal em linguagens de programação. Em linguagens com um garbage collector (GC), o GC rastreia e limpa a memória que não está mais sendo usada, e nós, como programadores, não precisamos pensar sobre isso. Contudo, na linguagem Rust, não precisameos nos preocupar tanto com isso!! Parece algo divino, pois em outras linguagens como C/ C++ sempre temos de usar um ``allocate`` ou um ``free`` para a alocação de memória, mas no Rust basta fechamos as chaves (``{}``). 

Quando uma variável sai de escopo, o Rust chama para nós uma função especial. Essa função é chamada ``drop``, e é aí que o autor de ``String`` pode colocar o código que retorna a memória. Rust chama ``drop`` automaticamente ao fechar chaves (``{}``).

Pode parecer simples agora, mas o comportamento do código pode ser inesperado em situações mais complicadas, quando queremos que múltiplas variáveis usem os dados que alocamos na heap. Vamos explorar algumas dessas situações agora.

## A Transfererência de Dados (*Move*)

Veja o seguinte código abaixo:

```
fn main() {
    let x = 5;
    let y = x;

    println!("{}", x);
}
```

Ele compila normalmente. Agora iremos ver o seguinte código:

```
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;

    println!("{}", s1);
}
```

Apesar de ser muito semelhante ao código anterior, ele não compilará. Aparecerá a seguinte mensagem de erro:

```
error[E0382]: borrow of moved value: `s1`
 --> src/main.rs:5:20
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `String`, which does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 |
5 |     println!("{}", s1);
  |                    ^^ value borrowed here after move
  |
  = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)

For more information about this error, try `rustc --explain E0382`.
warning: `playground` (bin "playground") generated 1 warning
error: could not compile `playground` due to previous error; 1 warning emitted
```

Para entendermos esse erro, iremos ver como a ``String`` funciona. Uma ``String`` é feita de três partes, mostradas à esquerda: um ponteiro para a memória que guarda o conteúdo da string, um tamanho, e uma capacidade. Este grupo de dados é armazenado na **Stack**. No lado direito está a memória na **Heap** que guarda o conteúdo.

![](/Imagens/HD07/String.png)

Quando atribuímos ``s1`` a ``s2``, os dados da String são copiados, o que significa que estamos copiando o ponteiro, o tamanho e a capacidade que estão na pilha. Não estamos copiando os dados que estão na heap, aos quais o ponteiro se refere. Isso é feito para que não haja um custo computacional de memória grande. A Imagem abaixo representa esse fenômeno:

![](/Imagens/HD07/Memoria.png)

Anteriormente, foi estabelecido que, quando uma variável sai de escopo, o Rust automaticamente chama a função ``drop`` e limpa a memória da ``heap`` para esta variável. Vimos na figura acima que que dois ponteiros estão apontados para uma mesma memória na ``Heap``. Isso é uma uma fragilidade grande: quando s2 e s1 saem de escopo, os dois vão tentar liberar a mesma memória. Isso é conhecido como erro de double free (liberação dupla), e é um dos bugs de segurança de memória. 

Em vez de tentar copiar a memória alocada, o Rust considera que ``s1`` deixa de ser válida, e portanto, o Rust não precisa liberar nenhuma memória quando ``s1`` sai de escopo. Se você já ouviu os termos "cópia rasa" e "cópia profunda" (shallow copy e deep copy) enquanto trabalhava com outras linguagens, o conceito de copiar o ponteiro, tamanho e capacidade sem copiar os dados provavelmente parece uma cópia rasa. **Mas como o Rust também invalida a primeira variável, em vez de chamar isto de cópia rasa**, isto é conhecido como um *move*.

Isso é que verdadeiramente ocorre:

![](/Imagens/HD07/Move.png)

Isso retrata uma escolha que o Rust tomou: Rust nunca vai criar deep copies dos seus dados. Logo, para qualquer cópia automática que aconteça, pode-se assumir que ela não será custosa em termos de desempenho em tempo de execução.

## Clone

É utilizado para uma "cópia profunda" (deep copy). Se nós queremos fazer uma cópia profunda dos dados da String que estão na heap, e não apenas os dados que estão na pilha, podemos usar um método comum chamado ``clone``.

![](/Imagens/HD07/Deep%20Copy.png)

Veja um exemplo:

```
fn main() {
let s1 = String::from("texto");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
}
```

## Copy

É utilizado para uma "cópia rasa" (shallow copy).

![](/Imagens/HD07/Memoria.png)

Veja o seguinte código abaixo:

```
fn main() {
let x = 5;
let y = x;

println!("x = {}, y = {}", x, y);
}
```

Este código parece contradizer o que acabamos de aprender: não temos uma chamada ao método ``clone``, mas ``x`` ainda é válido e não foi movido para ``y``. O motivo para esse fenômeno é que tipos como números inteiros têm um tamanho conhecido em tempo de compilação e são armazenados inteiramente na *Stack*, e por isso, cópias desses valores são rápidas de se fazer. Isso significa que não há razão para impedir ``x`` de ser válido após criarmos a variável ``y``. Em outras palavras, não há diferença entre cópia rasa e profunda aqui, então chamar o método clone não faria nada diferente de uma cópia rasa, por isso podemos deixá-lo de lado.

O Rust tem uma anotação especial chamada de trait ``Copy``, que podemos colocar em tipos como números inteiros, que são armazenados na *Stack*. Se um tipo possui o trait ``Copy``, uma variável anterior vai continuar sendo utilizável depois de uma atribuição. O Rust não vai nos deixar anotar um tipo com o trait ``Copy`` se este tipo, ou qualquer uma de suas partes, tiver implementado o trait ``Drop``.

Mas então fica a pergunta: quais tipos são ``Copy``? Bom, os tipos mais simples são ``Copy``.

- Todos os tipos inteiros, como u32.
- O tipo booleano, bool, com valores true e false.
- O tipo caractere, char.
- Todos os tipos de ponto flutuante, como f64.
- Tuplas, mas apenas aquelas que contém tipos que também são Copy. (i32, i32) é Copy, mas (i32, String) não.

**Veja um exemplo de fixação**

O seguinte código abaixo mostra as regras para os tipos ``Copy``.

```
fn main() {
    let s = String::from("texto");  // s entra em escopo.

    toma_posse(s);                  // move o valor de s para dentro da função...
                                    // ... e ele não é mais válido aqui.

    let x = 5;                      // x entra em escopo.

    faz_uma_copia(x);               // x seria movido para dentro da função,
                                    // mas i32 é Copy, então está tudo bem em
                                    // usar x daqui para a frente.

} // Aqui, x sai de escopo, e depois s. Mas como o valor de s foi movido, nada
  // de especial acontece.

fn toma_posse(uma_string: String) { // uma_string entra em escopo.
    println!("{}", uma_string);
} // Aqui, uma_string sai de escopo, e o método `drop` é chamado. A memória que
  // guarda seus dados é liberada.

fn faz_uma_copia(um_inteiro: i32) { // um_inteiro entra em escopo.
    println!("{}", um_inteiro);
} // Aqui, um_inteiro sai de escopo. Nada de especial acontece.

```

O que fazer se queremos deixar uma função usar um valor sem tomar posse dele? É meio irritante saber que qualquer coisa que passemos a uma função também precisa ser passado de volta se quisermos usá-lo novamente, além de algum possível resultado proveniente do corpo da função que também queremos retornar.

Você verá isso no próximo Hands-On, onde falaremos de referências.

Para entender um pouco mais sobre o tema, veja o vídeo abaixo:

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/rDoqT-a6UFg/0.jpg)](https://www.youtube.com/watch?v=rDoqT-a6UFg)

[![Visualizing Memory Layout of Rusts Data](https://img.youtube.com/vi/VFIOSWy93H0/0.jpg)](https://www.youtube.com/watch?v=VFIOSWy93H0)

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️ [Clique Aqui](/HandsOn/HD08/README.md)

## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Stack vs Heap Memory Allocation - Geeks for Geeks. Disponível em: <https://www.geeksforgeeks.org/stack-vs-heap-memory-allocation/>. Acesso em 21/09/2022.

[2] - Ownership. The Rust Programming Language  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch04-01-what-is-ownership.html>. Acesso em 28/09/2022.


Imagem 01 - <https://blog.pantuza.com/artigos/heap-vs-stack>. Acesso em 21/09/2022.

Imagem 02 - <https://www.oowgnoj.dev/review/js-remember>. Acesso em 21/09/2022.

Demais imagens - <https://rust-br.github.io/rust-book-pt-br/ch04-01-what-is-ownership.html>. Acesso em 28/09/2022.