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
```sql
SELECT 
  ROUND(
    100 * COUNT(
      CASE 
        WHEN c_inf.country_id != r_inf.country_id THEN r_inf.country_id
        ELSE NULL
      END
    )::DECIMAL
    /
    COUNT(r_inf.country_id)
  ,1)
FROM 
  (phone_calls AS pc JOIN phone_info AS c_inf ON c_inf.caller_id = pc.caller_id) 
  JOIN phone_info AS r_inf ON pc.receiver_id = r_inf.caller_id
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

```sql
WITH 

spend_by_year AS (
  SELECT
    EXTRACT(YEAR FROM transaction_date) AS year_,
    product_id,
    SUM(spend) AS total_spend
  FROM 
    user_transactions
  GROUP BY 
    EXTRACT(YEAR FROM transaction_date), 
    product_id
)


SELECT 
  curr.year_,
  curr.product_id,
  curr.total_spend AS curr_spend, 
  last_year.total_spend AS last_spend,
  ROUND(100.0 *
    (curr.total_spend - last_year.total_spend) 
    /
    last_year.total_spend
  ,2)
FROM 
  spend_by_year AS curr LEFT JOIN spend_by_year AS last_year 
  ON 
    curr.product_id = last_year.product_id AND
    curr.year_ = last_year.year_ + 1
ORDER BY
  product_id,
  curr.year_ ASC
```

```sql
SELECT
  d.driver_id,
  d.driver_name,
  COALESCE(COUNT(DISTINCT r.ride_id), 0) AS count
FROM
  drivers AS d LEFT JOIN rides as r ON d.driver_id = r.driver_id
GROUP BY
  d.driver_id,
  d.driver_name
```

### **RIGHT JOIN**


```sql
WITH 

count_by_employee AS (
  SELECT 
    e.employee_id,
    COALESCE(COUNT(DISTINCT q.query_id),0) AS count_
  FROM 
    queries AS q RIGHT JOIN employees AS e 
  ON 
    q.employee_id = e.employee_id AND
    (query_starttime < '2023-10-01' AND 
    query_starttime >= '2023-07-01') 
  GROUP BY 
    e.employee_id
)

SELECT 
  count_,
  COUNT(*)
FROM 
  count_by_employee
GROUP BY
  count_
ORDER BY
  count_ ASC
```

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

### **UNION e UNION ALL**

UNION junta as linhas de 2 consultas e remove as linhas duplicadas. UNION ALL junta sem remover duplicatas  

```sql
WITH spec_batch AS (
  SELECT
    item_type,
    COUNT(*) AS batch_itens,
    SUM(square_footage) AS batch_size
  FROM 
    inventory
  GROUP BY 
    item_type
),

prime_used AS (
  SELECT
    item_type,
    TRUNC(500000 / batch_size) * batch_itens AS item_count,
    MOD(500000, batch_size) AS resto
  FROM 
    spec_batch 
  WHERE 
    item_type = 'prime_eligible'
),

non_prime AS (
  SELECT 
    sb.item_type,
    TRUNC(pu.resto / sb.batch_size) * sb.batch_itens AS item_count
  FROM 
    prime_used AS pu 
    CROSS JOIN spec_batch AS sb
  WHERE 
    sb.item_type = 'not_prime'
)

SELECT item_type, item_count FROM prime_used
UNION 
SELECT item_type, item_count FROM non_prime;
```

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

```sql
WITH 

inter AS (
  SELECT DISTINCT
    user_id
  FROM 
    user_actions
  WHERE 
    EXTRACT(MONTH FROM event_date) = 6 AND
    EXTRACT(YEAR FROM event_date) = 2022 AND
    event_type IN ('sign-in', 'like', 'comment')
  
INTERSECT

  SELECT DISTINCT
    user_id
  FROM 
    user_actions
  WHERE 
    EXTRACT(MONTH FROM event_date) = 7 AND
    EXTRACT(YEAR FROM event_date) = 2022 AND
    event_type IN ('sign-in', 'like', 'comment')
)

