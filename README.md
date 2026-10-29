# Exercício Prático III — DAX aplicado ao Negócio

## Contexto

Você é o analista de dados da Olist, responsável por construir as métricas que alimentarão um dashboard estratégico no Power BI.

O modelo de dados segue um star schema com as tabelas:

- f_pedidos (Fato)  
- dim_clientes  
- dim_produtos  
- dim_vendedor  
- dim_reviews  

---

## Instruções

Para cada questão, responda obrigatoriamente:

### Etapa A — Raciocínio de negócio  
Explique o que calcular e como calcular, citando tabelas e colunas.

### Etapa B — Código DAX  
Escreva a medida ou coluna calculada.

---

# Bloco 1 — Volume e Operações

### Q01. Número total de pedidos  
Etapa A — Raciocínio:  
Para encontrar a quantidade total de pedidos efetuados no marketplace, contamos o total de linhas da tabela facto `f_pedidos`.

Etapa B — DAX:  
Total Pedidos = COUNTROWS(f_pedidos)

---

### Q02. Clientes únicos  
Etapa A — Raciocínio:  
Para identificar a quantidade de clientes distintos que efetuaram pelo menos uma compra, fazemos uma contagem distinta da coluna `id_cliente` na tabela `f_pedidos`.

Etapa B — DAX:  
Clientes Unicos = DISTINCTCOUNT(f_pedidos[id_cliente])

---

### Q03. Valor total faturado  
Etapa A — Raciocínio:  
Para obter o faturamento bruto da plataforma, somamos todos os valores da coluna `valor_pedido` na tabela `f_pedidos`.

Etapa B — DAX:  
Faturamento Total = SUM(f_pedidos[valor_pedido])

---

### Q04. Valor total de frete  
Etapa A — Raciocínio:  
Para calcular a receita total cobrada a título de frete, efetuamos a soma da coluna `valor_frete` da tabela `f_pedidos`.

Etapa B — DAX:  
Frete Total = SUM(f_pedidos[valor_frete])

---

### Q05. Produtos distintos vendidos  
Etapa A — Raciocínio:  
Para saber a variedade de produtos comercializados, fazemos a contagem distinta de `id_produto` dentro da tabela facto `f_pedidos`.

Etapa B — DAX:  
Produtos Distintos Vendidos = DISTINCTCOUNT(f_pedidos[id_produto])

---

### Q06. Vendedores distintos  
Etapa A — Raciocínio:  
Para contabilizar quantos parceiros/lojistas realizaram vendas, efetuamos uma contagem distinta do campo `id_vendedor` na tabela `f_pedidos`.

Etapa B — DAX:  
Vendedores Ativos = DISTINCTCOUNT(f_pedidos[id_vendedor])

---

### Q07. Ticket médio  
Etapa A — Raciocínio:  
O ticket médio é o valor médio faturado por cada pedido. Calculamos dividindo a medida de faturamento total (`[Faturamento Total]`) pela medida de quantidade de pedidos (`[Total Pedidos]`), utilizando a função `DIVIDE` para prevenir divisão por zero.

Etapa B — DAX:  
Ticket Medio = DIVIDE([Faturamento Total], [Total Pedidos], 0)

---

### Q08. Frete médio  
Etapa A — Raciocínio:  
Para saber o custo médio de frete por transação, dividimos o valor acumulado em fretes (`[Frete Total]`) pelo número total de pedidos (`[Total Pedidos]`).

Etapa B — DAX:  
Frete Medio = DIVIDE([Frete Total], [Total Pedidos], 0)

---

# Bloco 2 — Satisfação e Qualidade

### Q09. Nota média  
Etapa A — Raciocínio:  
Calculamos a média aritmética dos valores contidos na coluna `nota_avaliacao` pertencente à tabela `dim_reviews`.

Etapa B — DAX:  
Nota Media Geral = AVERAGE(dim_reviews[nota_avaliacao])

---

### Q10. Pedidos com nota 5  
Etapa A — Raciocínio:  
Para apurar o total de avaliações perfeitas, contamos as linhas da tabela `dim_reviews` filtrando apenas onde o campo `nota_avaliacao` é igual a 5.

Etapa B — DAX:  
Pedidos Nota 5 = CALCULATE(COUNTROWS(dim_reviews), dim_reviews[nota_avaliacao] = 5)

---

### Q11. Pedidos com nota 1 ou 2  
Etapa A — Raciocínio:  
Contamos o número de linhas da tabela `dim_reviews` filtrando os registos em que o campo `nota_avaliacao` é igual a 1 ou a 2.

Etapa B — DAX:  
Pedidos Nota Critica = CALCULATE(COUNTROWS(dim_reviews), dim_reviews[nota_avaliacao] IN {1, 2})

---

### Q12. % de nota 5  
Etapa A — Raciocínio:  
Dividimos o total de pedidos com nota máxima (`[Pedidos Nota 5]`) pelo total geral de avaliações na tabela `dim_reviews`.

