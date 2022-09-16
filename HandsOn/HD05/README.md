# **Comentários e Controle de Fluxo**

## **Comentários**

Comentário de código é a prática de espalhar notas curtas, normalmente de uma única linha em todo o seu código. Essas notas são chamadas de comentários. Eles explicam como seu programa funciona e suas intenções por trás dele.

Os comentários não têm nenhum efeito em seu programa, mas são inestimáveis ​​para as pessoas que lêem seu código. Em Rust, os comentários devem começar com duas barras e continuar até o final da linha. Para comentários que se estendem além de uma única linha, você precisará incluir ``//`` em cada linha. Aqui está um exemplo de comentário de código em ação [1]:

```
// Esse é um comentário em Rust
```

Mas você verá com mais frequência essas palavras nesse formato, com o comentário em uma linha separada acima do código que está anotando:

```
fn main() {
    // Estou com sorte hoje.
    let numero_da_sorte = 7;
}
```

## **Controle de Fluxo**

Decidir se deve ou não executar algum código, dependendo se uma condição é verdadeira e decidir executar algum código repetidamente enquanto uma condição é verdadeira, são blocos de construção básicos na maioria das linguagens de programação. As construções mais comuns que permitem controlar o fluxo de execução do código Rust são as expressões ``if``, ``else``, ``loop``, ``for``, ``while`` e  ``if let``/``while let``.

Antes de entrarmos no controle de fluxo propriamente dito, é imporante fazer a introdução de alguns argumentos lógicos. Assim como no C++, Rust possui um padrão identico para operadores de condições lógicas.

<table>
<thead>
  <tr>
    <th>Operador Lógico</th>
    <th>AND</th>
    <th>OR</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Representação</td>
    <td>&amp;&amp;</td>
    <td>||</td>
  </tr>
</tbody>
</table>

Agora podemos iniciar o módulo condicional ou de controle de fluxo.

### Expressões if/else.


Similarmente a outras linguaguens de programação, existem marcadores condicionais que fazem a validação de uma determinada condição. Em Rust, a expressão ``if`` representa algo como "Se esta condição for cumprida, execute este bloco de código. Se a condição não for atendida, não execute este bloco de código."

Veja um exemplo abaixo:

```
fn main() {
    let numero = 7;

    if numero < 10 {
        println!("condição era verdadeira");
    } else {
        println!("condição era falsa");
    }
}
```

Neste código, é definido um número igual a ``7``. Depois disso, é feito um marcador condicional ``if``, que irá validar se o número criado é menor que ``10``. Se o número for menor que 10, será printado ``condição era verdadeira``. Caso contrário, será printado ``condição era falsa``. Todas as expressões if começam com a palavra-chave if, que é seguida por uma condição. 

Opcionalmente, também podemos incluir uma expressão ``else``, que escolhemos fazer aqui, para dar ao programa um bloco de código alternativo a ser executado, caso a condição seja avaliada como falsa.

Se executarmos o programa, teremos a seguinte saída:

```
   Compiling playground v0.0.1 (/playground)
    Finished dev [unoptimized + debuginfo] target(s) in 0.55s
     Running `target/debug/playground`

condição era verdadeira
```

Agora, se mudarmos o ``numero`` para o valor de ``11``, veremos que temos a seguinte saída:

```
   Compiling playground v0.0.1 (/playground)
    Finished dev [unoptimized + debuginfo] target(s) in 0.73s
     Running `target/debug/playground`

condição era falsa
```
Veja que essa saída era esperada, pois agora o número adotado é maior que 10; Outro exemplo de código simples, porém um pouco mais complexo que o anterior, é esse abaixo:

```
fn main() {
    let numero = 7;
    
    if condicao(numero) ==  true {
        println!("Deu Certo!!")
    }
    else{
        println!("Ops! Algo de errado!!")
    }
}

fn condicao(x: usize) -> bool {
    if x < 10 {
        return true;
    }
    else {
        return false;
    }
}
```

Neste Código, criamos uma variável ``numero`` com valor igual a 7, também é criada uma função chamada ``condicao`` - que retorna ``true`` se o valor for menor que 10 e, caso contrário, retorna ``false``. Como 7 é menor que 10, é natural percebemos que quando executamos ``condicao(numero)`` será retornado o valor ``true``. Com isso, podemos ativar a outra condição que printa como resultado ``Deu Certo!!``.

Ao contrário de linguagens como Ruby e JavaScript, o Rust não tentará automaticamente converter tipos não-booleanos em um booleano. Você deve explicitar e sempre fornecer ``if`` com um booleano como sua condição.

Veja o exemplo abaixo, ele não compilará:

```
fn main() {
    let numero = 3;

    if numero {
        println!("número era 3");
    }
}
```

Se quisermos que o bloco de código if seja executado somente quando um número não é igual a ``0``, por exemplo, podemos mudar o ``if`` para o seguinte:


```
fn main() {
    let numero = 3;

    if numero != 0 {
        println!("número era algo diferente de zero");
    }
}
```

### Usando if let

Pelo fato de ``if`` ser uma expressão, podemos usá-la do lado direito de uma declaração ``let``.

Veja o exemplo abaixo:

```
fn main() {
    let condicao = true;
    let numero = if condicao {
        5
    } else {
        6
    };

    println!("O valor do número é: {}", numero);
}
```

Quando executamos temos a seguinte saída:

```
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/branches`
O valor do número é: 5
```


## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Guide to Code Commenting.Guide to Code Commenting - CodeConquest.<https://www.codeconquest.com/advanced-programming-concepts/code-commenting/>.  Acesso em 15/09/2022.

[2] - Comments. The Rust By Exemples  - doc.rust-lang.org. Disponível em: <https://rust-br.github.io/rust-book-pt-br/ch03-04-comments.html>. Acesso em 15/09/2022.

[3] - Control Flow. The Rust By Exemples  - doc.rust-lang.org. Disponível em: <https://doc.rust-lang.org/rust-by-example/fn.html>. Acesso em 15/09/2022.