SELECT 
  7 AS month,
  COUNT(*) AS active
FROM 
  inter
```

### **EXCEPT**

Subtração de conjuntos (consultas), ou seja, A - B remove tudo de B que esta em A

```sql
WITH 

sp_2025 AS (
  SELECT 
    riders_id
  FROM 
    signups
  WHERE 
    city = 'SP' AND
    EXTRACT(YEAR FROM signup_date) = 2025
),

rj_2025 AS (
  SELECT 
    riders_id
  FROM 
    signups
  WHERE 
    city = 'RJ' AND
    EXTRACT(YEAR FROM signup_date) = 2025
)

SELECT * FROM sp_2025  
EXCEPT 
SELECT * FROM rj_2025
```

```sql
SELECT DISTINCT
  driver_id
FROM 
  drivers

EXCEPT

SELECT DISTINCT
  driver_id
FROM 
  rides
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


### **DISTINCT**

Pode ser usado no SELECT DISTINCT para filtrar linhas completamente iguais (todas as colunas). Ou funções de agregação como COUNT(DISTINCT ...) para contar contar coisas unicas.

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

```sql
SELECT 
  driver_id,
  COUNT(ride_id),
  100 *(
    COUNT(
      CASE
        WHEN rating < 3 THEN rating
        ELSE NULL
      END
    )::DECIMAL
    /
    COUNT(rating)
  )

FROM
  rides
GROUP BY 
  driver_id

```


### **HAVING** 

Filtragem que acontece após o agrupamento. Se agrupamos por candidato_id e tiramos uma média de todas as linhas agrupadas desses candidato_id agora podemos filtrar candidatos pela media calculada.

```sql
WITH 

calls_by_holder AS (
  SELECT 
    policy_holder_id
  FROM 
    callers
  GROUP BY 
    policy_holder_id
  HAVING 
    COUNT(case_id) > 2
)

SELECT
  COUNT(*)
FROM 
  calls_by_holder 
```

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

### **SUM()**

SUM() junto com OVER() faz soma acumulada seguindo alguma ordem 

```sql
WITH cumulative AS (
  SELECT 
    searches,
    SUM(num_users) OVER (ORDER BY searches ASC) AS cum_asc,
    SUM(num_users) OVER (ORDER BY searches DESC) AS cum_desc,
    SUM(num_users) OVER () AS total_n
  FROM 
    search_frequency
)
SELECT 
  ROUND(AVG(searches * 1.0), 1) AS median
FROM 
  cumulative
WHERE 
  cum_asc >= total_n / 2.0 
  AND cum_desc >= total_n / 2.0;
```

#### **OVER()**

O OVER() funciona como uma janela de observação sobre a tabela inteira que permite calcular agregações, rankings e acúmulos sem destruir as linhas originais (diferente do GROUP BY, que as esmaga). Ele preserva a identidade de cada registro intacta e apenas adiciona uma nova coluna calculada ao lado, permitindo comparar o dado individual com o contexto do grupo de forma simples e direta.



```sql
WITH

freq_top10_by_song AS (
  SELECT 
    song_id,
    COUNT(*) AS freq
  FROM 
    global_song_rank
  WHERE 
    rank <= 10
  GROUP BY
    song_id
),


rank_artists AS (
  SELECT
    s.artist_id,
    DENSE_RANK() OVER(ORDER BY SUM(f.freq) DESC) AS rnk
  FROM 
    songs AS s JOIN freq_top10_by_song AS f ON s.song_id = f.song_id
  GROUP BY
    s.artist_id
)


SELECT 
  a.artist_name,
  ra.rnk
FROM 
  rank_artists AS ra JOIN artists AS a ON ra.artist_id = a.artist_id
WHERE 
  ra.rnk <= 5
ORDER BY 
  ra.rnk ASC
```


#### **PARTITION BY**

Separa a aplicação função em grupos diferentes da janela do OVER() começando sempre do zero. Sem o partition by o ranking leva em consideração todas as linhas. Com o partition by um ranking é criado por grupo. 


#### **ORDER BY**

