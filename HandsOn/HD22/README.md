# **Tratamento de Erros 01**

A maioria dos idiomas não distingue entre esses dois tipos de erros e manipula ambos da mesma forma, utilizando mecanismos como exceções. Ferrugem não tem Exceções. Em vez disso, ele tem o tipo ``Result<T, E>`` para erros recuperáveis e a macro ``panic!`` que interrompe a execução quando o programa encontra um erro irrecuperável. Este capítulo aborda o chamado ``panic!`` primeiro e depois ``Result<T, E>`` para devolução de valores.

## **DEU ``Panic!`` NO SISTEMA!!!**

Na maioria das vezes, erros irão acontecer naturalmente a medida que seus projetos ficam grandes. Não á como escapar dessa dura realidade. Apesar de Rust ser uma linguagem performática e muito segura, algumas lógicas entram em conflito e, desses conflitos, emergem erros! 

Mas, interessantemente, Rust possui o macro ``panic!`` ( As macros possuem exclamação no final - ex: ``panic!``, ``print!``, etc). Na prática, existem duas maneiras de causar um pânico: executando uma ação que faz com que nosso código entre em pânico (como acessar um array após o final) ou chamando explicitamente o método ``panic!`` macro. Em ambos os casos, causamos pânico em nosso programa. Por padrão, esses panics imprimirão uma mensagem de falha, desenrolarão, limparão a pilha e sairão.

___

### Desenrolando a Stack ou Abortando em Resposta a um Pânico

Por padrão, quando ocorre um pânico, o programa começa a desenrolar, o que significa que o Rust volta para a Stack e limpa os dados de cada função que encontra. No entanto, essa caminhada de volta e limpeza é muito trabalhosa. Rust, portanto, permite escolher a alternativa de abortar imediatamente, o que encerra o programa sem limpeza.

A memória que o programa estava usando precisará ser limpa pelo sistema operacional. Se em seu projeto você precisar tornar o binário resultante o menor possível, você pode mudar de unwinding para abortar em um pânico adicionando panic = 'abort' às seções [profile] apropriadas em seu arquivo Cargo.toml. Por exemplo, se você deseja abortar em pânico no modo de liberação, adicione isto:

```
[profile.release]
panic = 'abort'
```
___


Vamos testar um código simples para irmos nos acostumando aos poucos com esse atributo. Veja o exemplo abaixo:

```
fn main() {
    panic!("crash and burn");
}
```

Você verá que a saída será a seguinte:

```
Finished dev [unoptimized + debuginfo] target(s) in 0.59s
Running `target/debug/playground`
thread 'main' panicked at 'crash and burn', src/main.rs:2:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

A chamada para o ``pânic!`` causa a mensagem de erro contida nas duas últimas linhas. A primeira linha mostra nossa mensagem de pânico e o local em nosso código-fonte onde ocorreu o pânico: ``src/main.rs:2:5`` indica que é a segunda linha, quinto caractere do nosso arquivo ``src/main.rs``. Em outros casos, o ``pânic!``pode estar no código que nosso código chama, e o nome do arquivo e o número da linha relatados pela mensagem de erro serão o código de outra pessoa onde o ``panic!`` macro é chamada, não a linha do nosso código que eventualmente levou ao ``pânic!`` acionar.

Vejamos outro exemplo para ver como é quando um ``pânic!`` chamada vem de uma biblioteca devido a um bug em nosso código, em vez de nosso código chamando a macro diretamente.

### Usando um ``pânic!``  p/ rastreamento

Vejamos o exemplo de um código:

```
fn main() {
    let vetor = vec![1, 2, 3, 40, 78];
    vetor[69]; // Não tem no vetor
}
```

Aqui, estamos tentando acessar o 70º elemento do nosso vetor (que está no índice 69 porque a indexação começa em zero), mas o vetor tem apenas 5 elementos. Nesta situação, Rust entrará em pânic!. Usar [] deve retornar um elemento, mas se você passar um índice inválido, não há nenhum elemento que o Rust possa retornar aqui que esteja correto.

Em C, tentar ler além do final de uma estrutura de dados é um comportamento indefinido. Você pode obter o que quer que esteja no local da memória que corresponda a esse elemento na estrutura de dados, mesmo que a memória não pertença a essa estrutura. Isso é chamado de *buffer overread* e pode levar a vulnerabilidades de segurança se um invasor for capaz de manipular o índice de forma a ler dados que não deveriam ser permitidos e que são armazenados após a estrutura de dados.

Para proteger seu programa desse tipo de vulnerabilidade, se você tentar ler um elemento em um índice que não existe, o Rust interromperá a execução e se recusará a continuar.

