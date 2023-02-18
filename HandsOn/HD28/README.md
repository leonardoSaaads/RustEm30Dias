# **Traits: Definindo Comportamento de Estruturas**

Uma característica define a funcionalidade que um determinado tipo possui e pode compartilhar com outros tipos. Podemos usar traços para definir o comportamento compartilhado de forma abstrata. Podemos usar limites de característica para especificar que um tipo genérico pode ser qualquer tipo que tenha determinado comportamento.

## Definindo uma trait;

O comportamento de um tipo consiste nos métodos que podemos chamar nesse tipo. Tipos diferentes compartilham o mesmo comportamento se pudermos chamar os mesmos métodos em todos esses tipos. As definições de características são uma maneira de agrupar assinaturas de métodos para definir um conjunto de comportamentos necessários para atingir algum objetivo.

Por exemplo, digamos que temos vários structs que contêm vários tipos e quantidades de texto: um struct NewsArticle que contém uma notícia arquivada em um determinado local e um Tweet que pode ter no máximo 280 caracteres junto com metadados que indicam se foi um novo tweet, um retweet ou uma resposta a outro tweet.

Queremos criar uma caixa de biblioteca de agregador de mídia chamada agregador que pode exibir resumos de dados que podem ser armazenados em uma instância de NewsArticle ou Tweet. Para fazer isso, precisamos de um resumo de cada tipo e solicitaremos esse resumo chamando um método de resumo em uma instância.
