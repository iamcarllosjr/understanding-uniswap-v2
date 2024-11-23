# Os AMMs mantêm um conjunto de ativos de liquidez contra os quais as negociações podem ser feitas automaticamente ao longo de uma curva de preços. 

  - Os detentores de ativos são incentivados a fornecer seus tokens ao contrato inteligente do pool de liquidez em troca de uma parte das taxas de negociação.

# Como funcionam os AMMs
  - Um criador de mercado automatizado possui dois tokens (token X e token Y) no pool (um contrato inteligente). Permite que qualquer pessoa retire o token X do pool, mas deve depositar uma quantidade de token Y de tal forma que o total de ativos no pool não diminua, onde consideramos que o total é o produto dos valores dos dois ativos.
  - Os ativos são fornecidos ao pool por fornecedores de liquidez (qualquer pessoa), que recebem os chamados tokens LP para representar sua parte do pool. Os saldos dos provedores de liquidez são rastreados de maneira semelhante a como ERC 4626 funciona. A diferença entre um AMM e ERC 4626 é que o ERC 4626 suporta apenas um ativo, mas um AMM tem dois tokens. Assim como um cofre, a parte do pool dos provedores de liquidez’ permanece a mesma, mas o produto 
  `xy` fica maior, então sua fatia é maior.
  - A liquidez AMM é gerada quando os detentores de tokens bloqueiam seus tokens em um pool de liquidez em troca de um retorno percentual sobre seus ativos. De um modo geral, quanto mais liquidez for bloqueada em um pool, maior será o tamanho do pool em relação a um comércio, o que resulta em menor derrapagem.

# Conceitos
  - Como essa solução de liquidez envolvia a criação de um mercado fundamentalmente diferente, exigia que vários conceitos fossem empilhados uns sobre os outros :

  **Piscina de Liquidez :**
  - Crie um conjunto de ativos contra os quais as negociações podem ser feitas por uma pequena taxa. A composição desta cesta de ativos será determinada pelas negociações que estão ocorrendo contra ela. Os arbitrageurs desempenharão um papel fundamental no reequilíbrio desse pool com o mercado em geral.

  **Curva de Preços :**
  - Crie uma curva de preço para o pool que ingere dados de preço de uma fonte externa confiável. O preço exato de uma negociação que está sendo feita contra o pool levará em conta o feed de preços externo, a composição atual do pool, bem como o efeito resultante que a negociação terá sobre a liquidez do pool.

  **Taxas de Negociação :**
  - Incentivar os detentores de ativos a fornecer seus ativos ao pool por meio de um retorno percentual sobre seus ativos. A maioria da taxa de negociação que o pool cobra será acumulada no pool. Isso também é para compensar suficientemente o detentor do ativo por bloquear seu ativo com o pool e se tornar um provedor de liquidez (LP).
  - O nível de taxa é a configuração mais direta que afeta diretamente os retornos de um LPilits. Para cada negociação que utiliza os tokens LPilits, o LP receberá um corte percentual do volume de negociação de acordo com o nível de taxa configurado para o pool. Com tudo o mais sendo igual, os comerciantes sempre querem negociar contra os pools com o menor nível de taxa para minimizar as taxas de negociação incorridas. Como tal, os pools com níveis de taxa mais baixos provavelmente experimentarão contagens de negociação mais altas em comparação com os pools com níveis de taxa mais altos.

  **Tokens LP :**
  - Mint novos tokens derivativos para a carteira LPs’ que permitirá a recuperação de seus ativos, bem como quaisquer taxas que tenham acumulado para o pool. Esses tokens LP representam uma proporção dos ativos agrupados e podem ser negociados.

  `Tomada como um todo, essa solução passou a ser conhecida como a Criador Automatizado de Mercado (AMM). “Automated” porque está sempre disponível para negociação e não depende da interação tradicional entre compradores e vendedores onde as encomendas devem ser apresentadas primeiro`

# Vantagens dos AMMs
**Os AMMs não tem um spread de compra e venda**
  - O spread é a diferença entre o preço de compra e o preço de venda. Em um mercado tradicional, os spreads são necessários para cobrir os custos de transação e gerar lucro para o broker. No entanto, em um AMM, os spreads não são necessários porque o preço é determinado pela relação entre os dois tokens no pool. Em um AMM, a descoberta de preços é automática. Determinado pela proporção de ativos no pool.

