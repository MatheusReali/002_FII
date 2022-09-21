# ANÁLISE DE FUNDOS IMOBILIÁRIOS (FII)

Este projeto tem por objetivo criar uma ferramenta para analisar os diversos fundos imobiliários listados no mercado brasileiro a fim de encontrar boas opções de compra para investidores que buscam rendimentos mensais consistentes.

OBSERVAÇÃO IMPORTANTE: esta ferramenta *NÃO É UMA RECOMENDAÇÃO DE INVESTIMENTO*. O usuário deve realizar sua própria análise e tirar suas próprias conclusões. A ferramenta foi criada para uso exclusivo do autor e o mesmo não se responsabiliza por qualquer ação tomada pelo usuário.

Link para a apresentação no Tableau Public: https://public.tableau.com/app/profile/matheus3596/viz/001_FII_1/Apresentao?publish=yes

## MOTIVAÇÕES

Fundos imobiliários são uma forma simples de se investir no mercado imobiliário sem a necessidade de comprar imóveis físicos. Com a compra de uma cota de um fundo imobiliário, o investidor se torna dono de uma parcela dos imóveis do fundo e, com isso, passa a receber rendimentos desses imóveis proporcionalmente à sua cota.

O investimento em fundos imobiliários é ótima maneira de se conseguir uma renda passiva mensal.

Entretanto, analisar um fundo imobiliário não é algo tão simples para quem está começando a investir. Existem diversos fundos e vários indicadores para serem analisados.

Entre tantos fundos, fica difícil encontrar os que são seguros e rentáveis olhando um a um.

## INTRODUÇÃO

### A ferramenta segue os seguintes passos:
  - Conectar e realizar a extração de dados de fundos imobiliários através de webscrapping;
  - Realizar filtros nos dados com a biblioteca Pandas do Python;
  - Exportar tabelas para arquivos Comma Separated Values (csv);
  - Importar as tabelas para a ferramenta de visualização Tableau e criar gráficos.
 
### Tecnologias:
  - Python;
  - Tableau;
  - MySQL.
  
### Bibliotecas do Python:
  - Pandas;
  - Selenium;
  - Webdriver-manager;
  - Requests;
  - re (regular expression);
  - Numpy;
  - dotenv;
  - matplotlib;
  - Beautiful Soup.

## COMO USAR

Para acessar a apresentação atualizada periodicamente, acesse o link do Tableau Public https://public.tableau.com/app/profile/matheus3596/viz/001_FII_1/Apresentao?publish=yes

### 1. Instalando extensões e importando bibliotecas

Será necessário instalar algumas extensões do Python caso elas não estiverem instaladas.

