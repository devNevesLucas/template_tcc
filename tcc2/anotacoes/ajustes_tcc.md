## Ajustes do tcc

### Ajustes nos objetivos

Objetivos mudam pois podem ser interpretados como lista de tarefas, com dependência entre si. Novos objetivos são três, ao invés dos quatro anteriores

- Objetivo um: Descrever como o HOFG e os SIs se integram, bem como descrever mudanças no algoritmo do HOFG, necessárias para que a integração ocorra.
- Objetivo dois: Implementação do algoritmo HOFG SI, realizando a leitura/análise de programas que possuem um único arquivo. -- O desafio esta na implementação do algoritmo e leitura na linguagem zig, versão 0.16.0. Pode ser testado com a utilização do Juliet Test Suite.
- Objetivo três: Implementação do HOFG SI para análise de projetos com mais de um arquivo ou módulo. -- Desafio final do projeto, compilação se torna complexa. Análise e testabilidade pode ser realizada com projetos de código aberto, com milhares de linhas de código e múltiplos módulos e arquivos. Torna possível a análise de projetos com mais de quinhentas mil linhas de código, sendo uma limitação do HOFG original.

### Ajuste do título

Problemas do título atual:
- Muito aberto: "vulnerabilidades de memória em C"
- Ainda utiliza VSA, sendo um algoritmo que foi descartado

Ideia de título ajustado:

"Detecção de vulnerabilidades de memória em programas escritos em linguagem C: Integração de SIs ao HOFG"