```sql
SELECT 
  drug,
  (total_sales - cogs) AS proft
FROM 
  pharmacy_sales
ORDER BY  
  proft DESC
LIMIT 
  3
```

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

O LAG() acessa o valor de uma única célula localizada exatamente $k$ linhas acima (atrás) da linha atual, considerando a ordem definida na janela do OVER(). Caso não exista retorna o valor padrão ou nulo por padrão. Olha para linhas que tem rank < que o rank da linha atual.

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
```sql
WITH 

num_transactions AS (
  SELECT
    *,
    LAG(transaction_date,1) OVER(PARTITION BY user_id ORDER BY transaction_date) AS last,
    LAG(transaction_date,2) OVER(PARTITION BY user_id ORDER BY transaction_date) AS second_last
  FROM 
    transactions
)


SELECT DISTINCT
  user_id 
FROM 
  num_transactions
WHERE 
  last IS NOT NULL AND 
  second_last IS NOT NULL AND
  second_last::DATE = last::DATE - 1 AND
  last::DATE = transaction_date::DATE - 1
ORDER BY
  user_id ASC
```


#### **LEAD(coluna, deslocamento k, valor padrão)**

O LEAD() acessa o valor de uma única célula localizada exatamente $k$ linhas abaixo (frente) da linha atual, considerando a ordem definida na janela do OVER(). Caso não exista retorna o valor padrão ou nulo por padrão. Olha para linhas que tem rank > que o rank da linha atual.

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

#### **ROWS/RANGE BETWEEN [início] AND [fim]**

ROWS BETWEEN [início] AND [fim] tal que as opcoes para inicio e fim:

```sql
N PRECEDING → N linhas antes da atual
CURRENT ROW → a própria linha atual
N FOLLOWING → N linhas depois da atual
UNBOUNDED PRECEDING → desde o início da partição
UNBOUNDED FOLLOWING → até o final da partição
```

ROWS conta literalmente linhas entao se temos linhas iguais no ranking apenas uma entra, RANGE olha para o rank da linha entao se tem duplicatas contas ambas

```sql
WITH 
  ranked_rides AS (
    SELECT
      rider_id,
      ride_date,
      fare_amount,
      LAG(ride_date, 1) OVER (
        PARTITION BY
          rider_id
        ORDER BY
          ride_date ASC
      ) AS last_ride_date,
      AVG(fare_amount) OVER (
        PARTITION BY
          rider_id
        ORDER BY
          ride_date ASC
        ROWS 
          BETWEEN 2 PRECEDING AND CURRENT ROW 
      ) AS 3_avg
    FROM 
      rides
  )

SELECT
  rider_id,
  ride_date,
  fare_amount,
  DATEDIFF('days', last_ride_date, ride_date) AS days_since_last_ride,
  3_avg
FROM
  ranked_rides
```

```sql 
SELECT 
  MAX(fare_amount) OVER(
    PARTITION BY 
      driver_id
    ORDER BY  
      ride_date ASC
    ROWS 
      BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) 
FROM 
  rides
```

```sql
SELECT 
  AVG(fare_amount) OVER(
    PARTITION BY 
      driver_id 
    ORDER BY 
      ride_date ASC
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
  ) AS type1_window,
  AVG(fare_amount) OVER(
    PARTITION BY 
      driver_id 
    ORDER BY 
      ride_date ASC
    ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
  ) AS type2_window,
  AVG(fare_amount) OVER(
    PARTITION BY 
      driver_id 
    ORDER BY 
      ride_date ASC
    ROWS BETWEEN CURRENT ROW AND 2 FOLLOWING
  ) AS type3_window
FROM 
  rides
```

```sql
SELECT 
  SUM(fare_amount) OVER(
    PARTITION BY 
      driver_id 
    ORDER BY 
      ride_date ASC
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
  ) AS cumulative_sum,
  SUM(fare_amount) OVER(
    PARTITION BY 
      driver_id 
    ORDER BY 
      ride_date ASC
    ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
  ) AS cumulative_sum_inverse
FROM
  rides
```

