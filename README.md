# Estudo de Caso A1 — O Problema da Entrega Inteligente


**Unidade Curricular:** Estruturas de Dados e Análise de Algoritmos  
**Professor:** Alexandre "Montanha" de Oliveira  
**Aluno:** Maria Paula Pereira Sousa - 12315841


## Questão 1 — Complexidade combinatória


### a)  O problema de roteamento da FastBite pertence à classe P, NP ou NP-Completo? Justifique sua resposta com base na definição formal de cada classe.

**R.:** No contexto do estudo de caso, a formulação de roteamento está diretamente relacionada ao TSP e ao VRP, ambos citados no enunciado como problemas clássicos cuja explosão combinatória torna a busca exaustiva inviável. A versão de decisão do problema de roteamento da FastBite pode ser entendida assim: “existe uma atribuição e um conjunto de rotas que respeitam as restrições e resultam em custo total menor ou igual a um certo limite?”. Dada uma solução candidata, é possível verificar em tempo polinomial se cada pedido foi atribuído corretamente, se as capacidades foram respeitadas, se os prazos urgentes foram atendidos e se o custo total fica dentro do limite; portanto, o problema está em NP.


### b) Demonstre, de forma intuitiva, que o problema da FastBite pode ser reduzido ao TSP. Para isso, descreva como transformar uma instância do problema de roteamento da FastBite em uma instância do TSP.

**R.:** Uma forma intuitiva de reduzir o problema da FastBite ao TSP é considerar uma versão simplificada dele. Suponha apenas um entregador, capacidade suficiente para carregar todos os pedidos, ausência de restrições de prioridade e preparação, e a necessidade de sair de uma posição inicial, visitar os pontos relevantes e minimizar a distância total percorrida. Nesse caso, cada ponto de coleta ou entrega pode ser modelado como um vértice de um grafo, e a distância entre dois pontos pode ser representada como o peso da aresta correspondente.

A instância do problema seria transformada assim:

- A posição inicial do entregador vira a cidade de partida.
- Cada restaurante e cada cliente vira uma “cidade” a ser visitada.
- As distâncias entre todos os pares de pontos são calculadas pela métrica adotada, no cenário, a distância Manhattan.
- O objetivo passa a ser encontrar a ordem de visita de menor custo total.

Se ainda for desejado preservar a regra de que a coleta deve ocorrer antes da entrega correspondente, isso deixa de ser o TSP puro e aproxima o problema de variantes mais restritas de roteamento. Porém, para fins de redução intuitiva, basta observar que o TSP aparece como um caso particular dentro do problema mais geral da FastBite. Em outras palavras, se fosse possível resolver o problema da FastBite de forma ótima e eficiente em seu caso geral, então também seria possível resolver o TSP usando essa mesma solução, pois bastaria converter cada cidade do TSP em um ponto de visita do entregador. Isso mostra que o problema da FastBite é pelo menos tão difícil quanto o TSP.


### c) Por que a solução por força bruta é inviável para o problema da FastBite em produção? Calcule ou estime a quantidade de permutações possíveis de entregas para um cenário com 8 pedidos e 3 entregadores (cada um com até 3 pedidos). Mostre o raciocínio combinatório e indique a complexidade assintótica resultante.

**R.:** A força bruta é inviável porque o número de possibilidades cresce de forma explosiva à medida que se aumenta a quantidade de pedidos e entregadores. O próprio enunciado afirma que, com poucos pedidos, o número de combinações já chega a dezenas de milhares, e em cenários reais de pico a avaliação exaustiva torna-se computacionalmente inviável dentro do limite de 2 segundos.

Para estimar o cenário com 8 pedidos e 3 entregadores, pode-se separar o raciocínio em duas etapas: atribuição dos pedidos e ordenação das entregas dentro de cada entregador.

Primeiro, para a atribuição, cada um dos 8 pedidos pode, em princípio, ser alocado a um dos 3 entregadores. Isso gera uma estimativa de atribuições possíveis bruta de:

$$
3_8 = 6561 
$$


