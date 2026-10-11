# 📈 Calculadora de Renda Passiva
> Aplicação interativa para projetar cenários de renda passiva considerando patrimônio, rentabilidade, inflação, aportes, retiradas e uma estimativa simplificada de Imposto de Renda.
<p align="left"><a href="https://calculadorafinanceira.streamlit.app/"><img src="https://img.shields.io/badge/Aplicação-Streamlit-FF4B4B?logo=streamlit&logoColor=white" alt="Aplicação Streamlit"></a> <a href="https://www.python.org/"><img src="https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white" alt="Python"></a> <a href="https://github.com/betokoso/Calculadora_Financeira_Doc"><img src="https://img.shields.io/badge/Documentação-GitHub-181717?logo=github" alt="Documentação GitHub"></a></p>
**[▶ Acessar a calculadora](https://calculadorafinanceira.streamlit.app/)** · **[LinkedIn do autor](https://www.linkedin.com/in/norberto-koso-7091a020)**
---
## Visão geral
Planejar renda passiva exige mais do que projetar juros compostos. É necessário avaliar a relação entre patrimônio, rentabilidade, inflação, aportes, retiradas e tributação ao longo do tempo. Uma projeção nominal positiva, isoladamente, não garante ganho de poder de compra.
A **Calculadora de Renda Passiva** transforma essas variáveis em cenários compreensíveis, permitindo observar a evolução patrimonial mensal e verificar se a renda desejada é atingida durante o horizonte definido.
## O problema que a aplicação procura responder
- Tenho um plano de acumulação e retirada coerente com a renda que desejo?
- Em que momento a renda projetada alcança a meta definida?
- O patrimônio cresce acima da inflação ou apenas aumenta nominalmente?
- Como aportes, prazo de acumulação e início das retiradas alteram o cenário?
- O que acontece se o patrimônio ficar insuficiente para sustentar as retiradas?
## Da análise à solução
O objetivo não foi simplesmente escolher uma biblioteca e começar a programar. A proposta parte da estruturação do problema, da identificação das variáveis relevantes e da definição de regras de negócio. Essas regras são traduzidas em uma simulação mensal, separando interface, lógica de cálculo e obtenção de indicadores econômicos.
Essa abordagem combina análise de problema, modelagem, tratamento de dados e comunicação de resultados — competências relevantes em análise de dados, automação e transformação digital.
## Funcionalidades
- **Projeção patrimonial mensal:** acompanha o patrimônio durante o horizonte selecionado.
- **Meta de renda passiva:** compara a renda projetada com a meta definida e corrigida pela inflação.
- **Inflação e poder de compra:** oferece uma referência para avaliar o crescimento nominal e real.
- **Parâmetros de investimento:** simula rentabilidade pré-fixada, percentual do CDI ou IPCA+ conforme as opções implementadas.
- **Aportes configuráveis:** define valor mensal, correção pela inflação e idade limite para contribuir.
- **Retiradas configuráveis:** permite iniciar desde o início ou após o último aporte, por percentual do patrimônio ou valor definido, com opções de correção.
- **Estimativa de Imposto de Renda:** considera uma aproximação tributária quando habilitada.
- **Visualizações:** indicadores, gráficos e tabelas para facilitar a interpretação dos resultados.
- **Insuficiência patrimonial:** a implementação limita a retirada ao saldo disponível e pode interromper a projeção quando os recursos ficam insuficientes.
## Tecnologias
| Tecnologia | Aplicação no projeto |
|---|---|
| Python | Regras de negócio e cálculos. |
| Streamlit | Interface interativa e publicação web. |
| Pandas | Organização e apresentação dos resultados. |
| NumPy | Operações numéricas. |
| Plotly | Gráficos interativos. |
| API do Banco Central do Brasil | Consulta de indicadores econômicos utilizados como referência. |
## Como utilizar
1. Acesse a [aplicação publicada](https://calculadorafinanceira.streamlit.app/).
2. Informe patrimônio atual, renda desejada e idades da simulação.
3. Confira os indicadores econômicos e ajuste as premissas.
4. Escolha o tipo de investimento e configure rentabilidade, aportes, retiradas e incidência de imposto.
5. Execute a simulação e analise os indicadores, gráficos e evolução patrimonial mensal.
## Documentação complementar
- [Metodologia de cálculo](docs/metodologia.md)
- [Premissas, simplificações e limitações](docs/limitacoes-do-modelo.md)
## Sobre o projeto
Projeto independente desenvolvido por **Norberto Koso**, com foco em transformar uma questão prática de planejamento financeiro em uma aplicação interativa baseada em regras de negócio, cálculos e visualização de dados.
- **Aplicação:** https://calculadorafinanceira.streamlit.app/
- **LinkedIn:** https://www.linkedin.com/in/norberto-koso-7091a020
- **Repositório de documentação:** https://github.com/betokoso/Calculadora_Financeira_Doc
## Aviso
Esta aplicação é uma ferramenta de simulação e exploração de cenários. Não constitui recomendação de investimento, consultoria financeira, garantia de rentabilidade ou cálculo oficial de tributos. Os resultados dependem das premissas informadas e devem ser interpretados considerando as limitações do modelo.
