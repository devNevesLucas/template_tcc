## Workflow da ferramenta HOFG SI

Programa é executado com pasta de projeto ou com arquivo .c solitário.

Grafo HOFG é inicializado vazio.

Para cada arquivo de entrada:
- Arquivo é compilado para o formato .bc;
- Caminho do arquivo é utilizado para ser lido com biblioteca api LLVM-C;
- Linha por linha do arquivo é lida;
- As linhas de origem de tais operações se tornam visíveis a partir de flag utilizada durante a compilação individual de cada arquivo;
- Para cada função encontrada, sumário é inicializado;
- Funções dependentes são armazenadas em hashmap de dependências;
- Para cada variável de ponteiro obtida, um nó dentro do HOFG é criada, contemplando também SI;
- Para cada alteração de um símbolo existênte, novo nó é criado a partir do nó original, contendo caso necessário, as mudanças de ponteiro, representado pelo SI;
- A definição de um ponteiro deve criar um tipo de nó "Objeto", representando uma nova variável ponteiro encontrada;
- Ao encontrar um free, nó de tipo "Liberação" deve ser adicionado ao grafo, com aresta proveniente de seu objeto conhecido;

----
- Deve existir a possibilidade de adicionar como parâmetros de entrada do programa, as funções de alocação e desalocação a serem consideradas em tal projeto ou código a ser analisado. Apesar de existirem heurísticas capazes de identificar tais funções, esta ação pode adiantar o processo da análise
----

- Ao encontrar uma pendência de função, tal pendencia deve ser anotada, para ser preenchida posteriormente, numa fase de substituição de chamadas de funções pelos seus sumários;

Após montagem do grafo, é iniciada etapa de substituição de chamadas pelos sumários obtidos.

Posteriormente, é iniciada etapa de poda do grafo

A partir de cada nó do tipo "Objeto", é realizada:
- Busca em profundidade a procura de nós de liberação para cada ramo do objeto;
- Ao encontrar nó de liberação, todos os nós de tal ramificação devem ser removidos;
- Verificação dos SIs de cada nó deve ser realizada, a procura de operações inválidas;
- Como diferença do HOFG original, um nó de objeto pode ser apagado somente se todas as suas ramificações forem corretamente liberadas, fazendo com que nenhuma pendência seja mantida; Com isso, é possível mapear um vazamento de memória a partir da alocação do objeto até o ponto do fluxo no qual o vazamento ocorre.

Após poda da árvore, é iniciado processo de análise dos nós restantes:
- Caso não haja nó algum no grafo HOFG, nenhum vazamento foi detectado, indicando a ausência de vazamentos de memória
- Para nós restantes, obter linhas e arquivos de origem e exibir tais dados no formato de texto, com as classificações: "must leak" e "maybe leak"