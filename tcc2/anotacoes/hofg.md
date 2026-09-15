## HOFG - Heap Object Flow Graph

Focado em detectar erros de memória que acontecem pelo uso impróprio dos privilégios de alocação de memória em C e C++.

Utiliza apenas os ponteiros nos quais o fluxo da memória dinâmica tem contato, ignorando demais variáveis e constantes que não geram vazamentos de memória. Tais ponteiros são utilizados para geração de representação intermediária, a partir disso, o contexto visualizado é diminuído, fazendo com que a análise seja focada na memória dinâmica.

Mapeia fluxo de cada objeto alocado dinamicamente, do seu ponto inicial até sua desalocação.

O programa é convertido para a forma SSA, fato que auxilia a diferenciar as atribuições e usos de cada variável/ponteiro.

Para cada nó do grafo HOFG, são adicionadas informações sobre o ponteiro, tais informações são chamadas de "embedding".

A geração do HOFG se divide em três camadas:
- Geração do HOFG para o programa inteiro.
- Remoção dos percursos sem vazamento.
- Detecção e avaliação de caminhos com vazamentos encontrados.

Os nós se dividem em três tipos:
- Nós de objeto.
- Nós de ponteiro.
- Nós de liberação.

As arestas possuem informações sobre as condicionais necessárias para que aquele fluxo/percursos seja tomado. Cada aresta possui o ponteiro para o vertice cabeça, bem como uma lista dos ponteiros para estados condicionais que se tornam restrições para a aresta.

O grafo é montado de forma que toda e qualquer informação necessária sobre os ponteiros já esteja adicionada no próprio grafo.

Após a montagem do grafo, caminhos que não possuem vazamentos são liberados. Isso reduz o tamanho do grafo, otimizando os próximos passos da análise. A remoção é feita obtendo um percurso da criação/adição de um objeto até um vértice de liberação, num percurso no qual as arestas não possuem anotações de condicionais.

Se o grafo HOFG resultante da poda não possuir nenhum vértice e aresta, é verificado que o programa não possui vazamentos de memória.

----
#### Alteração

Adicionar heurística neste trecho:
- Ao remover itens durante a poda, apenas caminhos sem condicionais são removidos, com isso, arestas condicionais ficam sem vértice de origem. Para solucionar caso: Um vértice só deve ser apagado se todos os seus vértices filhos, com condicionais ou não, forem de fato liberados. E, além disso, deve possuir entre seus filhos um nó de liberação.
----

### Visão geral

O HOFG é implementado utilizando o framework LLVM, utilizado para compilar os arquivos do projeto de forma individual, obtendo a versão bitcode dos arquivos, sendo então combinados utilizando o LLVM Gold Linker, que gera um único arquivo de código para o programa inteiro no formato .bc.

O algoritmo segue os seguintes passos:
- Geração do HOFG para cada função
- Para uma chamada de função, é utilizado o sumário de tal função
- O sumário é aplicado caso esteja disponível, caso contrário, é adicionada uma pendência nesta etapa
- A partir disso, sumários de funções são atualizados quando uma nova entrada é obtida

O grafo HOFG é definido como HOFG(V,E), onde:
- E, arestas de derivação F, arestas de desreferência R, e arestas de derivação de percurso D
- V, objetos alocados na memória o1, o2, o3, on, variáveis de programa p, p1*, p2*, free1, free2, ...freen

Para a análise do programa, é necessária a geração do HOFG para o programa inteiro, com o acompanhamento através dos diferentes módulos e arquivos.

----
#### Alteração 2

Modificar geração dos arquivos .bc, para que sejam lidos de forma separada:
- Arquivo A é convertido para .bc, e então é obtida sua versão HOFG.
- É possível que o mesmo precise de funções B, C... e afins, provenientes de outro arquivo ou módulo
- A partir disso, funções faltantes devem ser adicionadas a lista de dependências, sendo removidas ao obter seus devidos sumários e pontos necessários atualizados.
----

O sumário de uma função contém:
- A lista de argumentos.
- Informações sobre itens alocados e desalocados.
- Valores retornados.

A etapa da poda consiste num DFS, para cada nó do tipo objeto como começo do percurso, para encontrar caminhos do nó objetivo dentro do HOFG do programa inteiro

"Summary application" é a etapa de aplicação dos sumários das funções dentro do HOFG.

#### Análise sensível a contexto e a caminho

Uma aresta do HOFG é rerpesentada pela tripla $<I2, c, I2>$, onde $I1$ e $I2$ são as instruções, e o $C$ é a condição do grafo de fluxo de controle que leva o item $I1$ ao $I2$

Condicionais que podem ser resolvidas são geradas pelo LLVM IR.

Cada campo de uma struct é mapeado pelo LLVM IR como uma variável distinta, isso permite que as instruções envolvendo tais campos sejam verificados, cada alocação e desalocação de um campo é explícito de forma distinta e mapeado de forma separada.

As arestas do grafo são direcionadas conforme o fluxo do programa. A ordem de execução de cada instrução é levado em consideração na fase de adição de arestas do HOFG, o que torna a análise sensível a fluxo.