**Os AMMs são mais eficientes**
  - Em um AMM, as trocas são executadas diretamente no contrato inteligente, o que elimina a necessidade de intermediários como corretoras. Isso reduz custos e aumenta a eficiência.

**AMMs dobrou como um oráculo**
  - Como o preço dos ativos é determinado automaticamente, outros contratos inteligentes podem usar um AMM como um oráculo de preços. No entanto, os preços AMM podem ser manipulados com empréstimos flash, portanto, as salvaguardas precisam ser postas em prática ao usar AMMs dessa maneira. No entanto, é importante que os dados de preços sejam fornecidos gratuitamente.

# Desvantagens das AMMs
  - Há duas grandes desvantagens para os criadores de mercado automatizados: 1) o preço sempre se move e 2) perda impermanente para os provedores de liquidez.

**Mesmo pequenas trocas/compras movimentam o preço em AMMs**
  - Se você fizer um pedido para comprar 100 ações da Apple, seu pedido não fará com que o preço se mova porque existem milhares de ações disponíveis para venda pelo preço especificado. Este não é o caso de um criador de mercado automatizado. Todo comércio, não importa quão pequeno, move o preço.
  Isso tem duas implicações. Uma ordem de compra ou venda geralmente encontrará mais derrapagem do que em um modelo de livro de pedidos, e o mecanismo de troca convida a ataques de sandwich.

**Ataques sanduíche são em grande parte inevitáveis em AMMs**
  - Uma vez que cada ordem vai mover o preço, os comerciantes MEV (Valor Extraível Máximo) vão esperar por uma ordem de compra suficientemente grande para entrar, em seguida, colocar uma ordem de compra logo antes da ordem da vitima (Com uma taxa de gas mais alta) e uma ordem de venda logo após ela (Apos a vitima ter comprado por um preço mais alto). A ordem de compra líder (Do atacante) aumentará o preço para o trader original (vitima), o que lhes dá pior execução. Isso é um ataque de sanduíche, uma vez que o comércio da vitima é “sandwiched” entre os atacantes.
  1) Atacante: primeira compra (corrida frontal): aumenta o preço da vítima 
  2) Vitima comprar: aumentar o preço ainda mais
  3) Atacante: venda a primeira compra com lucro

  - Os provedores de liquidez não determinam diretamente os preços dos ativos em uma pool. Em vez disso, eles fornecem a liquidez inicial, e o algoritmo AMM calcula automaticamente os preços com base nas quantidades disponíveis de cada token no pool.
  - Além da Constant Product Formula, existem outros algoritmos AMM comuns, como a Fórmula de Soma Constante (para tokens com o mesmo preço), Fórmula Média Constante (permite mais de dois ativos em um pool) e Stableswap Invariant (adequado para stablecoins) 


# Impermanent Loss
  - Perda impermanente refere-se a uma perda temporária não realizada de valor de capital que surge ao fornecer liquidez para protocolos AMM. Em sua forma mais simples, a perda impermanente é a diferença de valor entre manter seus ativos versus utilizar os ativos para comercializar faz e ganha rendimento. A perda impermanente ocorre devido ao fato de que os índices de token do pool de liquidez estão mudando constantemente de acordo com as negociações contra ele.

  - Em DEXs AMM, os Provedores de Liquidez (LP) contribuem com fundos para um pool de liquidez, que permite aos usuários comprar e vender um ativo específico em um troca descentralizada (DEX). O provedor de liquidez ganha uma parte das taxas de negociação geradas pela DEX em troca de sua contribuição de liquidez de mercado. No entanto, o valor dos ativos no pool de liquidez pode e flutuará ao longo do tempo devido à atividade de compra/venda em relação ao pool, que altera o saldo de liquidez original do pool.

  - Ao fornecer liquidez a um pool, os LPs consentem que seus tokens sejam negociados automaticamente pelo contrato inteligente sempre que um trader inicia uma negociação. Observe que os LPs estão sempre no final curto do comércio, pelo qual um comerciante trocará o token menos valioso em troca dos tokens mais valiosos do Pool. Por exemplo, em um swap ETH para USDC, o comerciante fornece ETH para o LP (através do contrato inteligente) em troca de USDC. O resultado final do comércio resulta em ETH valendo menos após o comércio (ou seja, é por isso que a venda de um token resulta em uma diminuição de preço). Como os LPs não conseguem controlar esse aspecto do comércio, as taxas de negociação cobradas também devem cobrir os riscos de perda impermanentes aumentados durante períodos de extrema volatilidade.

  - Como resultado, o valor total da liquidez fornecida pelo LP poderia diminuir em relação ao valor potencial se o LP tivesse mantido seus ativos e optado por não participar do pool de liquidez em primeiro lugar. Essa redução temporária do valor de capital é conhecida como Perda Impermanente.

  - Dito isso, perda impermanente não é a forma ideal de nomear esse fenômeno. O termo “impermanência” refere-se ao fato de que se o preço dos ativos voltarem ao que eram no momento do depósito, as perdas serão mitigadas. No entanto, se você retirar seus fundos quando a relação de preços for diferente da relação no momento de depósito, as perdas serão permanentes. Em alguns casos, as taxas de trading podem mitigar as perdas, mas ainda é importante considerar os riscos.

  ```Imagem em assets```