```sql
WITH 

daily AS (
  SELECT 
    driver_id,
    DATE_TRUNC('day', ride_date) AS day,
    SUM(fare_amount) AS daily_fare
  FROM
    rides   
  GROUP BY 
    driver_id,
    DATE_TRUNC('day', ride_date)
),

window3_avg AS (
  SELECT 
    driver_id,
    day,
    AVG(daily_fare) OVER (
      PARTITION BY 
        driver_id
      ORDER BY 
        day ASC
      ROWS 
        BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS avg3
  FROM 
    daily
),

count_month_rides AS (
  SELECT 
    driver_id,
    DATE_TRUNC('month', ride_date) AS month,
    COUNT(DISTINCT DATE_TRUNC('day', ride_date)) AS work_days
  FROM 
    rides
  GROUP BY 
    driver_id,
    DATE_TRUNC('month', ride_date)
)

SELECT 
  w.driver_id,
  w.day,
  w.avg3,
  c.month,
  c.work_days
FROM 
  window3_avg AS w JOIN count_month_rides AS c ON w.driver_id = c.driver_id AND DATE_TRUNC('month', w.day) = c.month
```

### **Funções de ranking**

#### **ROW_NUMBER()**

Numeração sequencial única (1, 2, 3...) para cada linha. Desempata arbitrariamente se houver valores iguais, ou seja, se 2 linhas são iguais vão receber posições diferentes (3,4).

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

```sql
WITH 

num_cards AS (
  SELECT 
    *,
    ROW_NUMBER() OVER(PARTITION BY card_name ORDER BY issue_year ASC, issue_month ASC) AS rank_
  FROM 
    monthly_cards_issued
)

SELECT 
  card_name,
  issued_amount
FROM
  num_cards
WHERE 
  rank_= 1
ORDER BY
  issued_amount DESC
```
  
#### **RANK()**

Atribui a mesma posição em caso de empate, mas pula as posições seguintes (ex: 1, 2, 2, 4).

#### **DENSE_RANK()**

Atribui a mesma posição em caso de empate e não pula posições (ex: 1, 2, 2, 3).

```sql
WITH 
  
ranked_products AS (
  SELECT
    DENSE_RANK() OVER (PARTITION BY p.category_name ORDER BY ps.sales_quantity DESC, ps.rating DESC) AS rnk,
    *
  FROM 
    products AS p JOIN product_sales AS ps ON p.product_id = ps.product_id
)

SELECT 
  category_name,
  product_name
FROM 
  ranked_products
WHERE 
  rnk = 1

```

```sql
WITH 

moda AS (
  SELECT 
    *,
    DENSE_RANK() OVER (ORDER BY order_occurrences DESC) AS rank
  FROM 
    items_per_order
)

SELECT 
  item_count
FROM 
  moda
WHERE 
  rank = 1
ORDER BY 
  item_count ASC
```

```sql
WITH 

num_transactions AS  (

  SELECT
    *,
    DENSE_RANK() OVER(PARTITION BY user_id ORDER BY transaction_date DESC) AS rank_
  FROM 
    user_transactions
)

SELECT
  transaction_date,
  user_id, 
  COUNT(*)
FROM 
  num_transactions
WHERE 
  rank_ = 1
GROUP BY
  transaction_date,
  user_id
ORDER BY
  transaction_date ASC
```

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
```sql
SELECT 
  ROUND( 100.0*
    COUNT(
      CASE 
        WHEN call_category = 'n/a' THEN case_id 
        WHEN call_category IS NULL THEN case_id
        ELSE NULL
      END
    )
    /
    COUNT(*)
  ,1)
FROM 
  callers
```

#### **SUM()**

Soma os valores da janela

```sql
SELECT 
  ROUND(
    SUM(item_count * order_occurrences)::DECIMAL /
    SUM(order_occurrences)
  ,1)
FROM 
  items_per_order
```
```sql
SELECT 
  manufacturer,
  COUNT(*),
  - SUM(total_sales - cogs) AS loss
FROM 
  pharmacy_sales
WHERE 
  (total_sales - cogs < 0)
GROUP BY 
  manufacturer 
ORDER BY
  loss DESC
```