Depois, para cada atribuição, é necessário considerar a ordem de execução das entregas. Como cada entregador pode receber até 3 pedidos simultâneos, uma distribuição compatível seria, por exemplo, 3 pedidos para o primeiro, 3 para o segundo e 2 para o terceiro. O número de ordenações internas seria então:

$$
3! \cdot 3! \cdot 2! = 6 \cdot 6 \cdot 2 = 72
$$

Logo, uma estimativa para o total de combinações seria:

$$
6561 \cdot 72 = 472392
$$


Esse valor ainda é conservador, porque simplifica várias restrições reais. Em uma modelagem mais fiel, cada pedido envolve coleta e entrega, aumentando o número de eventos a ordenar; além disso, seria preciso verificar precedência, janelas de tempo, capacidade e impacto do trânsito. Se fossem considerados 16 eventos (8 coletas + 8 entregas), a contagem bruta de sequências poderia se aproximar de uma ordem fatorial, o que cresce muito mais rápido que qualquer polinômio simples.

Assim, a complexidade assintótica de uma busca exaustiva é tipicamente **exponencial ou fatorial**, dependendo da modelagem exata. Em termos gerais, pode-se descrevê-la como algo da ordem de 
$$m^n \cdot n!$$
 ou pior, em que \(n\) é o número de pedidos e \(m\) o número de entregadores. Esse crescimento explica por que a força bruta não é adequada para uso em produção em uma plataforma que recebe lotes a cada 30 segundos e precisa decidir em até 2 segundos.


## Questão 2 — Abordagem Gulosa (Greedy)


### a) Descreva, em linguagem natural (sem código), o funcionamento desse algoritmo passo a passo para o cenário de exemplo da seção Dados do Cenário.

**R.:** No cenário fornecido, os pedidos são P1, P2, P3, P4 e P5, e os entregadores disponíveis são E1, E2 e E3, com posições e capacidades definidas. O algoritmo guloso funciona escolhendo, para cada pedido ainda não atribuído, o entregador disponível mais próximo do restaurante daquele pedido; depois, para cada entregador, a rota é montada sempre indo ao ponto mais próximo do ponto atual.

Levando em consideração a distância Manhattan, definida como:

$$
|x_1-x_2|+|y_1-y_2|=
$$

E aplicando a lógica ao cenário:

- Para P1, o restaurante está em (0,2). Sendo assim, as distâncias são:
  
    Restaurante: (0,2)

    - E1: (1,1) →

      $|1 - 0| + |1 - 2| = 1 + 1 = 2$

    - E2: (5,3) → 

      $|5 - 0| + |3 - 2| = 5 + 1 = 6$

    - E3: (7,6) → 

      $|7 - 0| + |6 - 2| = 7 + 4 = 11$

    **Dessa forma, P1 será atribuído ao E1 pois ele possui a menor distância e está disponível.**

- Para P2, o restaurante está em (1,1). As distâncias são: E1 = 0, E2 = 6, E3 = 11. Como E1 ainda tem capacidade, P2 também seria atribuído ao E1.

  Restaurante: (1,1)

  - E1: (1,1) →

    $|1 - 1| + |1 - 1| = 0 + 0 = 0$

  - E2: (5,3) →

    $|5 - 1| + |3 - 1| = 4 + 2 = 6$

  - E3: (7,6) →

    $|7 - 1| + |6 - 1| = 6 + 5 = 11$

  **Dessa forma, P2 será atribuído ao E1, pois ele possui a menor distância e está disponível.**

- Para P3, o restaurante está em (4,4). As distâncias são: E1 = 6, E2 = 2, E3 = 5. Como E1 já atingiu capacidade máxima, P3 tem que ir para outro entregador.

  Restaurante: (4,4)

  - E1 (não pode ser atribuído por não ter mais capacidade): (1,1) →

    $|1 - 4| + |1 - 4| = 3 + 3 = 6$

  - E2: (5,3) →

    $|5 - 4| + |3 - 4| = 1 + 1 = 2$

  - E3: (7,6) →

    $|7 - 4| + |6 - 4| = 3 + 2 = 5$

  **Dessa forma, P3 será atribuído ao E2, pois ele possui a menor distância e está disponível.**