## Exemplo de Perda Impermanente

**1. Uma pool de ETH/USDT existente**
   - Há um pool inicial de ETH/USDT com uma proporção de 50:50 dos seguintes valores de token :
    1. ETH: 100
    2. USDT: 20.000
  `Com base na fórmula constante do produto detalhada no Exemplo AMM, a constante para o pool é agora 20.000.000 (ou seja. Tokens ETH x Tokens USDT).`

**2. LP Adiciona Liquidez**
  - Um LP decide adicionar 5 ETH e o valor correspondente de 10.000 USDT (ou seja. 1 ETH = 2,000 USDT) para a pool. Observe que os saldos simbólicos do pool aumentam com a constante do pool, aumentando também pelo valor correspondente. Criticamente, a relação ETH:USDT no pool permanece a mesma.
  - Com esta adição, o LP possui 4,76% da liquidez do pool (5/105 ou 10.000/210000). Isso é representado por um token LP que é devolvido ao provedor de liquidez.

**3. O Trader compra ETH**
  - Após a contribuição de liquidez do LP, um trader compra 5 ETH do pool. Usando a fórmula constante do produto, o trader envia 10.500 USDT para o pool em troca de 5 ETH. O ETH é retirado do pool e transferido para o Negociador enquanto o USDT é adicionado ao pool.
  - Observe que a relação ETH:USDT do pool muda, com 1ETH agora valendo mais USDT após a troca. A proporção de liquidez do pool do LP ainda permanece a mesma, em 4,76%. Criticamente, a constante da pool permanece inalterada em todo o comércio.

**4. LP Retira a Liquidez**
  - Após a negociação, o LP decide retirar sua liquidez do pool. Como a proporção do pool mudou devido ao comércio, podemos então comparar o valor total da posição do LP (4,76% do pool por etapa 2) antes e depois do comércio : 
  ```Imagem em assets```

  - Para ver a perda impermanente, teremos que comparar o valor após a negociação com o valor da posição se o LP tivesse acabado de manter os 5 ETH iniciais e 10.000 USDT. Observe que, se o LP tivesse apenas mantido os dois tokens o valor total de seus tokens seria 25 USD a mais (21.025.00–21.000) do que se seus tokens fossem fornecidos ao pool de liquidez. Este déficit de 25 USD é o que é conhecido como perda impermanente.

  - De acordo com os depósitos no pool, o valor constante do pool também é diminuído pelo valor de retirada correspondente.

# Por que Fornecer Liquidez ?
  - Se cada negociação resulta em perda impermanente para o LP, por que os LPs seriam incentivados a assumir os riscos de perda impermanentes dadas as taxas nominais ? A resposta para isso reside no fato de que os pools de liquidez não existem isoladamente, pois existem muitas trocas de token externas, cada uma com seu próprio preço de par de token. Se o preço do pool subir acima do preço de mercado, as oportunidades de arbitragem resultarão no reequilíbrio do pool e vice-versa. Através deste reequilíbrio, os LPs ganham taxas tanto do comércio inicial quanto do comércio de arbitragem.

  - Com efeito, os arbitrageurs são um componente crítico do projeto AMM, garantindo que o pool sempre se reequilibre. É este período entre o reequilíbrio do pool que resulta em tais perdas é denominado “impermanent”. Se o pool retornar à proporção inicial, os LPs poderão retirar a mesma quantidade de tokens adicionados com as taxas adicionais acumuladas ao fornecer liquidez.

  - No entanto, isso o risco de perda impermanente pode se tornar permanente se as avaliações de pares de tokens divergem significativamente umas das outras e nunca se aproximam novamente da proporção inicial quando a liquidez foi adicionada. É mais provável que isso aconteça para pares de tokens menos correlacionados e, portanto, taxas de negociação mais altas são necessárias para compensar os riscos de perda impermanentes. Esta é a razão pela qual as taxas de negociação de pool tendem a estar alinhadas com a correlação de tokens.

