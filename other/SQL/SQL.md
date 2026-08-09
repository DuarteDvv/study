# **Ordem execução**

1. FROM (pega as tabelas)

2. WHERE (filtra linhas individuais)

3. GROUP BY (agrupa os dados)

4. HAVING (filtra os grupos formados)

5. SELECT (escolhe as colunas, calcula agregados e window functions)

6. ORDER BY (ordena o resultado)

## **FROM**

FROM é a etapa de criação de tabelas temporárias, recuperação de tabelas e execução de JOINs

### **CTEs**

Para criar tabelas temporarias e deixar a query bem mais legivel a sintaxe é:

```sql
WITH 

tabela1 AS (
  query
),

tabela2 AS (
  query
),
...
```

```sql
WITH 

max_stocks AS (

  SELECT 
    ticker,
    TO_CHAR(date, 'Mon-YYYY') AS mth,
    open,
    ROW_NUMBER() OVER (PARTITION BY ticker ORDER BY open DESC) AS rank_
  FROM 
    stock_prices
),

min_stocks AS (

  SELECT 
    ticker,
    TO_CHAR(date, 'Mon-YYYY') AS mth,
    open,
    ROW_NUMBER() OVER (PARTITION BY ticker ORDER BY open ASC) AS rank_
  FROM 
    stock_prices
)

SELECT 
  max_.ticker,
  max_.mth,
  max_.open,
  min_.mth,
  min_.open
FROM
  max_stocks AS max_ JOIN min_stocks AS min_ ON max_.ticker = min_.ticker
WHERE
  max_.rank_ = 1 AND min_.rank_ = 1
```

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

Faz join de uma tabela com ela mesma. È util para comparar coisas dentro da mesma tabela (como tipos diferentes de funcioários).

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

### **CROSS JOIN**

Faz produto cartesiano entre duas tabelas (se uma tem 2 linhas e outra 3, o resultado é uma com 2x3=6). Util para agregar CTEs pequenas com valor escalar unico como por exemplo uma tabela de uma linha e coluna com um unico valor.

```sql
WITH distinct_categories AS (
  SELECT 
    COUNT(DISTINCT product_category) AS total_categories
  FROM 
    products
)

SELECT 
  cc.customer_id
FROM 
  customer_contracts AS cc
JOIN 
  products AS p ON cc.product_id = p.product_id
CROSS JOIN 
  distinct_categories AS dc
GROUP BY 
  cc.customer_id, 
  dc.total_categories
HAVING 
  COUNT(DISTINCT p.product_category) = dc.total_categories;
```

### **LEFT JOIN**

Trás todos que satisfazerem a junção no ON + todos que não satisfazerem da tabela da esquerda do ON (com null)

### **RIGHT JOIN**

Trás todos que satisfazerem a junção no ON + todos que não satisfazerem da tabela da direita do ON (com null)

### **FULL OUTER JOIN**

Trás todos que satisfazerem a junção no ON + todos que não satisfazerem da tabela da direita do ON (com null) e todos que não satisfazerem da tabela da esquerda do ON (com null)


```sql
WITH 

week_count AS (
  SELECT 
    w.user_id,
    w.song_id,
    COUNT(*) AS w_song_plays
  FROM 
      songs_weekly AS w
  WHERE
    DATE(w.listen_time) < '2022-08-05'
  GROUP BY
    w.user_id,
    w.song_id
)


SELECT 
  COALESCE(wc.user_id, h.user_id),
  COALESCE(wc.song_id, h.song_id),
  COALESCE(h.song_plays,0) + COALESCE(wc.w_song_plays,0) AS song_plays
FROM 
    week_count AS wc 
  FULL OUTER JOIN 
    songs_history AS h 
  ON 
    wc.user_id = h.user_id AND
    wc.song_id = h.song_id
ORDER BY
  song_plays DESC
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

### **EXCEPT**

### **DISTINCT**

Pode ser usado no SELECT DISTINCT para filtrar linhas completamente iguais (todas as colunas). Ou funções de agregação como COUNT(DISTINCT ...) para contar contar coisas unicas.


### **LIKE**

Comparação de strings

### **(Timestamp a ser convertido)::DATE**

Função que converte TIMESTAMP (Data + Horas) para DATE (apenas data). Qualquer operação com DATE retorna já em dias. Operações com TIMESTAMP retornam itens do tipo INTERVAL com dias, horas, minutos e segundos.

### **(Timestamp a ser convertido)::TIME**

Converte timestamp para TIME que preserva apenas horas (hh-mm-ss)

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

### **EXTRACT(FROM MONTH/DAY/YEAR/HOUR/SECOND/MINUTE)**

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



#### **Agregação Condicional**

Funciona da seguinte forma podendo aninhas varias preposições WHEN-THEN. Pode ser usado em funções de agregação como SUM e COUNT mas se for usado com COUNT o else precisa ser NULL para não contar a linha.

CASE WHEN condição1 THEN oq-fazer1 WHEN condição2 THEN oq-fazer2 ELSE oq-fazer-se-nenhuma-condição-verdadeira END.

Além disso, não funciona apenas em agregações mas para linhas normais tbm.

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
```sql
SELECT
  e.app_id,
  ROUND(
    100.0 * SUM(CASE WHEN e.event_type = 'click' THEN 1 ELSE 0 END) 
    / 
    SUM(CASE WHEN e.event_type = 'impression' THEN 1 ELSE 0 END)
  , 2) AS ctr
