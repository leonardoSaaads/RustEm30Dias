# **Matchs**

O Rust tem um excelente operador de controle de fluxo chamado ``match``, que nos permite comparar um valor com uma série de padrões e executar um código com base no padrão que casar. Padrões podem ser compostos de valores literais, variáveis, caracteres-curinga e várias outras coisas. A grande utilidade do ``match`` vem da expressividade dos padrões e das análises feitas pelo compilador, tendo certeza de que todos os casos possíveis estão sendo tratados.

Imagine que expressão ``match`` funciona como uma máquina de contar moedas: as moedas passam por um canal que possui furos de vários tamanhos, e cada moeda cai no primeiro furo em que ela couber. Da mesma forma, os valores passam por cada padrão de um ``match``, e logo no primeiro padrão que o valor "se encaixar", o bloco de código que estiver associado a ele será executado.

