# Exploration Data Analysis of credit using SQL

This table was made using S3 and AWS Athena, using the dataset described below.

## Data
- idade = customer age
- sexo = customer gender
- dependentes = customer dependents number
- escolaridade = customer scholarity
- salario_anual = wage per year
- tipo_cartao = card type
- qtd_produtos = quantity of products bought in the last 12 months
- iteracoes_12m = number of iterations in the last 12 months
- meses_inativo_12m = number of months the customer was inactive in the last 12 months
- limite_credito = credit limit
- valor_transacoes_12m = total value of transactions in the last 12 months
- qtd_transacoes_12m = quantity of transactions in the last 12 months


This dataset is a short version of this one: https://github.com/andre-marcos-perez/ebac-course-utils/blob/main/dataset/credito.csv

## Exploration

First let's understand what data we have

**Dataset Overview**
![dataset overview](https://github.com/lucaselpidio/media-rep/blob/main/table_overview.png?raw=true)
QUERY: SELECT * FROM credito limit 10;
______________________________________________________________________________________________________
**How many information do we have in this dataset?**

Just a reminder, this dataset is a shortened version of the previously described dataset. Using the full version can enrich the analysis. We're using the shortened version for studies purposes and to reduce the computational cost of AWS Athena.

![Number of Lines in the Dataset](https://github.com/lucaselpidio/media-rep/blob/main/number_lines.png?raw=true)

QUERY: SELECT COUNT (*) AS "Number of Lines" FROM credito

_______________________________________________________________________________________________________

**What type of data we have?**

![Type of data](https://github.com/lucaselpidio/media-rep/blob/main/describe.png?raw=true)

QUERY: describe credito
_______________________________________________________________________________________________________

**What level of scholarity are presented?**

This column present null data as "na"

![Level of Scholarity Presented image](https://github.com/lucaselpidio/media-rep/blob/main/scholarity.png?raw=true)

QUERY: SELECT DISTINCT escolaridade FROM credito
_______________________________________________________________________________________________________
**What marital status are presented?**

This column also present null data as "na"

![Marital Status image](https://github.com/lucaselpidio/media-rep/blob/main/marital_status.png?raw=true)

QUERY: SELECT DISTINCT estado_civil FROM credito
________________________________________________________________________________________________________
**What wage per year and type of card are presented?**

It is worth mentioning that the wage values are in string format, and are just a wage range, not containing the actual values.

This wage column also present null values as "na"

![Wage per year image](https://github.com/lucaselpidio/media-rep/blob/main/wage_per_year.png?raw=true) ![Card Type](https://github.com/lucaselpidio/media-rep/blob/main/card_type.png?raw=true)

QUERY for wage: SELECT DISTINCT salario_anual FROM credito

QUERY for card: SELECT DISTINCT tipo_cartao FROM credito
________________________________________________________________________________________________________
## Data Analysis
Now we have an idea of our dataset, let's try to understand it.

 ### **How many customers we have in each wage range?**

It's worth remembering that I left the null data out of the salario_anual column. This data includes 235 customers who didn't provide or do not include a wage range.

![Quantity of customers per wage range table](https://github.com/lucaselpidio/media-rep/blob/main/quantity_customers-wage_per_year.png?raw=true)

Let's look at this in a more visually appealing way.

![Quantity of customers per wage range graphic](https://github.com/lucaselpidio/media-rep/blob/main/qtt-wage-graphic.png?raw=true)

-  Most of the customers are in the **Less than $40K** wage range, the bank could focus on this lower-income audience.

QUERY: SELECT COUNT (*) AS "quantity of customers", salario_anual FROM credito WHERE salario_anual != 'na' GROUP BY salario_anual;
_______________________________________________________________________________________________________
**How many are male ? And how many are female?**

![gender quantity table](https://github.com/lucaselpidio/media-rep/blob/main/gender_quantity.png?raw=true)

![gender quantity graphic](https://github.com/lucaselpidio/media-rep/blob/main/gender_graphic.png?raw=true)

- Most of the customers are male

QUERY: SELECT  COUNT (*), sexo FROM credito GROUP BY sexo
_______________________________________________________________________________________________________
**What is the average age of our customers?**

![average age table](https://github.com/lucaselpidio/media-rep/blob/main/avg_age_table.png?raw=true)

- The average age are basically the same

QUERY: SELECT AVG(idade) as media_idade, min(idade) as min_idade, max(idade) as max_idade, sexo FROM credito GROUP BY sexo
_______________________________________________________________________________________________________
**What is the profile of our customers with the highest credit?**

![highest credit customers table](https://github.com/lucaselpidio/media-rep/blob/main/highest_credit_table.png?raw=true)

- There's a variety of card types and scholarity levels, but these don't seem to influence the credit limit. After all, the highest limit appears for someone with no formal education.

QUERY: SELECT MAX(limite_credito) as limite_credito, escolaridade, tipo_cartao, sexo FROM credito WHERE escolaridade != 'na' and tipo_cartao != 'na' GROUP BY escolaridade, tipo_cartao, sexo ORDER BY limite_credito DESC limit 10
_______________________________________________________________________________________________________
**What is the profile of our customers with the lowest credit?**
![Lowest credit limit table](https://github.com/lucaselpidio/media-rep/blob/main/lowest_credit_table.png?raw=true)

- Scholarity level doesn't seem to affect the low limit here either.
- Female is the most common gender.
- It's also worth noting that the Platinum card level doesn't appear in the lower limits.

QUERY: SELECT MAX(limite_credito) as limite_credito, escolaridade, tipo_cartao, sexo FROM credito WHERE escolaridade != 'na' and tipo_cartao != 'na' GROUP BY escolaridade, tipo_cartao, sexo ORDER BY limite_credito ASC limit 10
______________________________________________________________________________________________________
**What gender spend the most?**

![Gender Spend table](https://github.com/lucaselpidio/media-rep/blob/main/gender_spend_table.png?raw=true)

- The max, minimum and average spending between men and women is very similar

QUERY: SELECT MAX(valor_transacoes_12m) as maior_valor_gasto, AVG(valor_transacoes_12m) as media_valor_gasto, MIN(valor_transacoes_12m) as min_valor_gasto, sexo FROM credito GROUP BY sexo
______________________________________________________________________________________________________
**Does wage per year impact credit limit?**

![average limit by annual wage table](https://github.com/lucaselpidio/media-rep/blob/main/avg_limit-wage-v2.png?raw=true)

- Yes it does, the higher the wage, the higher the limit
- Men have better average credit limit than woman

QUERY: SELECT AVG(limite_credito) as media_limite, sexo, salario_anual FROM credito WHERE salario_anual != 'na' GROUP BY sexo, salario_anual ORDER BY media_limite DESC
______________________________________________________________________________________________________
## Conclusion
- Most of the customers are in the Less than $40K wage range, the bank could focus on this lower-income audience.
- Most of the customers are male
- Average age between males and females customers are basically the same
- Highest credit limit ranking have a variety of card types and scholarity levels, but these don't seem to influence the credit limit.
- Highest limit appears for someone with no formal education
- There's a lot of variety in scholarity level considering both highest/lowest credit limits.
- Usually females have lower credit.
- Platinum card level doesn't appear in the lower limits
- The max, minimum and average spending between men and women is very similar
- The higher the wage, the higher the limit