Etapa B — DAX:  
% Pedidos Nota 5 = DIVIDE([Pedidos Nota 5], COUNTROWS(dim_reviews), 0)

---

### Q13. % de notas críticas  
Etapa A — Raciocínio:  
Dividimos a quantidade de pedidos insatisfeitos com notas 1 ou 2 (`[Pedidos Nota Critica]`) pelo número total de avaliações registadas na `dim_reviews`.

Etapa B — DAX:  
% Pedidos Criticos = DIVIDE([Pedidos Nota Critica], COUNTROWS(dim_reviews), 0)

---

### Q14. Classificação de avaliação (coluna calculada)  
Etapa A — Raciocínio:  
Em cada linha da tabela `dim_reviews`, avaliamos a nota com a função `SWITCH`: valores 4 ou 5 são rotulados como "Positiva", valor 3 como "Neutra" e valores 1 ou 2 como "Negativa".

Etapa B — DAX:  
categoria_avaliacao = SWITCH(TRUE(), dim_reviews[nota_avaliacao] >= 4, "Positiva", dim_reviews[nota_avaliacao] = 3, "Neutra", dim_reviews[nota_avaliacao] <= 2, "Negativa", "Sem Avaliação")

---

### Q15. Quantidade de avaliações negativas  
Etapa A — Raciocínio:  
Contamos as linhas da tabela `dim_reviews` aplicando o filtro da coluna calculada criada na Q14 em que `categoria_avaliacao` seja igual a "Negativa".

Etapa B — DAX:  
Pedidos Avaliacao Negativa = CALCULATE(COUNTROWS(dim_reviews), dim_reviews[categoria_avaliacao] = "Negativa")

---

# Bloco 3 — Logística

### Q16. Tempo de entrega (coluna)  
Etapa A — Raciocínio:  
Em cada linha da tabela `f_pedidos`, calculamos a diferença em dias entre a `data_compra` e a `data_entrega` através da função `DATEDIFF`.

Etapa B — DAX:  
tempo_entrega_dias = DATEDIFF(f_pedidos[data_compra], f_pedidos[data_entrega], DAY)

---

### Q17. Status de entrega (coluna)  
Etapa A — Raciocínio:  
Para cada registo na tabela `f_pedidos`, verificamos se a `data_entrega` é menor ou igual à `data_limite_envio`. Se sim, atribuímos "No prazo"; caso contrário, "Atrasado".

Etapa B — DAX:  
status_prazo = IF(f_pedidos[data_entrega] <= f_pedidos[data_limite_envio], "No prazo", "Atrasado")

---

### Q18. Tempo médio de entrega  
Etapa A — Raciocínio:  
Calculamos a média simples dos valores guardados na coluna calculada `tempo_entrega_dias` da tabela `f_pedidos`.

Etapa B — DAX:  
Tempo Medio Entrega = AVERAGE(f_pedidos[tempo_entrega_dias])

---

### Q19. Pedidos atrasados  
Etapa A — Raciocínio:  
Contamos as linhas da tabela `f_pedidos` filtrando apenas os registos em que a coluna `status_prazo` possui o valor "Atrasado".

Etapa B — DAX:  
Pedidos Atrasados = CALCULATE(COUNTROWS(f_pedidos), f_pedidos[status_prazo] = "Atrasado")

---

### Q20. % entregas no prazo  
Etapa A — Raciocínio:  
Contamos o número de pedidos cujo `status_prazo` é "No prazo" e dividimos pelo total de pedidos que já possuem uma `data_entrega` preenchida.

Etapa B — DAX:  
% Entregas no Prazo = DIVIDE(CALCULATE(COUNTROWS(f_pedidos), f_pedidos[status_prazo] = "No prazo"), CALCULATE(COUNTROWS(f_pedidos), NOT(ISBLANK(f_pedidos[data_entrega]))), 0)

---

### Q21. Tempo entre aprovação e envio  
Etapa A — Raciocínio:  
Utilizamos a função `AVERAGEX` na tabela `f_pedidos` para calcular linha a linha o número de dias entre `data_aprovacao` e `data_envio`, obtendo a média deste intervalo.

Etapa B — DAX:  
Tempo Medio Processamento Lojista = AVERAGEX(f_pedidos, DATEDIFF(f_pedidos[data_aprovacao], f_pedidos[data_envio], DAY))

---

### Q22. Prazo prometido (coluna)  
Etapa A — Raciocínio:  
Criamos uma coluna na tabela `f_pedidos` que utiliza `DATEDIFF` para medir a quantidade de dias previstos entre a `data_compra` e a `data_prevista_entrega`.

Etapa B — DAX:  
prazo_prometido_dias = DATEDIFF(f_pedidos[data_compra], f_pedidos[data_prevista_entrega], DAY)

---

### Q23. Peso médio dos produtos  
Etapa A — Raciocínio:  
Usamos a função `AVERAGEX` na tabela `f_pedidos`, procurando o peso em gramas do produto na tabela relacional `dim_produtos` através da função `RELATED`.