FROM
  events AS e
WHERE
  e.timestamp >= '2022/01/01' AND e.timestamp < '2023/01/01'
GROUP BY  
  e.app_id;
```

```sql
SELECT 
  age_.age_bucket, 
  ROUND(100.0 *
    SUM(
      CASE 
        WHEN a.activity_type = 'send' THEN a.time_spent 
        ELSE 0 
      END) / 
    SUM(
      CASE 
        WHEN a.activity_type = 'send' OR a.activity_type = 'open' THEN a.time_spent 
        ELSE 0 
      END),2) AS send_perc,
  ROUND(100.0*
    SUM(
    CASE 
      WHEN a.activity_type = 'open' THEN a.time_spent 
      ELSE 0 
    END) / 
  SUM(
    CASE 
      WHEN a.activity_type = 'send' OR a.activity_type = 'open' THEN a.time_spent 
      ELSE 0 
    END),2) AS open_perc
FROM 
  activities AS a JOIN age_breakdown AS age_ 
  ON a.user_id = age_.user_id
GROUP BY
  age_.age_bucket
```
```sql
WITH 

numbered_orders AS (
  SELECT
    *, 
    ROW_NUMBER() OVER() AS row_n
  FROM
    orders
)

SELECT 
  (CASE 
    WHEN row_n % 2 != 0 THEN COALESCE(LEAD(order_id,1) OVER(), order_id)
    WHEN row_n % 2 = 0 THEN LAG(order_id,1) OVER()
  END) AS new_id,
  item
FROM 
  numbered_orders
ORDER BY
  new_id
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
  
### **WINDOW FUNCTIONS**

Windows functions sempre seguem esse padrão

$$\text{Função}(X) \quad \mathbf{OVER} \quad \left( Y \right)$$

Em que: 
- X é uma função de agregação, ranking ou navegação
- Y é quem define quais linhas entram na agregação e qual ordem elas terão

#### **OVER()**

O OVER() funciona como uma janela de observação sobre a tabela inteira que permite calcular agregações, rankings e acúmulos sem destruir as linhas originais (diferente do GROUP BY, que as esmaga). Ele preserva a identidade de cada registro intacta e apenas adiciona uma nova coluna calculada ao lado, permitindo comparar o dado individual com o contexto do grupo de forma simples e direta.

```sql
WITH transactions_w_number AS (
  SELECT 
    *,
    ROW_NUMBER() OVER(PARTITION BY user_id ORDER BY transaction_date) AS row_n
  FROM 
    transactions
)

SELECT 
  t.user_id,
  t.spend,
  t.transaction_date
FROM 
  transactions_w_number AS t 
WHERE
  row_n = 3
```


#### **PARTITION BY**

Separa a aplicação função em grupos diferentes da janela do OVER() começando sempre do zero. Sem o partition by o ranking leva em consideração todas as linhas. Com o partition by um ranking é criado por grupo. 


#### **ORDER BY**

Ordena o resultado do SELECT em ascendente ASC ou descendente DESC. Pode ser usado também com window functions para ordenar a janela do OVER()


### **Funções de navegação**

```sql
SELECT 
  data_venda,
  valor AS venda_hoje,
  
  -- Pega a venda da 1ª linha ANTERIOR (Ontem)
  LAG(valor, 1) OVER (ORDER BY data_venda) AS venda_ontem,
  
  -- Pega a venda da 1ª linha SEGUINTE (Amanhã)
  LEAD(valor, 1) OVER (ORDER BY data_venda) AS venda_amanha
FROM 
  vendas_diarias;
```

#### **LAG(coluna, deslocamento k, valor padrão)**

O LAG() acessa o valor de uma única célula localizada exatamente $k$ linhas acima (atrás) da linha atual, considerando a ordem definida na janela do OVER(). Caso não exista retorna o valor padrão ou nulo por padrão.

```sql
WITH tweets_window AS (
  SELECT 
    t.user_id,
    t.tweet_date,
    t.tweet_count AS hoje,
    LAG(t.tweet_count, 1) OVER (PARTITION BY t.user_id ORDER BY t.tweet_date) AS ontem, 
    LAG(t.tweet_count, 2) OVER (PARTITION BY t.user_id ORDER BY t.tweet_date) AS anteontem
  FROM tweets AS t
)

SELECT
  user_id,
  tweet_date, 
  ROUND(
    1.0 * (hoje + COALESCE(ontem, 0) + COALESCE(anteontem, 0))
    / 
    (
      1.0 + 
      CASE WHEN ontem IS NULL THEN 0 ELSE 1 END +
      CASE WHEN anteontem IS NULL THEN 0 ELSE 1 END
    )
  , 2) AS rolling_avg_3d
FROM 
  tweets_window;
```

