# **Structs - Parte 02**

Methods (métodos) são semelhantes às funções: são declarados com a chave ``fn`` e o seu nome, eles podem ter parâmetros e valor de retorno, e eles contêm algum código que é executado quando eles são chamados de algum outro lugar. No entanto, métodos são diferentes das funções, porque são definidos no contexto de uma struct (ou um objeto ``enum`` ou uma ``trait``), o seu primeiro parâmetro é sempre ``self``, que representa a instância da struct do método que está a ser chamado.

Neste primeiro exemplo, iremos fazer um método que retorna a área de um triangulo dado 3 pontos. |Como sabemos, a área de um triângulo é dado por:

$$
\displaystyle \begin{align*} Area &= \frac{1}{2} \begin{Vmatrix} x_1 & y_1 & 1 \\ x_2 & y_2 & 1 \\  x_3 & y_3 & 1 \end{Vmatrix} \end{align*}
$$

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

Na assinatura de ``area``, usamos ``&self`` em vez de ``triangule: &Triangule`` porque Rust sabe que o tipo de ``self`` é ``Triangle`` devido a este método estar dentro do contexto do ``impl Triangle``. Escolhemos ``&selft`` aqui pela mesma razão usamos ``&Triangle`` na versão função: nós não queremos tomar posse, nós apenas queremos ler os dados da struct, e não escrever nela.

## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - WolframAlpha. Disponível em: <https://www.wolframalpha.com/input?i=area+of+%281.0%2C+8.0%29%2C+%289.0%2C+5.0%29%2C+%288.4%2C+43.2%29>. Acesso em 13 de outubro de 2022.