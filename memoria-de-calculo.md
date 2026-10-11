# Memória de cálculo e rastreabilidade

Este documento descreve a lógica implementada na versão documentada da Calculadora de Renda Passiva. O código-fonte permanece em repositório privado; esta especificação permite entender as premissas, acompanhar as dependências entre cálculos e conferir os resultados sem publicar a implementação.

> **Importante:** as fórmulas abaixo descrevem o comportamento do modelo, incluindo suas aproximações. Elas não representam uma metodologia tributária oficial nem uma previsão de rentabilidade.

## 1. Fluxo geral da simulação

A função de simulação percorre o horizonte em meses. Em cada mês, a aplicação:

1. identifica ano, mês e idade de referência;
2. atualiza os valores que recebem correção anual ou mensal;
3. determina o aporte e a retirada desejada;
4. calcula inflação de referência e rendimento do patrimônio inicial;
5. limita a retirada ao saldo disponível e calcula eventual retirada não atendida;
6. calcula o patrimônio antes do IR e, quando aplicável, estima o imposto no encerramento do ciclo;
7. calcula indicadores de renda e variação de poder de compra;
8. grava a linha do mês e usa o patrimônio final como patrimônio inicial do mês seguinte;
9. encerra a simulação se o patrimônio se esgotar ou se uma retirada não puder ser integralmente atendida.

A ordem é relevante: por exemplo, o rendimento é calculado sobre o patrimônio inicial do mês; o aporte entra antes do limite de retirada; e o IR é descontado quando o ciclo tributário aproximado é encerrado.

## 2. Taxas e premissas

### Rentabilidade anual

A taxa nominal anual é determinada pelo tipo de investimento:

- **Pré-fixado:** taxa informada pelo usuário.
- **Percentual do CDI:** Selic anual multiplicada pelo percentual do CDI informado. É uma aproximação, pois CDI e Selic não são idênticos.
- **IPCA+:** composição da inflação anual com a taxa adicional informada: `(1 + inflação anual) × (1 + taxa adicional) − 1`.

A taxa mensal equivalente é calculada por capitalização composta:

`r_m = (1 + r_a)^(1/12) - 1`

A inflação mensal equivalente segue a mesma lógica:

`i_m = (1 + i_a)^(1/12) - 1`

Onde `r_a` é a rentabilidade anual em formato decimal e `i_a` é a inflação anual em formato decimal.

### Horizonte

O número de meses é calculado a partir da diferença entre a idade final e a idade atual, multiplicada por 12. O ano e o mês da simulação são contadores do horizonte, começando em ano 1, mês 1.

## 3. Cálculo mensal: colunas da tabela