- Para P4, o restaurante está em (6,1). As distâncias são: E1 = 5, E2 = 3, E3 = 6. Como E2 ainda tem capacidade, P4 vai para E2.

  Restaurante: (6,1)

  - E1: (1,1) →

    $|1 - 6| + |1 - 1| = 5 + 0 = 5$

  - E2: (5,3) →

    $|5 - 6| + |3 - 1| = 1 + 2 = 3$

  - E3: (7,6) →

    $|7 - 6| + |6 - 1| = 1 + 5 = 6$

  **Dessa forma, P4 será atribuído ao E2, pois ele possui a menor distância e está disponível.**

- Para P5, o restaurante está em (5,5). As distâncias são: E1 = 8, E2 = 2, E3 = 3. Como E2 já atingiu a capacidade máxima, P5 é atribuído ao E3.

  Restaurante: (5,5)

  - E1: (1,1) →

    $|1 - 5| + |1 - 5| = 4 + 4 = 8$

  - E2: (5,3) →

    $|5 - 5| + |3 - 5| = 0 + 2 = 2$

  - E3: (7,6) →

    $|7 - 5| + |6 - 5| = 2 + 1 = 3$

  **Dessa forma, P5 será atribuído ao E3, pois, apesar de não ser o mais próximo em distância, é o entregador disponível (E2 já está com a capacidade máxima).**


Depois da atribuição, cada entregador organiza a sua própria rota pelo critério do “vizinho mais próximo”. Por exemplo, E1 ficaria com P1 e P2; ele partiria de sua posição atual, iria primeiro ao ponto mais próximo entre os restaurantes de seus pedidos, e depois escolheria sempre o próximo ponto de coleta ou entrega mais perto dentre os ainda pendentes. O mesmo raciocínio seria usado para E2 com P3 e P4, e para E3 com P5. O algoritmo não testa várias combinações globais; ele apenas toma a melhor decisão imediata em cada passo com base na distância local.


### b) Explique por que esse algoritmo é classificado como guloso (greedy). Qual é a propriedade de escolha local que ele aplica?

**R.:** Esse algoritmo é classificado como guloso porque ele constrói a solução gradualmente, sempre escolhendo, em cada etapa, a alternativa que parece melhor naquele instante, sem reconsiderar sistematicamente decisões anteriores. Ele não avalia o efeito global de longo prazo da atribuição atual sobre as próximas entregas, nem compara essa escolha com todas as outras combinações possíveis.

A propriedade de escolha local aplicada é: **atribuir cada pedido ao entregador mais próximo do restaurante naquele momento** e, dentro de cada rota, **seguir para o próximo ponto mais próximo da posição atual**. Essa estratégia tenta minimizar o custo imediato de deslocamento a cada passo. O problema é que minimizar localmente não garante minimização global, porque uma decisão aparentemente ótima agora pode bloquear uma combinação melhor depois, especialmente quando há restrições de capacidade, urgência e múltiplos pedidos concorrendo pelos mesmos entregadores.


### c) Apresente um contraexemplo concreto (pode ser hipotético ou adaptado do cenário) em que a solução gulosa leva a um resultado subótimo — ou seja, em que existiria uma atribuição melhor que o algoritmo guloso não encontraria.

**R.:** Considere um cenário hipotético com dois entregadores, E1 e E2, ambos com capacidade para 2 pedidos. Suponha que E1 esteja muito próximo de dois restaurantes centrais, enquanto E2 esteja um pouco mais longe desses restaurantes, mas muito mais próximo de uma região periférica onde há dois pedidos urgentes. Imagine ainda que cheguem quatro pedidos: P1 e P2 em região central, P3 e P4 na periferia, sendo P3 e P4 urgentes.