![image](https://user-images.githubusercontent.com/95454600/189501922-d7e53459-3061-4d59-b30b-80dbb602a43f.png)

Também deve-se importar as bibliotecas que serão utilizadas.

![image](https://user-images.githubusercontent.com/95454600/189501947-845d2b2a-5ee9-48ea-8ddb-f08befb65e0d.png)

### 2. Webscrapping dos dados

Os dados serão extraídos do site https://www.fundamentus.com.br/fii_resultado.php através de webscrapping com selenium (vide notebook para detalhes da extração).

*Observação: a extração pode demorar alguns minutos (entre 2 e 5 minutos).*

![image](https://user-images.githubusercontent.com/95454600/189502024-ab9f3988-f1c2-4acf-89b3-2b870e7d614c.png)

Uma tabela com mais de 300 (trezentos) fundos será extraída classificada em ordem alfabética por ticker do fundo.

Nesta etapa já podemos exportar essa tabela completa para um arquivo csv chamado 'fii_completa.csv' dentro de uma pasta chamada 'data'.

### 3. Usando Pandas para filtrar os dados

Com a tabela commpleta em mãos, pode-se começar a filtrá-la para limpar os dados considerados dispensáveis para esta análise.

Para auxiliar na análise, será utilizado o método .describe() para criar uma tabela com as estatísticas de cada coluna dos dados que serão analisados.

#### POR DIVIDEND YIELD

O termo Dividend Yield (DY) significa, em portugês, "rendimento de dividendos" e é um dos principais indicadores de um fundo imobiliário.

Esse indicador representa a proporção entre rendimentos distribuídos e o preço das ações de um fundo.

Em síntese, busca-se um DY alto, mas valores muito altos podem representar eventos excepcionais.

Dessa forma, será a utilizada a tabela estatística para selecionar os fundos que possuem DY acima do terceiro quartil (75%) e abaixo do terceiro quartil mais uma vez e meia o InterQuartile Range (< 75% + 1,5*IQR).

![image](https://user-images.githubusercontent.com/95454600/189502728-1e9b00a5-47d6-48db-8bd4-d537c0c75bbb.png)

#### POR PREÇO / VALOR PATRIMONIAL (P/VP)

O preço da cota dividido pelo valor patrimonial por cota do fundo é um indicador que representa por quanto uma cota está sendo comercializada no mercado em relação ao valor patrimmonial desse fundo.

Em suma, esse indicador nos mostra se a cota está 'cara' ou 'barata'.

Um p/vp acima de 1 (um) significa que a cota está sendo comercializada acima do seu valor patrimonial (está cara), enquanto que um p/vp abaixo de 1 (um) significa que a cota está sendo comercializada abaixo do seu valor patrimonial (está barata).

Nesse caso, prefere-se um p/vp mais baixo, esperando que o fundo se valorize e gere um rendimento maior quando vendido.

Entretanto, valores de p/vp muito baixos podem representar que o fundo está passando por um período difícil ou está sendo mal administrado.

Com isso, vamos filtrar por fundos que tenham um p/vp entre o segundo e terceiro quartil (50% < x < 75%). Tomando o cuidado, no final, de não escolher fundos com p/vp abaixo de, aproximadamente, 0,90 e acima de, aproximadamente, 1,20. 

![image](https://user-images.githubusercontent.com/95454600/189503095-e1e31a80-d90f-4d22-8317-0df1961f6b95.png)

Pela imagem é possível perceber que os dados possuem muitos valores discrepantes que puderam ser filtrados.

#### POR VALOR DE MERCADO E LIQUIDEZ

O valor de mercado de um fundo é o número que representa o valor patrimonial desse fundo. Nessa análise vamos buscar por valores de mercado alto, pois geralmente são fundos grandes, mais diversificados, bem administrados e, consequentemente, menos suscetíveis à variações de mercado.

Já a liquidez, representa o número médio de cotas que são comercializadas por dia. Quanto mais alto esse valor, mais fácil de comprar e vender ações do fundo. Alta liquidez é interessante para investidores que desejam vender ações de forma mais ágil.

Serão selecionados fundos com valor de mercado acima do segundo quartil (>50%) e com liquidez acima do terceiro quartil (>75%).

![image](https://user-images.githubusercontent.com/95454600/189503426-a7a91e39-7155-436c-a85b-ebdcc2258c02.png)

#### POR TAXA DE VACÂNCIA

A taxa de vacância representa a quantidade média de metros quadrados dos imóveis do fundo que estão vagos ou que não estão gerando rendimentos.

Nesta análise serão selecionados apenas fundos que possuem taxa de vacância igual à zero, *mas, vale ressaltar, que esse tipo de análise é bastante vaga*. O ideal seria analisar essa taxa de acordo com os vários tipos de fundo (se são fundos de fundos, de tijolo etc.), com o número de imóveis do fundo, localização etc. Como não temos como extrair essas informações dos dados que possuímos, essa análise fica a cargo do investidor e como ideia a ser implantada em uma futura versão desta ferramenta.

### 4. Classificando e visualizando os dados

Por fim, os fundos que restaram dos filtros acima serão classificados de acordo com sua taxa de rendimento (DY), da maior para a menor.

Para os cinco primeiros fundos dessa tabela, será feito um webscrapping dos rendimentos distribuídos ao longo do tempo (dados extraídos do site https://www.fundamentus.com.br/fii_resultado.php) utilizando selenium e webdriver-manager.

Esses dados serão exportados para arquivos csv, importados para o tableau e os gráficos desses rendimentos serão analizados ao longo dos últimos 12 meses para ter noção da consistência do pagamento desses dividendos ao longo do tempo.

![image](https://user-images.githubusercontent.com/95454600/189503823-50a0df7a-5299-45d8-abf9-81fa06ac91d4.png)

## IDEIAS PARA FUTURAS VERSÕES
  
  - Análisar o número e a localização dos imóveis dos FIIs;
  - Criar filtros interativos para o usuário;
  - Criar visualizações comparativas entre os fundos;
  - Buscar mais dados históricos, como preço da cota ao longo do tempo, taxa de juros e IPCA para efeitos comparativos.

## CONCLUSÃO

Apesar de simples, a análise reduz consideravelmente o número de fundos listados, trazendo alguns fundos consistentes e confiáveis.

A ferramenta não dispensa que o usuário possua algum conhecimento em fundos imobiliários para poder investir, mas traz ótimas opções para investidores iniciantes e que buscam opções mais seguras e estáveis.

Por fim, deve-se salientar novamente que, apesar de estar em um repositório público, a ferramenta *NÃO É UMA RECOMENDAÇÃO DE INVESTIMENTO* e o autor não se responsabiliza pela forma como é utilizada.

## LINKS E REFERÊNCIAS

 - Tableau Public: https://public.tableau.com/app/profile/matheus3596/viz/001_FII_1/Apresentao?publish=yes
 - Fundamentus: https://www.fundamentus.com.br/fii_resultado.php
 - EXAME "Fundos imobiliários: o que são e como funcionam?": https://exame.com/invest/guia/fundos-imobiliarios-o-que-sao-e-como-funcionam/
 - FundsExplorer: https://www.fundsexplorer.com.br/artigos/principais-indicadores-na-hora-de-escolher-um-fii-o-que-considerar/
 - O Primo Rico: https://www.youtube.com/watch?v=IazEN13o304&list=LL&index=1&t=820s

## VERISONAMENTO

Versão 1.0

## AUTOR

Matheus Reali de Oliveira Fabricio (https://github.com/MatheusReali/MatheusReali)
