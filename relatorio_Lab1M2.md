---
title: "Lab1 - Milestone 2"
author: "Bruno Dias"
date: "March 17, 2016"
output: html_document
---


Uso das cotas parlamentares
---------------------------------------------------------------------------------------------------
Para esta analise, o objetivo foi encontrar os deputados que ultrapassam suas cotas parlamentares. Existe um limite mensal por deputado que é definido pela Camara dos Deputados. Podemos encontrar essa informação [aqui](http://www2.camara.leg.br/comunicacao/assessoria-de-imprensa/cota-parlamentar)

```{r echo=TRUE, message=FALSE}
library("dplyr")
library("ggplot2")
```

Primeiro para aprendizagem do R, procurei fazer um levantamento de **quais as empresas que mais prestaram serviços** para os deputados. 

```{r}
dadosParlamentares <- read.csv("/Users/bruno/Documents/Mestrado/fpcc2/AnoAtual.csv")

empresasTop10 <- dadosParlamentares %>% 
  group_by(txtBeneficiario) %>% 
  summarise(total=n()) %>% 
  arrange(desc(total)) %>% 
  slice(c(1:10))

ggplot(empresasTop10, aes(x=txtBeneficiario, y=total)) + 
  geom_bar( size=.2, stat="identity", width = 0.5) + 
  xlab("Qtd de Serviços prestados") + 
  ylab("Empresas") + 
  coord_flip()
```

Como podemos perceber, dentre as 10 empresas que mais prestaram serviços para os deputados, três são companhias aéreas e três são dos correios. Essa informação sugere que a maior parte dos gastos dos parlamentares foram com viagens e trânsito de encomendas. 

Agora vamos procurar os tipos de despesas que os parlamentares mais gastam 
``` {r}
dadosParlamentares %>% group_by(txtDescricao, vlrDocumento) 
```





Qual foi o gasto mensal por deputado? 

gastosDeputados <- AnoAtual %>% select(txNomeParlamentar, sgUF, sgPartido, txtBeneficiario, txtDescricao, vlrDocumento, vlrLiquido, numMes)

gastosDeputadosMensal <- gastosDeputados %>% group_by(txNomeParlamentar, sgUF, numMes) %>% summarise(total=sum(vlrLiquido)) %>% arrange(desc(total))

Quais os deputados que ultrapassaram a cota parlamentar mensal? 
Dado para gerar o dataset de cotas se encontra aqui: http://www2.camara.leg.br/comunicacao/assessoria-de-imprensa/cota-parlamentar

gastosDeputadosCotas <- merge(x=gastosDeputadosMensal, y=cotasParlamentares, by ="sgUF", all = TRUE)
deputadosEstourados <- gastosDeputadosCotas %>% filter(total > Cota) %>% arrange(desc(total))