O algoritmo guloso pode começar atribuindo P1 e P2 a E1, o que parece ótimo localmente. Porém, se P3 também estiver um pouco mais próximo de E1 do que de E2, o algoritmo pode acabar consumindo a última capacidade útil de E1 com um pedido que não era urgente, deixando E2 com uma configuração pior para atender P4 dentro do prazo. Em consequência, um pedido urgente pode atrasar. Já uma solução melhor globalmente reservaria E1 para um pedido central e um periférico estratégico, ou deixaria E2 concentrado nos urgentes da periferia, mesmo que em uma das atribuições individuais ele não fosse o entregador mais próximo.

Adaptando ao espírito do cenário da FastBite, também é possível imaginar que E2 esteja perto de P3 e P5, mas P5 seja urgente e muito mais sensível ao tempo, enquanto P3 seja premium, porém menos crítico em prazo. Se o algoritmo atribuir primeiro P3 a E2 apenas por proximidade, pode reduzir a capacidade disponível para atender P5 da forma ideal. O resultado final pode ter maior tempo total ou até violar a janela do urgente, mesmo que cada decisão isolada tenha parecido boa no momento.


### d) Qual é a complexidade de tempo desse algoritmo guloso em função de n pedidos e m entregadores? Justifique.

**R.:** Na fase de atribuição, para cada um dos n pedidos é necessário examinar até m entregadores para descobrir qual deles está mais próximo e ainda pode receber o pedido. Isso leva a um custo de O(nm).

Depois da atribuição, cada entregador precisa ordenar seus pontos de coleta e entrega usando a regra do ponto mais próximo. Se um entregador receber k paradas, a estratégia de vizinho mais próximo exige, no pior caso, comparar o ponto atual com todos os restantes, depois com quase todos os restantes, e assim por diante, gerando custo O(k^2). Somando todos os entregadores, o custo total dessa segunda fase pode ser limitado por O(n^2), considerando o total de pedidos distribuídos.

Portanto, uma estimativa razoável para a complexidade do algoritmo guloso completo é **O(nm + n^2)**. Em muitos cenários práticos, essa complexidade é muito menor que a de métodos exatos exponenciais, o que explica sua atratividade operacional em sistemas que precisam responder muito rapidamente.


## Questão 3 — Programação Dinâmica e Divisão e Conquista

### a) A equipe sênior sugeriu aplicar Programação Dinâmica (PD) para resolver o problema de roteamento de cada entregador isoladamente (assumindo que a atribuição já está feita).

**R.:** A Programação Dinâmica é aplicável ao roteamento de um único entregador com k pedidos, principalmente quando esse subproblema é tratado como uma variante do TSP com memoização sobre subconjuntos de paradas. Isso faz sentido porque, ao fixar a atribuição, o problema deixa de decidir “quem atende qual pedido” e passa a decidir apenas “em que ordem visitar os pontos de coleta e entrega” para minimizar o custo total.

Informalmente, o subproblema da PD pode ser definido como: “qual é o menor custo para sair de um ponto atual, tendo já visitado um certo conjunto de pontos, e completar todas as visitas restantes respeitando as restrições de precedência e capacidade?”. Esse formato apresenta duas características clássicas que favorecem PD: subestrutura ótima e sobreposição de subproblemas. O melhor caminho restante a partir de um estado depende apenas do conjunto de pontos já visitados e da posição atual, e muitos estados se repetem em caminhos diferentes.

No entanto, o custo é alto. Em uma formulação inspirada no algoritmo de Held-Karp para TSP, o tempo cresce na ordem de **O(k^2·2^k)** e o espaço na ordem de **O(k·2^k)**. Isso pode ser administrável para valores pequenos de k, mas se torna rapidamente impraticável em tempo real. Em um sistema com decisão máxima de 2 segundos por ciclo, essa abordagem pode ser aceitável apenas quando cada entregador tiver poucos pedidos, algo como 8, 10 ou talvez 12 paradas relevantes, dependendo do hardware e da implementação. Acima disso, o crescimento exponencial compromete o uso em produção.


### b) Avalie a aplicabilidade de Divisão e Conquista ao problema de roteamento da FastBite.

