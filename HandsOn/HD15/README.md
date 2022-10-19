# **Módulos - parte 1**

## Para que serve Módulos?

Os programas que escrevemos até agora foram em um módulo em um arquivo. À medida que um projeto cresce, você deve organizar o código dividindo-o em vários módulos e, em seguida, vários arquivos. Um pacote pode conter várias caixas e, opcionalmente, uma caixa biblioteca. À medida que um pacote cresce, você pode extrair peças em caixas separadas que se tornam dependências externas. Este capítulo abrange todas essas técnicas. Para projetos muito grandes que compõem um conjunto de pacotes interrelacionados que evoluem juntos, o ``Cargo`` fornece espaços de trabalho. Vamos definir alguns conceitos: 

- **Pacotes**: Um recurso que o Cargo permite construir, testar e compartilhar caixas.
- **Caixas**: Uma árvore de módulos que produz uma biblioteca ou executável.
- **Módulos**: Deixe você controlar a organização, o escopo e a privacidade dos caminhos.
- **Caminhos**: Uma maneira de nomear um item, como uma estrutura, função ou módulo.

Da mesma forma que você extrai uma lógica das linhas de código em uma função, você pode extrair funções (e outros códigos, como structs e enums) em diferentes módulos. **Um módulo é um *namespace* que contém definições de funções ou tipos, e você pode escolher se essas definições são visíveis fora de seu módulo (público) ou não (privado)**.

- A palavra-chave ``mod`` declara um novo módulo.
- Por padrão, as funções, tipos, constantes e módulos são privados. A palavra-chave ``pub``    torna um item público e, portanto, visível fora do seu *namespace*.
- A palavra-chave ``use`` traz módulos, ou as definições dentro dos módulos, ao escopo.

___