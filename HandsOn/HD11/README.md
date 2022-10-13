# **Structs - Parte 02**

Methods (métodos) são semelhantes às funções: são declarados com a chave ``fn`` e o seu nome, eles podem ter parâmetros e valor de retorno, e eles contêm algum código que é executado quando eles são chamados de algum outro lugar. No entanto, métodos são diferentes das funções, porque são definidos no contexto de uma struct (ou um objeto ``enum`` ou uma ``trait``), o seu primeiro parâmetro é sempre ``self``, que representa a instância da struct do método que está a ser chamado.

Neste primeiro exemplo, iremos fazer um método que retorna a área de um triangulo dado 3 pontos. |Como sabemos, a área de um triângulo é dado por:

$$ \displaystyle \begin{align*} Area &= \frac{1}{2} \begin{Vmatrix} x_1 & y_1 & 1 \\ x_2 & y_2 & 1 \\  x_3 & y_3 & 1 \end{Vmatrix} \end{align*} $$

No caso deste exemplo, iremos utilizar os pontos (1.0, 8.0), (9.0, 5.0), (8.4, 43.2).

![](/Imagens/HD11/triangulo.png)

```
// Define a formatação de ponto - eixo X e Y.
struct Point(f64, f64);

// Define a formatação de um trinângulo  - recebe três pontos no planu XY.
struct Triangle {
    p1: Point,
    p2: Point,
    p3: Point,
}

// Implementa uma função que Calcula a área de um triângulo.
impl Triangle {
    fn area(&self) -> f64 {
        (self.p1.0 * (self.p2.1 - self.p3.1) + 
        self.p2.0 * (self.p3.1 - self.p1.1) +
        self.p3.0 * (self.p1.1 - self.p2.1)).abs() / 2.0
    }
}

// Define três pontos e imprime a área do triângulo.
fn main() {
    let a = Point(1.0, 8.0);
    let b = Point(9.0, 5.0);
    let c = Point(8.4, 43.2);

    let triangle = Triangle{
        p1: a,
        p2: b,
        p3: c
        };
    
    println!("A área do triângulo é: {}", triangle.area());
}
```

Para definir a função dentro do contexto de ``Triangle``, vamos iniciar um bloco ``impl`` (Implementação). Depois movemos a função area dentro do corpo ({}) do ``impl`` e alteramos o primeiro (e neste caso, unico) parâmetro a ser self na assinatura e em todos os lugares dentro do corpo.  Em ``main``, onde chamamos a função ``area`` e passamos ``triangle`` como um argumento, podemos usar a sintaxe de método (method sintax) para chamar o método área na nossa instância ``Rectangle``. A sintaxe de método vem em seguida a uma instância: adicionamos um ponto seguido pelo nome do método, parênteses e os argumentos.

Na assinatura de ``area``, usamos ``&self`` em vez de ``triangule: &Triangule`` porque Rust sabe que o tipo de ``self`` é ``Triangle`` devido a este método estar dentro do contexto do ``impl Triangle``. Escolhemos ``&selft`` aqui pela mesma razão usamos ``&Triangle`` na versão função: nós não queremos tomar posse, nós apenas queremos ler os dados da struct, e não escrever nela. Ter um método que toma posse da instância, usando apenas ``self`` como primeiro parâmetro é raro; esta técnica é geralmente utilizada quando o método transforma o ``self`` em algo mais e queremos evitar que o chamador use a instância original após a transformação.

A principal vantagem do uso de métodos em vez de funções, além de usar a sintaxe do método e não ter de repetir o tipo de ``self`` em cada assinatura do método, é a organização. Nós colocamos todas as coisas que nós podemos fazer com uma instância de um tipo em um bloco ``impl`` em vez de fazer os futuros utilizadores do nosso código procurar as capacidades de ``Triangle`` em vários lugares na biblioteca que fornecemos.

___
### CURIOSIDADE: E o operador ``->``?

Em linguagens como C++, dois operadores diferentes são usados para chamar métodos: você usa ``.`` se você está chamando um método do objeto diretamente e ``->`` se você está chamando o método em um ponteiro para o objeto e necessita desreferenciar o ponteiro primeiro. Em outras palavras, se objeto é um ponteiro, objeto->algo() é semelhante a ``(*objeto).algo()``.

Rust não tem um equivalente para o operador ``->``; Em vez disso, Rust tem um recurso chamado referenciamento e desreferenciamento automático. Chamada de métodos é um dos poucos lugares em Rust que têm este comportamento. Veja como funciona: quando você chama um método com ``object.something()``, o Rust adiciona automaticamente ``&``, ``&mut`` ou ``*`` para que o objeto corresponda à assinatura do método.
___

## Métodos com mais parâmetros

Vamos praticar usando métodos através da aplicação de um segundo método sobre o struct ``Rectangle``. Vamos criar uma função area que tem uma instância de ``Rectangle`` como um parâmetro e, em vez disso, fazer um método area definido na struct ``Rectangle`` como mostrado:

```
// Recebe altura e largura do retângulo.
struct Rectangle {
    length: u32,
    width: u32,
}
// Método para calcular área
impl Rectangle {
    fn area(&self) -> u32 {
        self.length * self.width
    }
}
// Cria um retangulo de altura e largura iguais a 50 e 30, respectivamente, e mostra a área.
fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

Desta vez, queremos uma instância de ``Rectangle`` para ter outra instância de ``Rectangle`` e retornar verdadeiro se o segundo ``Rectangle`` pode caber completamente dentro de ``self``, caso contrário deve retornar falso.

```
fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };
    let rect2 = Rectangle { length: 40, width: 10 };
    let rect3 = Rectangle { length: 45, width: 60 };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```

E o resultado esperado seria o seguinte, porque ambas as dimensões de ``rect2`` são menores do que as dimensões de ``rect1``, mas rect3 é mais amplo do que ``rect1``:

Can rect1 hold rect2? true   // resultado esperado
Can rect1 hold rect3? false  // resultado esperado

Sabemos que queremos definir um método, por isso vai ser dentro do bloco ``impl Rectangle``. O nome do método será ``can_hold``, e vai tomar um empréstimo imutável de um outro Rectangle como parâmetro. Podemos dizer qual o tipo do parâmetro, olhando o código que chama o método: rect1.can_hold(&rect2) passa &rect2, que é um empréstimo imutável de rect2, uma instância do Rectangle. Isso faz sentido porque nós só precisamos de ler rect2 (em vez de escrever, que precisaria de um empréstimo mutável),e nós queremos que main conserve a propriedade de rect2 para que possamos utilizá-lo novamente depois de chamar o método can_hold. O valor de retorno de can_hold será um booleano, e a aplicação irá verificar se o comprimento e a largura de self são ambos maiores que o comprimento e a largura do outro Rectangle, respectivamente.

Deste modo, temos a seguinte implementação:

```
impl Rectangle {
    fn area(&self) -> u32 {
        self.length * self.width
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.length > other.length && self.width > other.width
    }
}
}
```

## Funções Associadas.

Outro recurso útil dos blocos ``impl`` é que podemos definir funções dentro dos blocos ``impl`` que não recebem ``self`` como um parâmetro. Estas são chamadas de funções associadas porque elas estão associados com a struct. Elas ainda são funções, não métodos, porque elas não têm uma instância da struct para trabalhar. Você já usou a função associada ``String::from``.


## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - WolframAlpha. Disponível em: <https://www.wolframalpha.com/input?i=area+of+%281.0%2C+8.0%29%2C+%289.0%2C+5.0%29%2C+%288.4%2C+43.2%29>. Acesso em 13 de outubro de 2022.