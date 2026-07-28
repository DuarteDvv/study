# **Ordem execução**

1. FROM (pega as tabelas)

2. WHERE (filtra linhas individuais)

3. GROUP BY (agrupa os dados)

4. HAVING (filtra os grupos formados)

5. SELECT (escolhe as colunas, calcula agregados e window functions)

6. ORDER BY (ordena o resultado)

## **FROM**

FROM é a etapa de criação de tabelas temporárias, recuperação de tabelas e execução de JOINs

### **JOIN (INNER)**

table1 as t1 JOIN table2 as t2 ON t1.id = t2.id -> O join padrão é o INNER JOIN em que casamos cada linha de uma tabela com seu respectivo par de mesmo id (ou ids) na outra tabela. Se não existir correspondencia a linha na tabela resultado não é criada (ignorado). 

```sql
SELECT 
  u.city,
  SUM(CASE WHEN t.status = 'Completed' THEN 1 ELSE 0 END) AS total_orders
FROM 
  trades AS t JOIN users AS u ON 
  u.user_id = t.user_id
GROUP BY 
  u.city
ORDER BY
  total_orders DESC
LIMIT 3
```

### **SELF JOIN**

Faz join de uma tabela com ela mesmo de acordo com alguma chave. È util para comparar coisas dentro da mesma tabela (como tipos diferentes de funcioários).

```sql
SELECT 
  e.employee_id,
  e.name
FROM 
  employee AS e
INNER JOIN employee AS m 
  ON e.manager_id = m.employee_id 
WHERE 
  e.salary > m.salary;  
```        

## **SELECT**

### **INTERSECT** 

Operador de conjuntos que pega a interseção de linhas entre N consultas, ou seja, SELECTs distintos.

```sql
SELECT 
  c.candidate_id
FROM 
  candidates AS c
WHERE 
  c.skill = 'Python'
  
INTERSECT

SELECT 
  c.candidate_id
FROM 
  candidates AS c
WHERE 
  c.skill = 'Tableau'

INTERSECT

SELECT 
  c.candidate_id
FROM 
  candidates AS c
WHERE 
  c.skill = 'PostgreSQL'

ORDER BY 
    c.candidate_id 
ASC;
```

## **WHERE**

Filtra linhas antes de qualquer agregação, ou seja, trabalha nas linhas coletadas pelo FROM.

```sql
SELECT
  p.part, 
  p.assembly_step
FROM 
  parts_assembly AS p
WHERE 
  p.finish_date IS NULL
```

## **EXISTS**

Verifica se existe pelo menos uma linha na subconsulta e retorna booleano. 


```sql
SELECT 
  e.employee_id,
  e.name
FROM 
  employee AS e
WHERE 
  EXISTS(
    SELECT
      1
    FROM
      employee AS e2
    WHERE 
      e.manager_id = e2.employee_id AND
      e.salary > e2.salary
  )
```

```sql
SELECT 
  p.page_id
FROM
  pages as p 
WHERE 
  NOT EXISTS(
    SELECT
      *
    FROM 
      page_likes AS pl
    WHERE 
      p.page_id = pl.page_id
  )
ORDER BY 
  p.page_id
ASC 
```

## **EXCEPT**

## **DISTINCT**

## **LIKE**

## **DATE(Timestamp a ser convertido)**

Função que converte TIMESTAMP (Data + Horas) para DATE (apenas data). Qualquer operação com DATE retorna já em dias. Operações com TIMESTAMP retornam itens do tipo INTERVAL com dias, horas, minutos e segundos.

```sql
SELECT 
  p.user_id,
  MAX(DATE(p.post_date)) - MIN(DATE(p.post_date)) AS days_between
FROM 
  posts AS p 
WHERE 
  p.post_date >= '2021/01/01' AND p.post_date < '2022/01/01'
GROUP BY 
  p.user_id
HAVING 
  COUNT(p.post_id) >= 2
```

### **EXTRACT( FROM MONTH/DAY/YEAR/HOUR/SECOND/MINUTE )

Serve para extrair dados de data ou hora de date/timestamps. Sintaxe de PostgreSQL.

