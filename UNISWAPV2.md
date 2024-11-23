# Uniswap V2

### O Uniswap é um aplicativo DeFi que permite que os traders troquem um token por outro de maneira confiável. Foi um dos primeiros fabricantes de mercado automatizados para negociação (embora não o primeiro).

**O Uniswap V2 é um protocolo fundamental no DeFi, servindo de base para inúmeros outros AMMs (Automated Market Makers). Compreender o seu funcionamento, fornecer-lhe-á uma base valiosa para trabalhar com outros protocolos e projetos DeFi.**

# Uniswap V2 Contracts

**Vamos analisar os contratos do Uniswap V2 que funcionam nos bastidores para que o AMM funcione. Temos três contratos principais para compreender.**

1. **O primeiro é o contrato de fábrica :**
    -  https://github.com/Uniswap/v2-core/blob/master/contracts/UniswapV2Factory.sol
    - O contrato de fábrica é um contrato simples que é utilizado para implementar os contratos de pares. Os contratos de pares são usados para bloquear os tokens que queremos negociar. Por exemplo, podemos ter um contrato de par de ETH/USDT, ou DAI/ETH, ou DAI/MKR. O contrato de fábrica permite-nos criar estes pares.

    - O contrato Factory cria novos contratos de par quando necessário :
    `Recebe solicitações para criar novos pares de tokens.`
    `Verifica se o par já existe ou não.`
    `Se não existir, cria um novo contrato de par.`
    `Registra o endereço do novo contrato de par.`
    *Isso garante que cada par único de tokens tenha apenas um contrato de par associado.*

    - Também podemos interagir com os contratos de pares diretamente, para adicionar ou remover liquidez ou para trocar tokens. Isto é um pouco propenso a erros, pelo que também temos o contrato router para interagir com os contratos de PAR.
2. **Router :**
   - https://github.com/Uniswap/v2-periphery/tree/master/contracts
   - O contrato do router é utilizado como intermediário para adicionar ou remover liquidez dos pares e também para trocar tokens. Se trocarmos DAI por ETH, o router interage com o contrato do par DAI/ETH. Se quisermos trocar ETH por MKR, o router trocará ETH por DAI, DAI por MKR e, em seguida, enviará o MKR para nós.
   `Fornece métodos para fazer trades através de múltiplos pares.`
   `Permite trades complexos envolvendo mais de um par de tokens.`
   `Gerencia a rota mais eficiente para uma trade entre dois tokens quaisquer.`
   *O Router usa os contratos de par para executar as trades reais, mas oferece uma camada de abstração para facilitar interações mais complexas.* 

3. **Contratos de PAR (Pair Contracts):**
   - https://github.com/Uniswap/v2-core/blob/master/contracts/UniswapV2Pair.sol
   - São contratos usado para criarem pares, por exemplo, podemos ter um contrato de par de ETH/USDT, ou DAI/ETH, ou DAI/MKR, que são criados pelo contrato factory.

   - O contrato de par é um dos componentes mais cruciais do Uniswap V2. Ele representa uma pool de liquidez para um par específico de tokens.
   `Cada par de tokens tem seu próprio contrato inteligente.`
   `O contrato armazena os saldos dos dois tokens do par.`
   `Implementa a lógica de trocas entre os tokens do par.`
   `Utiliza o algoritmo de Constant Product Market Maker (CPMM).`
   - O contrato de par permite que os usuários façam trades entre dois tokens específicos, mantendo sempre um equilíbrio na relação entre os saldos dos tokens.
   **Liquidação :** Os fornecedores de liquidez depositam ambos os tokens do par no contrato.
   **Trocas :** Quando um usuário faz uma trade, ele interage diretamente com o contrato de par (Através do router).
   **Preço :** O preço da trade é determinado pela relação atual entre os saldos dos tokens no contrato.
   **Taxas :** Uma pequena taxa é cobrada em cada trade e distribuída aos fornecedores de liquidez.

**O contrato de par é o núcleo da funcionalidade do Uniswap V2. O Factory garante que cada par tenha um contrato único, enquanto o Router utiliza esses contratos para executar trades eficientes entre qualquer par de tokens.**
   - Juntos, eles criam um ecossistema descentralizado e flexível para trocas de tokens, onde :
    1. O Factory mantém a organização dos pares.
    2. O Router facilita interações complexas.
    3. Os contratos de par realizam as trocas reais e mantêm a liquidez.

**Essa arquitetura permite ao Uniswap V2 oferecer um mercado líquido e descentralizado para uma ampla variedade de pares de tokens.**

# Arquitetura de Uniswap V2