**R.:** Divisão e Conquista pode ser aplicada de forma parcial ao problema da FastBite, mas não de maneira perfeita. A ideia seria dividir o problema global em subproblemas menores. Por exemplo, separando a cidade em zonas geográficas, resolvendo a atribuição e o roteamento em cada zona, e depois combinando os resultados. Isso funciona melhor quando os subproblemas são quase independentes, ou seja, quando a maioria dos restaurantes, clientes e entregadores relevantes está concentrada dentro da mesma região e há pouca necessidade de cruzar fronteiras.

A divisão geográfica por zonas ou quadrantes pode ser útil porque reduz o espaço de busca. Em vez de considerar todos os pedidos e entregadores da cidade ao mesmo tempo, o sistema resolve blocos menores, o que melhora a escalabilidade e favorece o processamento em paralelo. Essa é uma estratégia compatível com o tipo de operação em larga escala descrita no estudo de caso, com muitos pedidos por dia e necessidade de resposta rápida.

As limitações aparecem nas fronteiras entre zonas. Um entregador muito próximo de um restaurante ou cliente que está no quadrante vizinho pode ser ignorado se a separação for rígida, produzindo decisões artificiais e piores do que a solução global. Além disso, pedidos longos que cruzam regiões ou desequilíbrios de demanda entre zonas podem tornar os subproblemas interdependentes. Portanto, Divisão e Conquista é mais apropriada como estratégia de engenharia para reduzir a complexidade do sistema do que como técnica exata capaz de garantir a melhor solução global.


## Questão 4 — Comparação das Abordagens

| Critério | Greedy | Programação Dinâmica | Divisão e Conquista |
|---|---|---|---|
| **Qualidade da solução** | Boa em muitos casos, mas sem garantia de ótimo global | Ótima no subproblema modelado corretamente, desde que o tamanho seja pequeno o bastante | Depende da qualidade da partição; pode ser boa, mas pode perder qualidade nas fronteiras |
| **Complexidade de tempo** | Baixa a moderada, como \(O(nm + n^2)\) | Exponencial, tipicamente \(O(k^2 2^k)\) no roteamento isolado | Reduz o problema global em blocos menores, mas depende do custo interno de cada bloco |
| **Complexidade de espaço** | Baixa | Alta, tipicamente \(O(k 2^k)\) | Moderada; exige estruturas para particionamento e coordenação entre zonas |
| **Viabilidade em tempo real (≤ 2s)** | Alta, especialmente como solução inicial | Baixa para instâncias médias ou grandes | Média a alta, se combinado com heurísticas locais |
| **Escalabilidade com aumento de n** | Boa, embora a qualidade possa cair | Ruim, por crescimento exponencial | Boa, desde que a divisão mantenha os subproblemas equilibrados |
| **Facilidade de adaptação a mudanças** | Alta; reage bem a novos pedidos e mudanças de trânsito | Baixa; recalcular estados pode ser caro | Média; mudanças locais podem ser tratadas por zona, mas há custo de coordenação |

**R.:** Entre as três abordagens, a gulosa tende a ser a mais adequada como solução principal da FastBite, desde que não seja usada sozinha. A Programação Dinâmica entrega alta qualidade apenas em instâncias pequenas, mas seu custo exponencial entra em choque com a exigência de resposta em até 2 segundos. Já Divisão e Conquista ajuda muito na escalabilidade ao quebrar a cidade em regiões, mas precisa de uma técnica interna eficiente para resolver cada parte. Por isso, a estratégia mais realista é usar particionamento geográfico para reduzir o tamanho do problema e, dentro de cada região, aplicar uma heurística gulosa com refinamentos locais. Assim, obtém-se uma solução boa, rápida e adaptável ao ambiente operacional descrito.


## Questão 5 — Solução de Engenharia Real


### a) Explique, com suas palavras, o que é uma heurística no contexto de algoritmos. Por que heurísticas são preferíveis a soluções ótimas em sistemas como a FastBite?

**R.:** Uma heurística é uma estratégia prática de resolução que busca encontrar uma solução boa em pouco tempo, sem garantir que ela seja a melhor possível em termos matemáticos. Em algoritmos, heurísticas costumam explorar regras simples, aproximações ou critérios locais para reduzir drasticamente o custo computacional .