# Mitigação de Perdas Impermanentes
  - Como um LP, existem algumas estratégias que você pode adotar para minimizar o impacto da IL em sua posição de liquidez :

  **1. Rebound de Preços :**
  - Aguarde até que o preço entre os tokens fornecidos retorne ao preço inicial ao qual a liquidez foi adicionada. Embora isso possa ser apenas uma questão de tempo para pares estáveis, pares mais exóticos ou mudanças de mercado podem resultar em tais perdas tornando-se permanentes, portanto, isso nem sempre é uma estratégia viável.

  **2. Faixa de Preço Mais Ampla :**
  - Ao adicionar liquidez a uma faixa de preço mais ampla, a mesma mudança no preço resulta em menos da sua liquidez sendo usada no mercado. Embora isso reduza a IL, isso também significa que as taxas geradas também serão reduzidas de acordo.

  **3. Níveis de Taxa Mais Elevados :**
  - A seleção de um pool de AMM com um nível de taxa mais alto garante que você seja pago, mas sempre que sua liquidez for utilizada, reduzindo assim a IL efetiva por negociação. Observe que, como as taxas são incorridas pelos traders, as negociações por meio de pools de níveis de taxa mais baixos sempre serão priorizadas, pois o preço de execução estará mais próximo do preço de mercado.

  - Os DEXs estão dando aos LPs mais autonomia sobre as configurações de taxas, mas sem o contexto adequado, selecionar um nível de taxa mais alto só resultará em taxas de gás desperdiçado. Quanto maior o nível de taxa, maior o prêmio de liquidez que os traders devem estar dispostos a incorrer para executar uma ordem de mercado contra o pool. Tal disposição dependerá da profundidade do mercado, com tokens mais exóticos comandando prêmios mais altos.

  **4. Taxas Dinâmicas :**
  - Fornecer liquidez em AMMs que tenham um recurso de taxa dinâmica que se ajusta de acordo com o spread de negociação. Em tais protocolos, a taxa de negociação escala com a volatilidade do mercado, garantindo um prêmio maior quando há um risco maior de IL.

  **5. Yield Farming :**
  - Vários protocolos de liquidez fornecem recompensas adicionais aos LPs em troca de bloquear sua liquidez no protocolo. Ao cultivar essas recompensas, você pode compensar os riscos de IL com as recompensas de liquidez acumuladas.

# Conclusão
  - Embora a perda impermanente (IL) possa ser muito intimidante para os LPs, é fundamental enquadrá-la no contexto dos AMMs. Nesta luz, a IL compara efetivamente um cenário teórico de melhor caso com o desempenho real do mercado.

  - Negociacoes contra um AMM sao automatizadas, portanto, consentindo em fornecer liquidez, voce esta sempre tomando o outro lado de um swap iniciado por um comerciante. Ou seja, o comerciante só escolhe executar o comércio se eles pensam que eles estão trocando um token de menor valor por um token de maior valor. Em suma, quaisquer negociações contra sua liquidez sempre resultarão em IL.

  - A IL é, portanto, um resultado natural do processo de criação de mercado, pelo qual o rendimento só pode ser gerado assumindo o risco de mercado de que os preços se desviem permanentemente do preço inicial. Simplificando, a taxa de negociação é um prêmio que é cobrado aos traders por assumir riscos de volatilidade de preços.

  - Como um LP, a questão mais importante é se as taxas e recompensas geradas serão suficientes para cobrir a IL esperada para a duração selecionada. Isso significa ter uma convicção razoável de que os preços vão se recuperar e ter a paciência de esperar até o momento certo para realizar qualquer mercado fazendo lucros.