**A arquitetura do Uniswap V2 é surpreendentemente simples. Em seu núcleo está :**
- UniswapV2Pair : Contrato que detém dois tokens ERC20 que os comerciantes podem trocar contra, ou provedores de liquidez podem fornecer liquidez para.
- Cada diferente possível UniswapV2Pair tem um contrato UniswapV2Pair diferente para gerenciá-lo.
- Se o desejado UniswapV2Pair contrato não existe, um novo pode ser criado sem permissão a partir do UniswapV2Factory.

- Embora os comerciantes avançados ou contratos inteligentes possam interagir diretamente com um par contrato, a maioria dos usuários irá interagir com um par através de um roteador contrato (Router), que tem várias funções de conveniência, como a negociação entre pares numa transação para criar um par “synthetic” se ele não existir.

# Swap Math
   **A teoria da fórmula do Produto Constante: x * y ≥ k**
   - A fórmula constante do produto determina que, em um conjunto de pares de token X e token Y, o produto das duas quantidades de ativos no pool (X vezes Y) em todos os momentos deve permanecer constante.
   ```javascript
       x * y = k;
   ```
   - A fórmula constante do produto garante uma relação inversa entre os dois tokens, modelando a oferta e a demanda do mercado. À medida que uma quantidade de token aumenta (depositada no AMM), a outra deve diminuir (retirada do contrato AMM).
   - Considere um par de negociação ETH /USDC com 100 ETH e 100 USDC no Pool de Liquidez. Para simplificar, isso pressupõe que 1 ETH é 1 USDC.
   ```javascript
     X = 100 ETH
     Y = 100 USDC
     k = 10.000 (100 ETH * 100 USDC)
   ```

   **Por que não podemos trocar 25 ETH por 25 USDC ?**
   - Para determinar se um swap é válido ou não, precisamos pré-calcular como o swap afetaria o produto constante do pool. Será que continua pelo menos o mesmo?
   - Seguindo a formula : `100 ETH * 100 USDC <= (100 ETH + 25 ETH) * (100 USDC - 25 USDC)`
   - Trocar 25 ETH por 25 USDC significaria que depositamos 25 ETH para o AMM, e retirar 25 USDC a partir dele. Isso ajustaria a liquidez do pool para 125 ETH e 75 USDC. O AMM rejeitará essa troca desde que o produto constante da pool diminui pós-troca.
   Porque, seguindo a formula, ficaria : 
   `100 ETH * 100 USDC <= (125 ETH) * (75 USDC)` = `10.000 <= 9.375`
   - O produto constante da pool diminui, portanto, o swap é rejeitado.
   - O produto pós-swap é menor que o produto pré-swap, 10.000, que viola o invariante constante do produto. O gráfico abaixo visualiza essa troca.

   **Determinando o Valor Correto do Swap do USDC**
   - Voltando ao exemplo anterior, adicionando 25 ETH para a pool, aumenta a quantidade de ETH para 125 ETH (100 + 25). A próxima tarefa é encontrar a nova quantidade reduzida de USDC no pool que preserva o produto constante, garantindo assim que o AMM o aceite.
   `??USDC <= 100 USDC - (100 ETH * 100 USDC) / 125 ETH`
   `??USDC <= 100 USDC - (10000 / 125)`
   `??USDC <= 100 USDC - 80`
   `??USDC <= 20`
   - O pool agora tem 125 ETH e 80 USDC, o que equivale a um produto constante de 10.000. `100 * 100 = 10.000` e `125 * 80 = 10.000`.

   **Exemplo 2:**
   - O exemplo abaixo assume um pool ETH/USDT existente que tem uma relação de valor exata de 50:50 de tokens (1 ETH = 2000 USDT).
    1. Piscina de Liquidez: ETH/USDT
    2. Quantidade de ETH da piscina: 100
    3. Quantidade de USDT da piscina: 200.000
    4. Constante: 20.000.000
   -  Observe que, com base na proporção atual de tokens, 1ETH:2000USDT.
   ```markdown
     Um comerciante decide negociar 2 ETH para USDT usando o pool. 
     Podemos calcular o valor equivalente de USDT para o comércio usando o produto constante 20,000,000 calculado acima. 
     Note que x refere-se à quantidade de ETH e y refere-se ao valor USDT.
   ```
   ```markdown
   (x + ⁇ x)(y + ⁇ y) = k

   (100 + 2)(200.000 + ⁇ y) = 20.000.000

   (102)(200.000 + ⁇ y) = 20.000.000

   200,000 + ⁇ y = 20.000.000/102

   ⁇ y = 20,000,000/102-200,000

   ⁇ y = −3.921,57
   ```

   **Código para teste :**
   ```javascript
   function testAMM() public {
        // Initial values
        uint256 k = 20000000; // constant product k
        uint256 initialEth = 100;
        uint256 initialUsdt = 200000;
        uint256 ethToAdd = 2;

        // Calculate new USDT amount after ETH swap
        uint256 newEth = initialEth + ethToAdd; // 102 ETH
        uint256 newUsdt = k / newEth; // 20000000 / 102

        // Calculate the USDT change (which should be negative, indicating USDT should be removed)
        int256 usdtChange = int256(newUsdt) - int256(initialUsdt);

        console.log("Initial ETH: ", initialEth);
        console.log("Initial USDT: ", initialUsdt);
        console.log("ETH added: ", ethToAdd);
        console.log("New USDT amount: ", newUsdt);
        console.log("USDT change: ", usdtChange); // Should be around -3922
    }
   ```
   - Para um swap de 2 ETH, o trader receberá 3.921,57 USDT. Com efeito, o preço médio por ETH é efetivamente 1.960,79 USDT. Observe que o preço final difere do preço inicial do pool antes do swap (ou seja, o preço final do preço inicial do pool. 1 ETH = 2.000 USDT). Essa escala exponencial de preços garante que o 20,000,000 a constante é mantida à medida que o fornecimento de cada token no pool muda.
    1. Quantidade de ETH da pool: 102
    2. Quantidade de USDT da pool: 196.078
    3. Constante: 20.000.000
   - Com base no acima, o preço USDT para a próxima unidade ETH no pool após a troca é 196,078/102 = 1,922.33.
   - Criticamente, as mudanças nos índices de pool devem ser tomadas dentro do contexto mais amplo do mercado global de ETH/USDT. Neste caso, assumindo que o preço de mercado da ETH nos mercados externos permanece em 1 ETH a 2.000 USDT, uma oportunidade de lucro se apresenta para os arbitrageurs que comprarão ETH do pool e o venderão em bolsas externas para embolsar o diferencial de preço (ou seja, comprarão 2 ETH a um preço médio de 1.960,79 e revendê-lo por 2.000, obtendo assim um lucro de 78,42). Esta ação de arbitragem irá trazer o pool de volta em linha com a taxa de mercado. Os arbitradores desempenham um papel crucial no reequilíbrio do pool com o mercado externo.

   **Exemplo 3 :**
   - Considere um pool de liquidez Uniswap v2 formado por dois tokens X e Y. Deixar x e y variáveis que representam a quantidade de tokens X e Y, respectivamente, que estão na piscina em um determinado momento. Como mencionamos anteriormente, os pools de liquidez do Uniswap v2 são baseados em um fórmula constante do produto. Isso significa que a piscina se equilibra x e y satisfazer o seguinte equação:
   $$ x\cdot y={L}^2, $$
   onde L é um número positivo que é chamado parâmetro de liquidez da piscina. Por enquanto, vamos assumir que L é um valor constante. Veremos mais tarde como a liquidez parâmetro L pode mudar.

   - Para ver como a fórmula constante do produto funciona, considere um pool de liquidez Uniswap v2 sem taxas cujas reservas são de 100 ETH e 400.000 USDC. A partir da fórmula constante do produto, obtemos que 100 ⋅ 400.000 = L2. Então L2 = 40,000,000. Suponha que um comerciante quer comprar 20 ETH. Para fazer isso, eles devem depositar uma certa quantidade de USDC no pool. Observe que, se o pool enviar 20 ETH para o comerciante, haverá 80 ETH restantes no pool. A partir da fórmula constante do produto, obtemos o saldo de B (USDC) quando há 80 ETH deixado na pool tem que satisfazer
   $$ 80\cdot B={L}^2=40,000,000. $$
   Assim, B = 5.000.000. Ou seja, se houver 80 ETH na pool, então deve haver 500.000 USDC nela. Portanto, o comerciante terá que depositar um montante de 500.000 – 400.000 = 100.000 USDC para receber 20 ETH. Em outros termos, os saldos do pool antes e depois da negociação devem satisfazer a Equação (Formula Constante), e assim eles podem ser considerados como pontos do plano real que pertencem à curva definida pela Equação.

   - Vamos agora analisar como funciona a fórmula constante do produto no caso geral, a fim de obter várias fórmulas importantes que serão usadas ao longo deste capítulo. Vamos assumir primeiro que o pool de liquidez não tem taxas.
    1. Suponha que um comerciante quer comprar uma quantia a de token X. Para isso, ele deve depositar um valor b de token Y. (Deixar a e b seja os saldos no conjunto de tokens X e Y, respectivamente, imediatamente antes do trade). Então,
     $$ AB={L}^2. $$
    2. Após o trade, o saldo do token X na piscina será A – a, e o saldo do token Y na piscina será B + b. Assim,
    $$ \left(A-a\right)\left(B+b\right)={L}^2. $$
    Então
    $$ {L}^2=\left(A-a\right)\left(B+b\right)= AB+ Ab-aB-ab={L}^2+ Ab-aB-ab $$
    e assim,
    $$ Ab-aB-ab=0. $$
    Portanto, podemos isolar b e obter :
    $$ b=\frac{aB}{A-a}. $$
    $$ a = 20 ETH $$ $$ B = 400.000USDC $$  $$ A = 100ETH $$
    $$ b = \frac{20*400.000}{100-20} $$
    $$ b = 100.000 $$
    Isso significa que, para receber um valor a de token X, o comerciante deve depositar um montante $$ \frac{aB}{A-a} $$ de token Y.
    Portando o trader deve depositar 100.000 USDC para obter 20 ETH.
   - Da mesma forma, podemos isolar a obter :
    $$ a=\frac{Ab}{B+b} $$
    Isso significa que, se o comerciante depositar um valor b de token Y, eles receberão uma quantia $$ \frac{Ab}{B+b} $$ de token X.

   **Calculando mal a troca: Dando ao AMM mais do que deveria**
   - Se você tirar menos, por exemplo, 18 USDC, o AMM ainda aceitaria, uma vez que o produto constante no pool de liquidez aumenta, mas você está em uma perda, uma vez que você não maximizou seu swap.
   `??USDC <= 18 USDC`
   `100 USDC - 18 USDC = 82 USDC (restante no pool)`
   `100 * 100 = 10.000` e `125 * 82 = 10.250`