#### **AVG()**

Calcula a média do grupo. Ignora nulos.

#### **MIN() e MAX()**

Retorna o min ou max do grupo

```sql
SELECT 
  card_name,
  MAX(issued_amount) - MIN(issued_amount) as difference
FROM 
  monthly_cards_issued
GROUP BY 
  card_name
ORDER BY 
  difference DESC
```

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

### **IS / IN** 

- IS é reservado para nulos e booleanos (IS NULL, IS NOT NULL)
- IN é usado para verificar se existe dentro de um vetor (IN (1,2,3))
  

### **COALESCE(Valor, Valor-caso-nulo)**

Caso um valor venha nulo substitui por outro padrão

### **MOD()**

Função de modulo que é mais segura que %

### **NULLIF(coluna, valor)**

transforma em nulo se a coluna tiver o valor especificado

```sql
WITH 
month_fare AS (
  SELECT 
    city,
    SUM(
      CASE 
        WHEN DATE_TRUNC('month', ride_date) = '2026-08-01' THEN fare_amount
        ELSE 0
      END
    ) AS aug,
    SUM(
      CASE 
        WHEN DATE_TRUNC('month', ride_date) = '2026-07-01' THEN fare_amount
        ELSE 0
      END
    ) AS july
  FROM  
    rides
  GROUP BY 
    city
)

SELECT
  city,
  july,
  aug,
  ((aug - july) / NULLIF(july, 0))* 100
FROM 
  month_fare

```

### **TRUNC(valor, x)**

Corta na xth casa decimal, o padrão é x = 0 que trunca para inteiro 

### **FLOOR()**

Arredonda para baixo


## **DATE FUNCTIONS**

### **DATE_TRUNC(Unidade, data)**

Trunca a data mantendo apenas informacoes maiores que a unidade, por exemplo:

```sql
DATE_TRUNC('year', '2026-08-27 14:35:22')   → 2026-01-01 00:00:00
DATE_TRUNC('month', '2026-08-27 14:35:22')  → 2026-08-01 00:00:00
DATE_TRUNC('day', '2026-08-27 14:35:22')    → 2026-08-27 00:00:00
DATE_TRUNC('hour', '2026-08-27 14:35:22')   → 2026-08-27 14:00:00
```

```sql
SELECT 
  rider_id,
  created_at,
  TO_CHAR(created_at, 'Mon-YYYY') AS month,
  ticket_id,
  subject,
  DATEDIFF(
    'days',
    LAG(created_at, 1) OVER(
      PARTITION BY 
        rider_id, 
        DATE_TRUNC('month', created_at)
      ORDER BY 
        created_at ASC 
    ), 
  created_at
  ) AS days_since_previous_ticket_on_same_month,

FROM 
  support_tickets 
WHERE 
  LOWER(subject) LIKE '%refund%'
ORDER BY 
  rider_id ASC,
  created_at ASC 
```

```sql
WITH 

total_fare AS (
  SELECT 
    city,
    driver_id,
    SUM(fare_amount) AS total_amount
  FROM 
    rides 
  WHERE 
    DATE_TRUNC('month', ride_date) = '2026-08-01'
  GROUP BY 
    city,
    driver_id
),

ranked_drivers AS (
  SELECT 
    city,
    driver_id,
    DENSE_RANK() OVER(
      PARTITION BY 
        city 
      ORDER BY
        total_amount DESC 
    ) AS rnk
  FROM
    total_fare 
)

SELECT 
  city, 
  driver_id
FROM 
  ranked_drivers 
WHERE 
  rnk = 1

```

### **DATEDIFF(Unidade, data1, data2)**

Retorna a diferenca entre 2 datas ou timestamps em uma unidade como days, hours, minutes e etc

