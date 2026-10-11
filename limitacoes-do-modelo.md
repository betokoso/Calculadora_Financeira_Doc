# Premissas, simplificações e limitações

Esta aplicação foi concebida para explorar cenários de renda passiva. Os resultados dependem dos parâmetros informados e não constituem previsões ou garantias.

## Rentabilidade e mercado
As taxas configuradas são mantidas como premissas. O modelo não prevê volatilidade, sequência real de retornos, mudanças futuras da Selic/CDI, risco de crédito ou alterações de mercado. Para percentual do CDI, utiliza-se uma aproximação baseada na Selic, embora sejam taxas diferentes.

## Inflação e poder de compra
A projeção utiliza uma taxa de inflação informada, que pode divergir da inflação futura e da inflação pessoal de cada investidor. Os indicadores reais devem ser interpretados de acordo com as fórmulas implementadas.

## Imposto de Renda
O imposto é aproximado por ciclos de duração média. O modelo não acompanha individualmente cada aporte, data de aquisição, prazo de permanência, resgate parcial, custo de aquisição ou regra específica de cada ativo. A alíquota aproximada pode diferir da tributação real e não deve ser usada para apuração fiscal.

## Custos e características dos ativos
A aplicação não representa individualmente todos os custos, como taxas de administração, custódia, corretagem, spreads, nem eventos de crédito, liquidez ou resgate antecipado.

## Aportes e retiradas
O comportamento real pode mudar por emergências, alterações de renda e decisões pessoais. A simulação aplica as regras escolhidas na interface e limita retiradas conforme o saldo disponível e as regras de encerramento implementadas.

## Indicadores via API
Os indicadores econômicos podem ficar indisponíveis temporariamente. Confira os valores apresentados antes de executar uma simulação.

## Melhorias futuras possíveis
- Tributação por ativo e por lote de aquisição.
- Taxas variáveis e cenários de estresse.
- Custos e características específicas de diferentes classes de ativos.
- Testes automatizados para cenários de referência e casos-limite.

A aplicação não substitui aconselhamento financeiro ou tributário profissional.
