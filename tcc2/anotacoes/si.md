## Strided Interval - SI
### Intervalo com passo

Representa um conjunto de números inteiros, no formato $s[lb, ub]$, no qual:
- $s$ é o passo (stride)
- $lb$ é o menor número ou valor
- $ub$ é o maior número, posição final do conjunto

$s$ é um valor sem sinal, maior ou igual a zero

Já o conjunto $[lb, ub]$ possui sinal, contendo possíveis valores $-2^k <= lb <= ub <= 2^k - 1$, onde $k$ é a quantidade de bits

No contexto de memória alocada:
- $s$ é o deslocamento dentro do conjunto
- $[lb, ub]$ é o intervalo de memória que uma variável possui

Exemplos de SIs:
- $4[0,12]$ -> ${0, 4, 8}$
- $3[12,21]$ -> ${12, 15, 18}$

Um SI é uma sobre-aproximação, permite uma aproximação segura de um conjunto de valores. Significa que não garante que todos os valores pertencentes ao conjunto realmente façam parte dele

Os Strided Intervals possuem aritmética própria, tais funções aritméticas devem ser adicionadas ao HOFG para que os limites e ponteiros de cada endereço possam ser rastreados de forma efetiva