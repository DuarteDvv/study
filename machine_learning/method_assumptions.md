| Método | Premissas | Complexidade | 
| :--- | :----: | :---: |
| Regressão Linear via Equação Normal | Não Singularidade (Matriz tem que ser invertivel), ou seja, m > n e colunas (features) linearmente independentes (sem multicolinearidade); Dados de treino tem que caber na memória principal; Erro tem que ser gaussiano; Dados são lineares | O(n²m+n³) cubico em features devido as inversão;  |
| Regressão Linear via SVD | Trabalha com matrizes singulares (pseudo-inversa); Relação linear; Dados na RAM | O(n²m) - Mais estável que a Equação Normal, mas ainda pesada em colunas |
| Regressão Linear via Gradiente | Requer Escalonamento (Scaling); Funciona bem com Outliers se a taxa de aprendizado for baixa; Não precisa de todos os dados na RAM (Incremental) | O(k⋅n⋅m(batch)​) - Linear em features e linhas. Ideal para Big Data |
| Regressão Polinomial | Assume que a relação é não-linear (curva), mas os parâmetros são lineares; Requer cuidado com Overfitting (graus altos)| O(n^d⋅m) - Onde d é o grau do polinômio. O número de features explode rapidamente |
| Regressão Logistica via Gradiente | Classes linearmente separáveis; Independência das observações; Não requer linearidade entre X e Y, mas sim entre X e o Logit | O(k⋅n⋅m(batch)​) - Similar ao SGD linear. A função de custo é a Log Loss (convexa) |
| **** | *** | *** |