| Coluna exibida | Regra implementada e interpretação |
|---|---|
| **Ano / Mês** | Posição do período dentro do horizonte simulado. O primeiro registro é ano 1, mês 1. |
| **Patrimônio Inicial** | Saldo disponível no começo do mês. No primeiro mês, corresponde ao patrimônio informado; nos seguintes, corresponde ao patrimônio final do mês anterior. |
| **Patrimônio Inflação** | Referência hipotética iniciada no patrimônio inicial e corrigida pela inflação mensal equivalente a partir do segundo mês. É usada como linha de comparação no gráfico patrimonial; não é o saldo efetivo investido. |
| **Rendimento** | Patrimônio inicial multiplicado pela taxa mensal equivalente de rentabilidade: `patrimônio inicial × r_m`. |
| **Inflação** | Valor de referência negativo calculado como `− patrimônio inicial × i_m`. É utilizado no indicador de variação do poder de compra; não é uma saída de caixa do patrimônio efetivo. |
| **Aporte** | Valor mensal configurado, se aportes estiverem habilitados e a idade de referência do mês for menor que a idade-limite dos aportes. Se a correção anual estiver habilitada, o aporte é atualizado pela inflação anual no primeiro mês de cada ano posterior ao primeiro. |
| **Retirada** | Valor efetivamente retirado, armazenado como número negativo. A retirada pode ser percentual ou valor fixo. Quando percentual, o percentual anual informado é convertido em taxa mensal equivalente e aplicado ao patrimônio inicial do mês. Quando fixa, pode ser corrigida mensalmente, anualmente ou não ser corrigida. |
| **Retirada Não Atendida** | Diferença positiva entre a retirada desejada e a retirada efetivamente possível. O saldo disponível para retirada é limitado a zero no mínimo e considera patrimônio inicial, rendimento do mês e aporte. |
| **IR** | Imposto estimado no encerramento de um ciclo tributário ou no último mês da simulação. É armazenado como valor negativo. Fora desses momentos, a coluna pode ser zero mesmo com IR habilitado. |
| **Patrimônio Final** | Saldo após rendimento, aporte, retirada efetiva e eventual IR do período. O valor é limitado a zero se o cálculo resultar negativo. Esse saldo alimenta o mês seguinte. |
| **Renda Atual** | No código, é calculada como rendimento + aporte + retirada efetiva. Portanto, não representa exclusivamente renda passiva: pode incluir aportes e o efeito negativo de retiradas. O IR não entra diretamente nessa coluna. |
| **Renda Desejada** | Meta mensal informada, corrigida uma vez por ano pela inflação anual, no primeiro mês de cada ano posterior ao primeiro. |
| **Delta Renda** | Diferença entre rendimento + aporte + retirada efetiva e a renda desejada corrigida. Valor igual ou maior que zero marca a meta como atingida. Como segue a fórmula de Renda Atual, também reflete aportes e retiradas. |
| **Meta Atingida** | “Sim” quando o Delta Renda é maior ou igual a zero; caso contrário, “Não”. O primeiro “Sim” é usado para identificar a primeira ocorrência de atingimento da meta. |
| **Var. Poder Compra** | Soma do rendimento, aporte, retirada efetiva e valor negativo de inflação calculado sobre o patrimônio inicial. É um indicador simplificado; não equivale exatamente à variação real do patrimônio final após IR. |

### Patrimônio antes e depois do IR

O saldo antes do imposto é:

`patrimônio antes do IR = patrimônio inicial + rendimento + aporte + retirada`

A retirada já possui sinal negativo. Quando o IR é aplicado, o saldo final é:

`patrimônio final = patrimônio antes do IR + IR`

Como o IR é armazenado como valor negativo, essa soma reduz o patrimônio.

## 4. Regras de aportes e retiradas

### Aportes
- O aporte só ocorre se a opção estiver habilitada e a idade de referência do mês for menor que a idade-limite definida.
- A correção anual, quando habilitada, ocorre antes do aporte do primeiro mês de cada novo ano.
- O total aportado exibido no resumo é a soma da coluna **Aporte**.

### Retiradas
- **Desde o início:** a retirada pode ocorrer desde o primeiro mês.
- **Após o último aporte:** se aportes estiverem habilitados, as retiradas começam quando a idade de referência alcança a idade-limite dos aportes. Sem aportes habilitados, essa opção permite retirada desde o início.
- **Por percentual:** o percentual anual é convertido em taxa mensal equivalente e aplicado ao patrimônio inicial do mês.
- **Por valor:** usa o valor mensal definido, com correção mensal, anual ou sem correção, conforme a seleção.
- A retirada efetiva nunca excede o saldo disponível calculado pelo modelo. A diferença entre a retirada desejada e a efetiva é registrada em **Retirada Não Atendida**.
- A simulação para quando a retirada não pode ser totalmente atendida ou quando o patrimônio final fica próximo de zero, usando a tolerância implementada de R$ 0,005.

## 5. Estimativa de Imposto de Renda

A tributação é simplificada e baseada em ciclos com duração aproximada definida pelo usuário. A duração em anos é convertida em quantidade de meses. A alíquota estimada é selecionada por faixas de duração:

- até 180/365 de ano: 22,5%;
- até 360/365 de ano: 20%;
- até 720/365 de ano: 17,5%;
- acima disso: 15%.

No encerramento do ciclo, o resultado tributável aproximado é calculado como:

