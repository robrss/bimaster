# Previsão de série temporal de Reservas Provadas de Óleo e Gás

#### Aluno: [Robson Silva](https://github.com/link_do_github)
#### Orientador: [Leonardo Mendonça](https://github.com/link_do_github).

---

Trabalho apresentado ao curso [BI MASTER](https://ica.puc-rio.ai/bi-master) como pré-requisito para conclusão de curso e obtenção de 
crédito na disciplina "Projetos de Sistemas Inteligentes de Apoio à Decisão".

<!-- para os links a seguir, caso os arquivos estejam no mesmo repositório que este README, não há necessidade de incluir o link completo: basta incluir o nome do arquivo, com extensão, que o GitHub completa o link corretamente -->
- [Link para o código](trabalho-final_v1.ipynb). 

---

### Resumo

O presente trabalho tem como objetivo relizar uma análise comparativa de diferentes tipos de modelos preditivos de séries temporais. 
Para a realização dessa análise comparativa, foram utilizados dados de reservas de Óleo e Gás disponibilizados pela OGA (Oil and Gas
Authority) do Reino Unido.

### Abstract 

The present work aims to present a comparative analysis of different types of time series predictive models.
To carry out this comparative analysis, data from Oil and Gas reserves made available by OGA (Oil and Gas Authority) of the United 
Kingdom were used.

### 1. Introdução

Foram escolhidos modelos LSTM e Prophet para a realização desse estudo uma vez que durante a etapa de análise exploratória dnão foram 
encontradas fortes evidências que a séries utilizadas não possuem média e desvio padrão estacionárias, critério necessário para modelos 
do tipo ARIMA. 

LSTM e Prophet não dependem que os dados sejam estacionários além de possuírem suporte nativo a previsão de séries multivariadas. 
A pesar de ser possível utilizar técnicas de manipulação de dados para tentar 
torná-los estacionários e assim viablizar a utilização de modelos do tipo ARIMA, esse tipo de modelagem não foi considerada no escopo 
desse trabalho uma vez que temos mais uma opção de modelo que não tem esse pré-requisito para ser utilizado diretamente.

Para corroborar com a análise exploratória dos dados, foi utilizado o método Augmented Dickey-Fuller (ADF) para a realização do teste de 
hipótese na série objetivo, a série na qual se tentará prever o seu comportamento: a série temporal de reservas provadas (1P_OIL).

A Hipótese nula (H0) foi definida como: a série temporal não é estacionária. Ao executar o método ADF sobre a série 1P_OIL, o p-value 
obtido foi de 0.421939 > 0.05 (nível de significância) não sendo assim possível rejeitarmos a hipótese nula.

Nos dados de entrada, é possível verificar (utilizando o método de Pearson), que os maiores níveis de correalação, se manifestam de
forma relativamente ituitiva, como por exemplo: (i) investimentos (TOTAL_CAPEX) e receita (INCOME) e (ii) investimentos (TOTAL_CAPEX) 
e total de gastos (TOTAL_EXPENSES).


### 2. Modelagem

Foram obtidos dados públicos anualizados relacionados ao processo de estimativa de reservas, disponiblizadado pela Oil and Gas Authority (OGA) 
do Reino Unido.

Dentre os dados disponibilizados pela OGA foram escolhidas as séries temporais de: Reservas Provadas (comumente referênciada 
pela sigla 1P na indútria de Óleo e Gás), produção de óleo (production), receitas (income), despesas (expenses) e custos de 
investimentos (capex). 

Uma vez escolhidas as séries temporais, foi montada uma base de dados de treino e teste no qual as séries possuem os seguintes 
identificadores: 1P_OIL (reserva provada),	OIL_PROD (produção de óleo),INCOME (receitas), TOTAL_EXPENSES (despesas) e 
TOTAL_CAPEX (custos de investimento).

Dentre as séries dessa base de dados, foi definida como série objetivo (a qual os modelos realizarão previsões) a série temporal 
das reservas provadas (1P_OIL). Para tal, foram definidas algumas estratégias e configurações dintintas com o intuito de se avaliar 
qual modelo terá melhor capacidade preditiva. 

Conforme exposto anteriormente, foram escolhidos modelos que não pressupõem estacionarieadade dos dados temporais: LSTM e Prophet. 
Ambos os modelos foram treinados com a base de dados na visão univariada e multivariada combinada com divesas configurações distindas 
dos modelos.

Para o modelo LSTM foram definidas 6 topologias de rede distintas. Para cada uma dessas redes, houve o treinamento 
considerando os dados de entrada univariado (1P_OIL) e multivariado (1P_OIL, OIL_PROD, INCOME, TOTAL_EXPENSES e TOTAL_CAPEX) 
com janelas de tempo t-2 e t-1. A janela de tempo é feita através de um pré-processamento do dataset de entrada, no qual o 
valor da série no tempo t-2 e t-1 é utilizado como dado de treino para a previsão do valor no tempo t. Para o treino utilizando 
o conjunto de dados univariado, ou seja, considerando somente a série que se deseja prever como entrada, foi considerada a janela de 
tempo t-3.

Como modelo Prophet segue uma abordagem diferente do LTSM, não houve a manipulação do dataset de entrada para a 
inclusão dos valores no tempo t-2 e t-1 para a previsão do valor da série no tempo t. Entretando, foram utilizados 
os dados de forma univariada e multivariada para os treinamentos dos modelos.


### 3. Resultados

Para o modelo LSTM dentre as 6 topologias de rede os melhores resultados foram:

 - Dados de treino multivariado, janela de tempo t-2: network_v4 com MAE=32.05 e RMSE:37.87;
 - Dados de treino multivariado, janela de tempo t-1: network_v2 com MAE=22.92 e RMSE:26.13;
 - Dados de treino univariado, janela de tempo: t-1: network_v2 com MAE=26.29 e RMSE:28.78;
 - LDados de treino univariado, janela de tempo: t-2:: network_v2 com MAE=35.84 e RMSE:39.65;

Para o modelo Prophet, temos:
 - Dados de treino multivariado: MAE=455.33 e RMSE:557.56;
 - Dados de treino univariado: MAE=169.95 e RMSE:183.35;


Entre os modelos LSTM e Prophet, o LSTM apresentou melhores resultados em todas as configurações. Dentre as 
cofigurações utilizadas para o LSTM a que  apresentou melhores resultados preditivos, considerando as métricas 
de MAE e RMSE, a utilização de univariado ou multivariado e diferentes janelas de  tempo, foi a configuração 
denominada por network_v2. 

A configuração network_v2,  possui uma camada LSTM de entrada com 20 neurônios, uma camada intermediária de uma rede 
neural densamente conectada com  5 neurônios e uma camada de saída com 1 neurônio. O tamanho do conjunto de treino e 
teste, função de perda, otimizador, números de épocas e batch size  são os mesmos para todas as configurações de rede testadas.


### 4. Conclusões

Nesse trabalho foi apresentado uma análise comparativa de diferentes tipos de modelos preditivos de séries temporais. 

O conjunto de dados escolhido foi um conjunto de séries temporais disponibilizados pela OGA (Oil and Gas Authority)
do Reino Unido. Dentre as séries disponibilidas foi utilizada a de reservas provadas para a avaliação da capacidade
preditiva dos modelos analisados.

Durante o processo de análise exploratória dos dados, foi identificado que as séries utilizadas não tinham a propriedade
de estacionariedade. Por esse motivo, a pesar de que em algumas situações ser possível a manipulação dos dados para 
busca uma aproximação dessa propriedade, foram descartados os modelos do tipo ARIMA do escopo deste trabalho, uma vez 
que há modelos que não dependem que os dados sejam estacionários, além de um melhor suporte para a previsão utilizado
séries multivariadas: LSTM e Prophet.

O modelo LSTM apresentou os melhores resultados em todas as situações analisadas. Adicionalmente, dentro das configurações
do modelo LSTM avaliadas foi possível notar que mesmo havendo mudanças consideráveis nas entradas dos dados o modelo LSTM
com a configuração network_v2 foi capaz de manter robustez na sua capacidade preditiva. 

### Referências 
 - [Reserves and resources](https://www.ogauthority.co.uk/data-centre/data-downloads-and-publications/reserves-and-resources/).

 - [Production and expenditure projections](https://www.ogauthority.co.uk/data-centre/data-downloads-and-publications/production-projections/).
 
 - [UKCS income and expenditure](https://www.ogauthority.co.uk/data-centre/data-downloads-and-publications/ukcs-income-and-expenditure/).
 
 - [How to Convert a Time Series to a Supervised Learning Problem in Python](https://machinelearningmastery.com/convert-time-series-supervised-learning-problem-python/).

 - [Time Series Analysis A Complete Guide](https://www.kaggle.com/andreshg/timeseries-analysis-a-complete-guide/).

---

Matrícula: 192.671.126

Pontifícia Universidade Católica do Rio de Janeiro

Curso de Pós Graduação *Business Intelligence Master*