#### **LEAD(coluna, deslocamento k, valor padrão)**

O LEAD() acessa o valor de uma única célula localizada exatamente $k$ linhas abaixo (frente) da linha atual, considerando a ordem definida na janela do OVER(). Caso não exista retorna o valor padrão ou nulo por padrão.

```sql
WITH 

rides_w_number_and_next_ride AS (
  SELECT 
    u.user_id,
    r.ride_date AS ride_date,
    u.registration_date,
    LEAD(r.ride_date,1) OVER (PARTITION BY user_id ORDER BY ride_date) AS next_ride, --- vira null 
    ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY ride_date) AS row_n 
  FROM 
    rides AS r JOIN users AS u 
    ON r.user_id = u.user_id
)

SELECT 
  ROUND(AVG(DATE(next_ride) - DATE(ride_date)),2) AS average_delay
FROM
    rides_w_number_and_next_ride
WHERE 
  row_n = 1 AND
  DATE(ride_date) = DATE(registration_date)
```

### **Funções de ranking**

#### **ROW_NUMBER()**

Numeração sequencial única (1, 2, 3...) para cada linha. Desempata arbitrariamente se houver valores iguais, ou seja, se 2 linhas são iguais vão receber posições diferentes (3,4).

```sql
WITH 

numbered_measurements AS (
  SELECT 
    measurement_time::DATE AS day_,
    measurement_value AS value_,
    ROW_NUMBER() OVER (PARTITION BY measurement_time::DATE ORDER BY measurement_time::TIME) AS row_n
  FROM 
    measurements
)

SELECT 
  day_,
  SUM(
    CASE
      WHEN row_n % 2 = 0 THEN 0 
      ELSE value_
    END
  ) AS odd_sum,
  SUM(
    CASE
      WHEN row_n % 2 = 0 THEN value_
      ELSE 0
    END
  ) AS even_sum
FROM 
  numbered_measurements
GROUP BY
  day_
```

```sql
WITH 

q AS (SELECT 
  ps.category,
  ps.product,
  SUM(ps.spend) AS total_spend,
  ROW_NUMBER() OVER (PARTITION BY ps.category ORDER BY SUM(ps.spend) DESC) AS row_n
FROM
  product_spend AS ps
WHERE 
  ps.transaction_date < '2023-01-01' AND 
  ps.transaction_date >= '2022-01-01'
GROUP BY 
  ps.category,
  ps.product
)

SELECT
  category,
  product,
  total_spend
FROM 
  q 
WHERE 
  row_n = 1 OR row_n = 2
```

#### **RANK()**

Atribui a mesma posição em caso de empate, mas pula as posições seguintes (ex: 1, 2, 2, 4).

#### **DENSE_RANK()**

Atribui a mesma posição em caso de empate e não pula posições (ex: 1, 2, 2, 3).

```sql
WITH 

temp_ AS (

  SELECT
    d.department_name,
    e.name, 
    e.salary,
    DENSE_RANK() OVER(PARTITION BY d.department_id ORDER BY e.salary DESC) AS row_n
  FROM
    employee AS e JOIN department AS d 
    ON e.department_id = d.department_id
)

SELECT 
  department_name,
  name, 
  salary
FROM 
  temp_
WHERE
  row_n IN (1,2,3)
ORDER BY 
  department_name ASC, salary DESC, name ASC
```

```sql
WITH 

employee_w_row_n AS (
  SELECT
    DENSE_RANK() OVER(ORDER BY e.salary DESC) AS row_n,
    e.salary
  FROM 
    employee AS e
)

SELECT DISTINCT 
  salary
FROM 
  employee_w_row_n 
WHERE
  row_n = 2
```

### **Funções de agregação**

#### **COUNT()**

Conta o total de registros dentro do intervalo da janela.

```sql
SELECT 
  ROUND(
    COUNT(DISTINCT CASE WHEN t.signup_action = 'Confirmed' THEN e.email_id END)::DECIMAL
    / 
    COUNT(DISTINCT e.email_id)
  , 2) AS confirm_rate
FROM 
    emails AS e
  LEFT JOIN 
    texts AS t 
  ON 
    e.email_id = t.email_id;
```

#### **SUM()**

Soma os valores da janela

#### **AVG()**

Calcula a média do grupo. Ignora nulos.

### **LIMIT**

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

### **CONCAT**

Usando para concatenar varias coisas em string por exemplo: CONCAT('Soma é ', SUM(), ' mil dolares')

### **COALESCE(Valor, Valor-caso-nulo)**

Caso um valor venha nulo substitui por outro padrão