Em sistemas como a FastBite, heurísticas são preferíveis porque o problema precisa ser resolvido sob restrição severa de tempo. O enunciado estabelece que nenhuma decisão pode levar mais de 2 segundos e que a plataforma opera com grandes volumes de pedidos em ciclos constantes. Nessa situação, uma solução ótima pode ser teoricamente desejável, mas praticamente inútil se demorar demais para ser calculada. Uma solução quase ótima, gerada rapidamente, produz mais valor operacional do que uma solução perfeita que chega tarde.


### b) Descreva, em linhas gerais, como uma solução de engenharia real para a FastBite poderia ser estruturada.

**R.:** Uma solução realista para a FastBite poderia ser organizada em quatro etapas complementares:

1. **Particionamento geográfico dos pedidos:** A cada ciclo, os pedidos seriam agrupados por região, setor ou quadrante da cidade. Isso reduz o tamanho do problema em cada bloco e permite processamento paralelo, o que melhora a escalabilidade.

2. **Construção de uma solução inicial gulosa:** Dentro de cada região, o sistema atribuiria pedidos aos entregadores mais adequados segundo critérios como proximidade, capacidade, tipo de veículo, prioridade e prazo. Em seguida, cada rota inicial seria montada por uma heurística simples, como vizinho mais próximo ou inserção do pedido menos custoso.

3. **Refinamento local:** Após a solução inicial, o sistema aplicaria pequenas melhorias, por exemplo trocar dois pedidos entre entregadores vizinhos, mudar a ordem de duas entregas, ou mover um pedido urgente para outro entregador se isso reduzir atraso total. Esse refinamento local melhora a solução sem precisar explorar todo o espaço combinatório.

4. **Interrupção por limite de tempo:** O algoritmo funcionaria sob orçamento rígido de processamento. Quando o tempo se aproximasse do limite operacional, o sistema encerraria a busca e publicaria a melhor solução encontrada até aquele instante. Essa abordagem é coerente com o cenário em que rapidez e robustez são mais importantes do que otimalidade absoluta.

O tipo de arquitetura apresentado combina escalabilidade, boa qualidade média e capacidade de adaptação contínua a trânsito, novos pedidos e indisponibilidade de entregadores.


### c) Quando vale a pena buscar a solução ótima? Dê um exemplo de situação — mesmo dentro do contexto de delivery — em que calcular a solução exata seria razoável.

**R.:** Vale a pena buscar a solução ótima quando a instância é pequena, estável e tem alto valor estratégico, de modo que o custo computacional adicional se justifique. Isso ocorre quando há poucos pedidos, pouca variação dinâmica e tempo suficiente para processar a melhor solução sem prejudicar a operação.

Um exemplo dentro do contexto de delivery seria o planejamento antecipado de uma rota de entregas agendadas para o dia seguinte em uma região específica, com poucos pedidos já confirmados e sem urgência de decisão instantânea. Outro caso razoável seria otimizar manualmente a distribuição de pedidos de uma operação corporativa fechada, como a entrega de almoços para uma empresa com 6 ou 8 destinos fixos. Nesses cenários menores, calcular a solução exata pode ser viável e útil, porque o problema não sofre a mesma pressão de tempo do despacho em tempo real.


## Questão 6 — Reflexão Crítica

Em sistemas de larga escala, “bom o suficiente” passa a ser a melhor decisão técnica quando o custo de buscar a solução ideal supera o benefício prático dessa idealização. No caso da FastBite, o problema de atribuição e roteamento cresce de forma combinatória e se relaciona a problemas clássicos como TSP e VRP, que se tornam inviáveis para resolução ótima em tempo real.

Nesse contexto, insistir na solução perfeita pode degradar a qualidade real do serviço, porque uma decisão atrasada também é uma decisão ruim. A complexidade computacional impõe limites concretos: não basta que um algoritmo seja correto; ele precisa ser executável dentro das restrições do sistema. Por isso, a melhor engenharia nem sempre busca o ótimo teórico, mas sim o melhor equilíbrio entre qualidade, tempo de resposta, consumo de recursos e capacidade de adaptação.