```sql
SELECT
  EXTRACT(MONTH FROM r.submit_date) AS mth,
  r.product_id AS product,
  ROUND(AVG(r.stars),2) AS avg_stars
FROM 
  reviews AS r
GROUP BY
  EXTRACT(MONTH FROM r.submit_date),
  r.product_id
ORDER BY
  mth, product
```
  

## **GROUP BY**

Agrupa as linhas de acordo com colunas especificadas. Todas as colunas que estiverem no SELECT e não forem uma agregação (media, max...) precisam também estar no GROUP BY mas todas as colunas que estiverem no GROUP BY não necessáriamente precisam estar no SELECT.

```sql
WITH tweets_p_user AS (
  SELECT
    t.user_id,  
    COUNT(DISTINCT t.tweet_id) AS tweet_n
  FROM 
    tweets AS t 
  WHERE 
    '1/1/2022' <= t.tweet_date AND t.tweet_date  < '1/1/2023'
  GROUP BY
    t.user_id
)

SELECT 
  tweet_n AS tweet_bucket,
  COUNT(user_id) AS users_num
FROM 
  tweets_p_user
GROUP BY
  tweet_n
```

### **Funções de agregação**

#### **Agregação Condicional**

Funciona da seguinte forma podendo aninhas varias preposições WHEN-THEN. Pode ser usado em funções de agregação como SUM e COUNT mas se for usado com COUNT o else precisa ser NULL para não contar a linha.

CASE WHEN condição1 THEN oq-fazer1 WHEN condição2 THEN oq-fazer2 ELSE oq-fazer-se-nenhuma-condição-verdadeira END 

```sql
SELECT 
  SUM(CASE WHEN v.device_type = 'laptop' THEN 1 ELSE 0 END) AS laptop_views,
  SUM(CASE WHEN v.device_type = 'phone' OR v.device_type = 'tablet' THEN 1 ELSE 0 END) AS mobile_views
FROM
  viewership AS v
```
```sql
SELECT
  t.account_id,
  SUM(
  CASE 
    WHEN t.transaction_type = 'Deposit' THEN t.amount 
    WHEN t.transaction_type = 'Withdrawal' THEN -t.amount 
    ELSE 0
  END
  ) 
FROM 
  transactions AS t 
GROUP BY
  t.account_id
```

### **HAVING** 

Filtragem que acontece após o agrupamento. Se agrupamos por candidato_id e tiramos uma média de todas as linhas agrupadas desses candidato_id agora podemos filtrar candidatos pela media calculada.

```sql
SELECT 
  c.candidate_id
FROM 
  candidates AS c
WHERE 
  c.skill IN ('Python','Tableau','PostgreSQL')
GROUP BY
  c.candidate_id
HAVING 
  COUNT(DISTINCT c.skill) = 3
ORDER BY
  c.candidate_id
ASC;
```
```sql
WITH companies_rep AS (SELECT
  jl.company_id
FROM 
  job_listings AS jl
GROUP BY
  jl.company_id,
  jl.title,
  jl.description
HAVING
  COUNT(*) > 1
)
  
SELECT 
  COUNT(DISTINCT company_id)
FROM 
  companies_rep
```

## **SELECT**

### **ROUND(valor, casas decimais)**

Arredonda casas decimais para quanto quiser
  
## **WINDOW FUNCTIONS**

### **ROW_NUMBER()**

### **OVER**

usado principalmente com ORDER BY e PARTITION BY


### **PARTITION BY**

### **ORDER BY**

Ordena o resultado do SELECT em ascendente ASC ou descendente DESC. Pode ser usado também com window functions.

## **LIMIT**

Limita o numero de linhas retornadas de baixa para cima

```sql
SELECT 
  m.sender_id,
  COUNT(m.message_id) AS message_count
FROM 
  messages AS m 
WHERE 
  m.sent_date >= '2022/08/01' AND  m.sent_date < '2022/09/01'
GROUP BY 
  m.sender_id
ORDER BY 
  message_count DESC
LIMIT 
  2
```

