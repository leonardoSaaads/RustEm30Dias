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


## REFERÊNCIAS BIBLIOGRÁFICAS

[1] - Stack vs Heap Memory Allocation - Geeks for Geeks. Disponível em: <https://www.geeksforgeeks.org/stack-vs-heap-memory-allocation/>. Acesso em 21/09/2022.



Imagem 01 - <https://blog.pantuza.com/artigos/heap-vs-stack>. Acesso em 21/09/2022.

Imagem 02 - <https://www.oowgnoj.dev/review/js-remember>. Acesso em 21/09/2022.