# Swap Fees (Calculo da Taxa)

   **Uniswap V2 impõe uma taxa de negociação de 0,3% AMM para cada swap. Ao fatorar as taxas, o produto constante do pool de liquidez aumenta com cada swap. Esse crescimento no pool é o principal incentivo para os provedores de liquidez. Somente quando os provedores de liquidez retiram sua liquidez, o produto constante do pool diminui.**
   - Como o Uniswap V2 calcula a taxa de 0,3% é dividindo o token depositado em 2 partes :
    1. Taxa: 0,3%
    2. Quantidade disponível restante para o swap: 99,7%

   - Para um swap de 25 ETH para USDC, o contrato de par vai calcular :
    1. Taxa(0,3%): 0.075 ETH
    2. Valor disponível restante para o swap (99,7%): 24.925 ETH

   - A taxa é imediatamente excluída deste cálculo de swapirates, portanto, o swapper não será creditado com a parte de taxa de 0,3%.
   - O que resta é o valor disponível para o swap que é 24.925 ETH. Este é o valor real pelo qual estamos trocando o USDC do pool.
   - Agora veremos a quantidade máxima de USDC (⁇ USDC) podemos retirar do pool. Adicionando 24.925 ETH para ao pool aumentaria a quantidade de ETH para 124.925 ETH.

   ```javascript
      ethBefore = 100;
      usdc = 100;
      ethAfter = 124.925;
      ⁇USDC = USDC - (ethBefore * usdc) / ethAfter;
      ⁇USDC = USDC - (10000 / 124.925);
      ⁇USDC = 100 USDC - 80.048 USDC;
      ⁇USDC = 19.952 USDC;
   ```
   - Contabilizando a taxa de troca de 0,3%, poderíamos retirar aproximadamente 19.952 USDC da AMM.

# Adicionando e removendo liquidez
   - Para contribuições e retiradas de liquidez, a proporção dos tokens no pool deve ser mantida. Assumindo que temos o mesmo pool ETH/USDT acima :
    1. Piscina de Liquidez: ETH/USDT
    2. Quantidade de ETH da piscina: 102
    3. Quantidade de USDT da piscina: 196.078
    4. Constante: 20.000.000

   - Quaisquer adições ou remoções de liquidez exigirão uma proporção de 1ETH:1,922USDT. Isso garante que o preço do pool não seja afetado por adições ou remoções de liquidez. Consequentemente, observe que a proporção dependerá da proporção exata no pool no ponto de adicionar/remover liquidez.

   - Supondo que um provedor de liquidez queira remover exatamente 2 ETH do pool, a retirada também resultará em 3.844 USDT sendo removidos do pool. Observe que a constante também foi atualizada, mas a relação de preços permanece a mesma (192,234/100=1,922).
    1. Quantidade de ETH da piscina: 100
    2. Quantidade de USDT da piscina: 192.234
    3. Constante: 19.223.400




