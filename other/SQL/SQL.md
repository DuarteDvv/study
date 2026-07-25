## **Ordem execução**

1. FROM (pega as tabelas)

2. WHERE (filtra linhas individuais)

3. GROUP BY (agrupa os dados)

4. HAVING (filtra os grupos formados)

5. SELECT (escolhe as colunas, calcula agregados e window functions)

6. ORDER BY (ordena o resultado)


## **INTERSECT** 

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

## **YEAR**

## **GROUP BY**

Agrupa as linhas de acordo com colunas especificadas. Todas as colunas que estiverem no SELECT e não forem uma agregação (media, max...) precisam também estar no GROUP BY

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
  
## **WINDOW FUNCTIONS**

### **ROW_NUMBER()**

### **OVER**

usado principalmente com ORDER BY e PARTITION BY


### PARTITION BY

### ORDER BY