Etapa B — DAX:  
Peso Medio Produtos = AVERAGEX(f_pedidos, RELATED(dim_produtos[peso_g]))

---

# Bloco 4 — Segmentação

### Q24. Estado do cliente (coluna)  
Etapa A — Raciocínio:  
Adicionamos uma coluna calculada à tabela `f_pedidos` que importa a coluna `estado_cliente` da dimensão `dim_clientes` com o uso da função `RELATED`.

Etapa B — DAX:  
estado_cliente = RELATED(dim_clientes[estado_cliente])

---

### Q25. Pedidos de SP  
Etapa A — Raciocínio:  
Contamos o total de linhas da tabela `f_pedidos` onde o estado do cliente (`f_pedidos[estado_cliente]`) é igual a "SP".

Etapa B — DAX:  
Pedidos Cliente SP = CALCULATE(COUNTROWS(f_pedidos), f_pedidos[estado_cliente] = "SP")

---

### Q26. Faturamento Sudeste  
Etapa A — Raciocínio:  
Calculamos a medida `[Faturamento Total]` restringindo o contexto aos clientes localizados na Região Sudeste do Brasil ("SP", "RJ", "MG", "ES").

Etapa B — DAX:  
Faturamento Regiao Sudeste = CALCULATE([Faturamento Total], f_pedidos[estado_cliente] IN {"SP", "RJ", "MG", "ES"})

---

### Q27. Categoria do produto (coluna)  
Etapa A — Raciocínio:  
Inserimos uma coluna calculada na tabela `f_pedidos` para carregar a informação `categoria_produto` proveniente da tabela `dim_produtos` via `RELATED`.

Etapa B — DAX:  
categoria_produto = RELATED(dim_produtos[categoria_produto])

---

### Q28. Faturamento cama_mesa_banho  
Etapa A — Raciocínio:  
Filtramos a medida `[Faturamento Total]` para recalcular a receita proveniente unicamente de artigos da categoria "cama_mesa_banho".

Etapa B — DAX:  
Faturamento Cama Mesa Banho = CALCULATE([Faturamento Total], f_pedidos[categoria_produto] = "cama_mesa_banho")

---

### Q29. Pedidos de vendedores de SP  
Etapa A — Raciocínio:  
Apuramos a quantidade de vendas filtrando as transações cujo vendedor pertence ao estado de São Paulo (`dim_vendedor[estado_vendedor] = "SP"`).

Etapa B — DAX:  
Pedidos Vendedor SP = CALCULATE(COUNTROWS(f_pedidos), dim_vendedor[estado_vendedor] = "SP")

---

# Bloco 5 — OKRs

### Q30. % Entregas no Prazo  
Etapa A — Raciocínio:  
Reaproveitamos a medida de percentual de pontualidade formulada na Q20 para acompanhar o cumprimento da meta do KR1.

Etapa B — DAX:  
KR1_%_Entregas_No_Prazo = [% Entregas no Prazo]

---

### Q31. % Avaliações Negativas  
Etapa A — Raciocínio:  
Referenciamos a medida calculada na Q13/Q15 para medir a proporção de avaliações insatisfeitas (notas 1 e 2) sobre o total, acompanhando o KR2.

Etapa B — DAX:  
KR2_%_Avaliacoes_Negativas = [% Pedidos Criticos]

---

### Q32. Tempo Médio de Entrega  
Etapa A — Raciocínio:  
Utilizamos a medida de média de dias para entrega criada na Q18 para validar o teto do KR3.

Etapa B — DAX:  
KR3_Tempo_Medio_Entrega = [Tempo Medio Entrega]

---

### Q33. Nota Média (robusta)  
Etapa A — Raciocínio:  
Validamos com `IF` e `ISBLANK` se a contagem de avaliações não é vazia antes de efetuar o cálculo da média de notas da tabela `dim_reviews`.

Etapa B — DAX:  
KR4_Nota_Media_Avaliacao = IF(NOT(ISBLANK(COUNTROWS(dim_reviews))), AVERAGE(dim_reviews[nota_avaliacao]), BLANK())

---

### Q34. Nota média — pedidos atrasados  
Etapa A — Raciocínio:  
Calculamos a nota média de satisfação (`[KR4_Nota_Media_Avaliacao]`) filtrando exclusivamente os pedidos cujo status de entrega seja "Atrasado".

Etapa B — DAX:  
Nota Media Pedidos Atrasados = CALCULATE([KR4_Nota_Media_Avaliacao], f_pedidos[status_prazo] = "Atrasado")

Interpretação de negócio:  
A análise comparativa demonstra que os pedidos entregues com atraso apresentam uma nota média drasticamente menor em relação à média geral da plataforma. Isso comprova que a ineficiência logística é o principal gatilho de insatisfação do cliente e de avaliações negativas na Olist. Consequentemente, para atingir as metas de satisfação dos clientes (KR2 e KR4), a empresa deve focar primordialmente na otimização dos prazos de envio e transporte (KR1 e KR3).
