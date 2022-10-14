# **Enums**

Neste capítulo vamos ver enumerações, também chamadas de **enums**. Enums permitem definir um tipo por meio da enumeração de seus possíveis valores. Primeiro, vamos definir e usar uma enum para mostrar como ela pode atrelar significado aos nossos dados. Depois, vamos explorar uma enum particularmente útil, chamada Option, que expressa um valor que tanto pode ser algo quanto pode não ser nada.

Vamos ver um caso em que enums podem ser mais apropriadas do que structs e descobrir como elas podem ser úteis. Digamos que estamos trabalhando com endereços IP. Atualmente, existem duas versões do protocolo IP que são mais utilizadas: a quatro e a seis. Estas são as únicas possibilidades para um endereço IP com que o nosso programa vai trabalhar: nós podemos enumerar todos os possíveis valores, é daí que vem o nome enumeração.

Um endereço IP qualquer pode ser ou da versão quatro ou da versão seis, mas nunca das duas ao mesmo tempo. Esta propriedade dos endereços IP faz com que a enum seja bem apropriada para este caso, pois enums só podem assumir o valor de uma de suas variantes. Os endereços de ambas as versões, seja quatro ou seis, ainda são, fundamentalmente, endereços IP, e deveriam ser tratados pelo mesmo tipo no código em situações que se aplicam a qualquer versão de endereço IP.

```
fn main() {
enum VersaoIp {
    V4,
    V6,
}
}
```

``V4`` e ``V6`` são as chamadas variantes da enum. Podemos criar instâncias de cada uma das duas variantes de VersaoIp, da seguinte forma:

```
fn main() {
enum VersaoIp {
    V4,
    V6,
}

let quatro = VersaoIp::V4;
let seis = VersaoIp::V6;
}
```

Repare que as variantes pertencem ao namespace da enum, e se usa ``::`` para separar os dois. Isso é útil porque agora ambos os valores ``VersaoIp::V4`` e ``VersaoIp::V6`` são do mesmo tipo: VersaoIp. O uso de enums tem ainda mais vantagens. Pensando mais a fundo sobre o nosso tipo de endereço IP, ainda não temos uma forma de representar o endereço em si, apenas sabemos qual a versão dele.

Podemos utilizar um modelo se struct para lidarmos com as diferentes versões de IP. Veja o exemplo abaixo:

```
fn main() {
enum VersaoIp {
    V4,
    V6,
}

struct EnderecoIp {
    versao: VersaoIp,
    endereco: String,
}

let local = EnderecoIp {
    versao: VersaoIp::V4,
    endereco: String::from("127.0.0.1"),
};

let loopback = EnderecoIp {
    versao: VersaoIp::V6,
    endereco: String::from("::1"),
};
}
```

Aqui nós definimos uma struct ``EnderecoIp`` que tem dois membros: ``versao``, do tipo ``VersaoIp`` (que definimos anteriormente) e endereco, do tipo String. Temos duas instâncias dessa struct. A primeira, local, tem o valor ``VersaoIp::V4`` como sua versao, e um endereço associado igual a ``127.0.0.1``. A segunda instância, loopback, tem como sua versao a outra variante de ``VersaoIp``, V6, e o endereço ``::1`` associado a ela. Nós usamos uma struct para encapsular os valores de versao e endereco, agora a variante está associada ao valor.
