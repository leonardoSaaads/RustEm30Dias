# **Iterators: Dando um *Boost* em seu código**

Iteradores são uma ferramenta utilizada em programação para percorrer sequências de dados de forma ordenada, executando uma tarefa específica em cada item dessa sequência. Em Rust, os iteradores são preguiçosos, o que significa que eles não são executados imediatamente, mas somente quando você chama um método que consome o iterador.

Por exemplo, como vimos anteriormente, é criado um iterador sobre os itens de um vetor usando o método ``iter()`` do tipo ``Vec<T>``. No entanto, esse código por si só não faz nada útil, pois apenas cria o iterador, mas não o utiliza de fato.

Para utilizar o iterador e executar uma tarefa em cada item da sequência, é necessário chamar um método que consuma o iterador, como ``map()``, ``filter()``, ``fold()``, entre outros. Esses métodos são responsáveis por aplicar a lógica de iteração em cada item do iterador e, quando necessário, determinar quando a sequência chegou ao fim.

Ao utilizar os métodos dos iteradores em Rust, você não precisa se preocupar em implementar a lógica de iteração manualmente, pois isso é feito pelos próprios métodos dos iteradores. Isso torna o código mais simples e legível, além de evitar erros comuns que podem ocorrer ao implementar a lógica de iteração manualmente.

Em Rust, os iteradores são preguiçosos, o que significa que eles não têm efeito até que você ligue métodos que consomem o iterador para usá-lo. Por exemplo, o código abaixo:

```
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();
```