`resultado tributável = patrimônio antes do IR − patrimônio no início do ciclo − aportes do ciclo + retiradas do ciclo`

O imposto é calculado somente sobre resultado positivo e registrado como valor negativo. O modelo limita o imposto para que ele não torne o patrimônio negativo. Ao concluir um ciclo completo, o patrimônio após IR inicia o ciclo seguinte e os acumuladores de aportes e retiradas são reiniciados.

**Limitação importante:** essa abordagem não acompanha cada aplicação, data de aquisição, prazo individual, lote, resgate ou regime tributário específico. É uma aproximação para simulação, não uma apuração oficial de IR.

## 6. Indicadores do resumo e gráficos

Os principais cartões do resumo são derivados da tabela mensal:

- **Patrimônio Inicial:** valor informado pelo usuário.
- **Patrimônio Final:** último valor da coluna Patrimônio Final, seja no fim do horizonte ou no mês de esgotamento.
- **Total Rendimentos Brutos:** soma da coluna Rendimento.
- **Total de Impostos:** soma da coluna IR exibida como valor positivo.
- **Total Aportado:** soma da coluna Aporte.
- **Total de Retiradas:** soma dos valores absolutos da coluna Retirada.
- **Variação do Poder de Compra:** último valor da coluna Var. Poder Compra.
- **Status da Meta:** primeira ocorrência de Meta Atingida = “Sim”; se não houver, a meta não foi atingida no período.
- **Delta Renda:** valor da coluna Delta Renda no último mês simulado.

Os gráficos usam a mesma base mensal. A renda atual é comparada com a renda desejada; o percentual de atingimento é calculado mês a mês e depois tem sua média calculada por ano; o patrimônio simulado é comparado com a referência corrigida pela inflação; e a linha de variação do poder de compra utiliza a coluna correspondente.

## 7. Indicadores econômicos da API

O módulo de integração consulta a API de Séries Temporais do Banco Central do Brasil para obter o último valor disponível das séries configuradas para Selic e IPCA acumulado em 12 meses. A aplicação registra a data retornada e mantém os valores em cache por uma hora. Se a consulta falhar, o módulo de interface usa zero como valor inicial e não exibe a indicação de fonte; a simulação exige valores positivos de Selic e inflação.

## 8. Como conferir os cálculos

Para validar uma execução, recomenda-se conferir pelo menos estes vínculos:

1. O patrimônio inicial do mês seguinte deve ser igual ao patrimônio final do mês anterior.
2. O patrimônio antes do IR deve corresponder ao patrimônio inicial + rendimento + aporte + retirada efetiva.
3. Quando há IR, o patrimônio final deve corresponder ao patrimônio antes do IR + IR.
4. A retirada efetiva não deve superar o saldo disponível calculado, e a diferença deve aparecer em Retirada Não Atendida.
5. A meta deve ser “Sim” exatamente quando o Delta Renda calculado for maior ou igual a zero.
6. O total aportado, as retiradas e o IR do resumo devem corresponder às somas das respectivas colunas, respeitando a apresentação de sinais.

Essas verificações conferem a consistência das relações implementadas. Para comprovação mais forte, devem ser acompanhadas por cenários de teste com resultados esperados calculados independentemente.

## 9. Limitações de interpretação

- A rentabilidade futura é constante no cenário escolhido; não há simulação de volatilidade ou sequência de retornos.
- A relação entre CDI e Selic é aproximada.
- O indicador **Renda Atual** inclui aporte e retirada, não apenas rendimento disponível para consumo.
- **Var. Poder Compra** é uma aproximação baseada em rendimento, aportes, retiradas e inflação sobre o patrimônio inicial; não é uma medida exata do ganho real líquido após IR.
- O IR é estimado por ciclos e não por aplicação ou lote.
- Os resultados dependem das premissas informadas e não garantem rentabilidade ou sustentabilidade futura.

Consulte também [Metodologia de cálculo](metodologia.md) e [Premissas, simplificações e limitações](limitacoes-do-modelo.md).
