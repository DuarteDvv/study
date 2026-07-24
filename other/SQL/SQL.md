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

## **GROUP BY**

Agrupa as linhas de acordo com colunas especificadas. Todas as colunas que estiverem no SELECT e não forem uma agregação (media, max...) precisam também estar no GROUP BY

### **Funções de agregação**

#### **Agregação Condicional**

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
``
  




