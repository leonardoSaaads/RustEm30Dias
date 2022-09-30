# **Conhecendo o Rust**

*Tomemos, por exemplo, o trabalho de “nível de sistema” que lida com detalhes de baixo nível de gerenciamento de memória, representação de dados e simultaneidade. Tradicionalmente, esse domínio da programação é visto como misterioso, acessível apenas a alguns poucos que dedicaram os anos necessários para aprender a evitar suas infames armadilhas. E mesmo aqueles que o praticam o fazem com cautela, para que seu código não fique aberto a explorações, falhas ou corrupção.*

*Rust quebra essas barreiras eliminando as armadilhas antigas e fornecendo um conjunto de ferramentas amigável e polido para ajudá-lo ao longo do caminho. Os programadores que precisam “mergulhar” no controle de nível inferior podem fazê-lo com o Rust, sem correr o risco habitual de falhas ou falhas de segurança e sem ter que aprender os detalhes de uma cadeia de ferramentas instável. Melhor ainda, a linguagem foi projetada para guiá-lo naturalmente em direção a um código confiável que seja eficiente em termos de velocidade e uso de memória.*

*Programadores que já estão trabalhando com código de baixo nível podem usar Rust para aumentar suas ambições. Por exemplo, introduzir paralelismo em Rust é uma operação de risco relativamente baixo: o compilador irá capturar os erros clássicos para você. E você pode lidar com otimizações mais agressivas em seu código com a confiança de que não introduzirá falhas ou vulnerabilidades acidentalmente.*

