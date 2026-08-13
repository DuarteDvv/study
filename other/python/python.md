# **Detalhes sutis de python**

## **for k in range(i,j,p):**

k vai variar começando exatamente em i, aumentar ou diminuir de acordo com p (passo) e finalizar quando for maior ou igual a j para passos positivos e menor ou igual para passos negativos. Para i = 1, j = 5 e p = 2 os numeros que k assume são [1,3]

```py
def fizz_buzz_sum(target):
  
  sum_ = 0
  for i in range(target):
    if i % 3 == 0 or i % 5 == 0 :
      sum_ += i
      
  return sum_
```

# **Complexidades de estruturas de dados**

## **Heap (Arvore binária)**

Uma arvore em que o nó pai é sempre maior/menor ou igual os filhos. 

- Se for maior é uma max-heap e se for menor é um min-heap.
- È uma arvore mas sua implementação geralmente é em um vetor usando indices para acessar os filhos (2*i +1 e 2*i +2)

### **Push ou Pop**

Ambos tem complexidade logn (altura da arvore) pois ao adicionar um nó ele pode se mover no pior caso até a raiz. Ao remover a raiz no pior caso uma folha pode ir para a raiz.

### **Consulta de max/min**

O(1) pois consiste em apenas olhar a raiz atual (arr[0]) da arvore

### **heapq.nsmallest(k,arr) e heapq.nlargest(k,arr)**

Funções prontas do heapq que permitem pegae os k maiores ou menores de um array. Para isso colocamos os k primeiros itens, ou seja, o heap sempre terá tamanho k e portanto todo push e pop tem custo logk. Depois passamos pelo restante dos itens:

- se queremos os k menores, criamos um max-heap e sempre que alguém menor que o maximo aparecer adicionamos ele no heap (push) e tiramos o maximo (pop).
- se queremos os k maiores, criamos um min-heap e sempre que alguém maior que o minimo aparecer adicionamos ele no heap (push) e tiramos o minimo (pop).

Ou seja, O(nlogk)

### **Espaço extra**

se a arvore for criada in-place gasta 0, caso contrário é O(n)

```py
def min_amplitude(arr): # O(nlogn) E:O(1)
  
  n = len(arr)
  
  if n <= 4: # podemos deixar todos os elementos iguais
    return 0
  
  arr.sort()
  
  # precisamos apenas dos 4 maiores elementos e 4 menores elementos para 
  # todas as possibilidades
  
  right3 = arr[n-4] - arr[0]
  left3 = arr[n-1] - arr[3]
  right2 = arr[n-3] - arr[1]
  left2 = arr[n-2] - arr[2]
  
      
  return min(right3, left3, left2, right2)
  
  import heapq

def min_amplitude(arr): # T:O(n) E:O(1) 
  
  n = len(arr)
  
  if n <= 4: 
    return 0
    
  smallests = heapq.nsmallest(4, arr) # crescente
  largests = heapq.nlargest(4,arr) # decrescente 
  largests.reverse() # crescente
    
  right3 = largests[0] - smallests[0]
  left3 = largests[3] - smallests[3]
  right2 = largests[1] - smallests[1]
  left2 = largests[2] - smallests[2]
  
  return min(right3, left3, left2, right2)
```