```sql
SELECT 
  DATE_TRUNC('month', pickup_time) AS month,
  AVG(DATEDIFF('minutes', pickup_time, dropoff_time)) AS avg_trip_duration,
  COUNT(driver_id) AS num_trips
FROM 
  trips
GROUP BY 
  DATE_TRUNC('month', pickup_time)
HAVING 
  AVG(DATEDIFF('minutes', pickup_time, dropoff_time)) > 15
ORDER BY 
  month ASC;
```


### **EXTRACT(MONTH/DAY/YEAR/HOUR/SECOND/MINUTE FROM data)**

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

### **(Timestamp a ser convertido)::DATE**

Função que converte TIMESTAMP (Data + Horas) para DATE (apenas data). Qualquer operação com DATE retorna já em dias. Operações com TIMESTAMP retornam itens do tipo INTERVAL com dias, horas, minutos e segundos.

```sql
SELECT DISTINCT
  user_id
FROM 
  emails AS e JOIN texts AS t ON e.email_id = t.email_id
WHERE 
  t.action_date::DATE = e.signup_date::DATE + 1 AND 
  t.signup_action = 'Confirmed'
```
```sql
SELECT 
  p.user_id,
  MAX(p.post_date::DATE) - MIN(p.post_date::DATE) AS days_between
FROM 
  posts AS p 
WHERE 
  p.post_date >= '2021/01/01' AND p.post_date < '2022/01/01'
GROUP BY 
  p.user_id
HAVING 
  COUNT(p.post_id) >= 2
```

### **(Timestamp a ser convertido)::TIME**

Converte timestamp para TIME que preserva apenas horas (hh-mm-ss)

### **TO_CHAR(timestamp, 'formato')**

Converte um timestamp para um formato especifico em string como MM-YYYY (06-2022) ou Mon-YYYY (Jun-2022)

```sql
SELECT 
    TO_CHAR(trans_date,'YYYY-MM') AS month,
    country,
    COUNT(*) AS trans_count,
    COUNT(
        CASE
            WHEN state = 'approved' THEN state
            ELSE NULL
        END
    ) AS approved_count,
    SUM(amount) AS trans_total_amount,
    SUM(
        CASE 
            WHEN state = 'approved' THEN amount
            ELSE 0
        END
    ) AS approved_total_amount
FROM 
    Transactions 
GROUP BY
    TO_CHAR(trans_date,'YYYY-MM'), 
    country
```

## **STRING MANIPULATION**

### **LIKE**

Comparacao de strings: % é qualquer coisa de qualquer tamanho. 

```sql
LIKE '%gmail.com'      -- termina com "gmail.com"
LIKE 'A%'               -- começa com "A"
LIKE '%uber%'           -- contém "uber" em qualquer posição
LIKE '_a%'              -- segunda letra é "a" (_ = exatamente 1 caractere qualquer)
```

```sql
SELECT 
  d.driver_id,
  COUNT(t.trip_id) AS trip_num,
  AVG(t.fare) AS fare_avg
FROM 
  trips AS t 
JOIN 
  drivers AS d ON t.driver_id = d.driver_id
WHERE 
  d.email LIKE '%gmail.com'
  AND DATEDIFF('month', d.signup_date, '2026-08-30') <= 6
  AND (
    d.driver_name LIKE 'A%' OR 
    d.driver_name LIKE 'E%' OR 
    d.driver_name LIKE 'I%' OR
    d.driver_name LIKE 'O%' OR 
    d.driver_name LIKE 'U%'
  )
GROUP BY
  d.driver_id
ORDER BY 
  trip_num DESC;
```

### **LOWER / UPPER**

Converte string para totalmente minuscula ou maiuscula 

### **CONCAT**

Usando para concatenar varias coisas em string por exemplo: CONCAT('Soma é ', SUM(), ' mil dolares')

```sql 
SELECT 
  manufacturer,
  CONCAT('$',
    ROUND(
      SUM(total_sales) / 1000000
    ),
    ' million'
  ) AS sale
FROM 
  pharmacy_sales
GROUP BY
  manufacturer
ORDER BY 
  (SUM(total_sales) / 1000000) DESC,
  manufacturer ASC
```