*Mas Rust não se limita à programação de sistemas de baixo nível. É expressivo e ergonômico o suficiente para tornar os aplicativos CLI, servidores web e muitos outros tipos de código bastante agradáveis ​​de escrever - você encontrará exemplos simples de ambos mais adiante neste [livro](https://doc.rust-lang.org/book/foreword.html). Trabalhar com Rust permite que você construa habilidades que são transferidas de um domínio para outro; você pode aprender Rust escrevendo um aplicativo da web e aplicar essas mesmas habilidades para direcionar seu Raspberry Pi.* [1]

— Nicholas Matsakis and Aaron Turon

## Uma Breve Introdução

Rust surgiu de um projeto pessoal iniciado em 2006 pelo funcionário da Mozilla, Graydon Hoare. A Mozilla começou a patrocinar o projeto em 2009 e anunciou oficialmente o projeto em 2010. Uma possível fonte do nome, segundo Hoare, é o fungo da ferrugem. Isso fez com que os programadores do Rust adotassem “Rustaceans” como seu apelido de escolha. Durante o mesmo ano, o trabalho mudou do compilador inicial escrito em OCaml para um compilador auto-hospedado baseado em LLVM (uma infraestrutura de compilador escrita em C++, desenvolvida para otimizar em tempos de compilação, ligação e execução de programas escritos em linguagens de programação variadas.) escrito em Rust. O novo compilador Rust, chamado rustc, compilou-se com sucesso em 2011. A primeira versão pré-alfa numerada do compilador, Rust 0.1, foi lançada em janeiro de 2012.

Desde o início, a Rust se concentrou em segurança e desempenho. Uma das principais maneiras de conseguir isso é enfatizando a simultaneidade segura. A computação concorrente permite que diferentes partes de um programa sejam executadas fora de ordem. Isso abre a possibilidade de computação paralela, onde várias tarefas podem ser realizadas simultaneamente, em vez de consecutivamente. Isso, por sua vez, pode melhorar significativamente o desempenho de um aplicativo. Como o Rust foi projetado em torno desse princípio, ele possui algumas vantagens significativas no domínio do desempenho.

O sistema de tipos do Rust mudou consideravelmente entre as versões 0.2, 0.3 e 0.4. A versão 0.2 introduziu classes pela primeira vez, e a versão 0.3 adicionou destructors e polymorphism através do uso de interfaces. No Rust 0.4, os traços foram adicionados como meio de fornecer herança; as interfaces foram unificadas com características e removidas como um recurso separado. As classes também foram removidas e substituídas por uma combinação de implementações e tipos estruturados. Junto com a tipagem estática convencional, antes da versão 0.4, o Rust também suportava a análise typestate por meio de contratos. Ele foi removido na versão 0.4, embora a mesma funcionalidade possa ser alcançada aproveitando o sistema de tipos do Rust.

Mais recentemente, em 8 de fevereiro de 2021, a formação da Rust Foundation foi anunciada por suas cinco empresas fundadoras (AWS, Huawei, Google, Microsoft e Mozilla). Em uma postagem de blog publicada em 6 de abril de 2021, o Google anunciou o suporte ao Rust no Android Open Source Project como uma alternativa ao C/C++.

Em termos de sintaxe, Rust é semelhante a C e C++, incorporando muitas das palavras-chave e comandos de ambas as linguagens. No entanto, não é um clone direto e possui alguns elementos não encontrados em C ou C++.

Rust não tem coleta de lixo automática, nem usa contagem automática de referência como o Swift. Em vez disso, ele foi projetado para proteger a memória usando princípios de propriedade e empréstimo, além de desencorajar comportamentos indefinidos. Por não usar coleta de lixo, isso torna o Rust uma opção ideal para integração com C.[2][3]

![](/Imagens/HD01/CrescimentoRust.png)

## Futuro do Rust

Ao construir Rust, a comunidade Rust desenvolveu os princípios para equilibrar prioridades de desenvolvimento às vezes concorrentes. Eles são:

- “Confiável: Se compila, funciona.”
- “Performace: o código idiomático é executado com eficiência.”
- “Apoio: a linguagem, as ferramentas e a comunidade estão aqui para ajudar.”
- “Produtivo: um pouco de esforço faz muito trabalho.”
- “Transparente: você pode prever e controlar detalhes de baixo nível.”
- “Versátil: você pode fazer qualquer coisa com Rust.”

Quando olhamos esssas qualidades, podemos fazer um comparativo com outras linguagens de programação.

**C++**

Comparado ao C/C++, o Rust é mais seguro. Rust protege tanto suas próprias abstrações quanto as abstrações feitas pelos desenvolvedores, enquanto o C/C++ não possui essas duas opções. Erros específicos em C/C++ podem levar a um comportamento arbitrário, enquanto o Rust ajuda você a se concentrar no que é realmente importante.

**Java/Javascript/Python**

Quando se trata de linguagens com garbage collector, verifica-se que essa linguagem de programação é significativamente mais lenta que o Rust, especialmente tendo em mente acompanhar o C em muitos domínios. Além disso, você precisa ter em mente tempos de inicialização mais rápidos e menor consumo de memória. Java/Javascript/Python usa Garbage Collection para gerenciamento de memória, o que diminui o desempenho (embora valha a pena notar que facilita a programação).[4]

Nesse sentido, quando olhamos a linguagem Rust, podemos perceber que ela possui um grande potencial para aplicações onde **Performace** e **Segurança** são pré-requisitos. 

![](/Imagens/HD01/Loved.png)

**Exemplos de Aplicações para Alta Performace & Segurança**

- Web browsers and services

1. Firefox

2. Amazon web services

3. Claudflares's

- Operating systems

1. Redox

2. Stratis

- Other notable projects and platforms

1. Discord 

2. Polkadot

3. TerminusDB

### ➡️ AVANÇAR PARA O PRÓXIMO HANDS-ON? ➡️ [Clique Aqui](/HandsOn/HD02/README.md)

## **REFERÊNCIAS BIBLIOGRÁFICAS**

[1] - The Rust Programming Language - Foreword. Disponível em: <https://doc.rust-lang.org/book/foreword.html>. Acesso em 04/09/2022.

[2] - The Rust Programming Language: Its History and Why It Matters - talentopia. Disponível em: <https://www.talentopia.com/news/the-rust-programming-language-its-history-and-why/>.Acesso em 07/09/2022.

[3] - Rust (programming language) - wikipedia. Disponível em: <https://en.wikipedia.org/wiki/Rust_(programming_language)>.Acesso em 07/09/2022.

[4] - 5 Reasons Why Rust Is The Future - D. KARASEK. Disponível em: <https://scalac.io/blog/5-reasons-why-rust-is-the-future-rust-functional-programming/>.Acesso em 07/09/2022.

__

Imagem 01 - https://www.nature.com/articles/d41586-020-03382-2

Imagem 02 - https://twitter.com/StackOverflow/status/1389882831384502273/photo/1