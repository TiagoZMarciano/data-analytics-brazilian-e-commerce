# DAX Measures

This document summarizes the main DAX measures used in the Power BI dashboard.

The model keeps business measures in `Tabela_Medidas`, organized by report section.

## Core Sales Measures

```DAX
GMV Itens =
SUMX(
    olist_order_items_dataset,
    olist_order_items_dataset[price]
        + olist_order_items_dataset[freight_value]
)
```

```DAX
Receita Produtos =
SUM(olist_order_items_dataset[price])
```

```DAX
Valor Frete =
SUM(olist_order_items_dataset[freight_value])
```

```DAX
Pedidos Itens =
DISTINCTCOUNT(olist_order_items_dataset[order_id])
```

```DAX
Ticket Medio Itens =
DIVIDE(
    [GMV Itens],
    [Pedidos Itens]
)
```

```DAX
GMV Pagamentos =
SUM(olist_order_payments_dataset[payment_value])
```

## Customer And Seller Measures

```DAX
Total Clientes =
DISTINCTCOUNT(olist_customers_dataset[customer_id])
```

```DAX
Clientes Unicos =
DISTINCTCOUNT(olist_customers_dataset[customer_unique_id])
```

```DAX
Clientes Recorrentes Estimado =
[Total Clientes] - [Clientes Unicos]
```

```DAX
% Clientes Recorrentes Estimado =
DIVIDE(
    [Clientes Recorrentes Estimado],
    [Clientes Unicos]
)
```

```DAX
Total Sellers =
DISTINCTCOUNT(olist_sellers_dataset[seller_id])
```

```DAX
GMV Por Cliente =
DIVIDE(
    [GMV Itens],
    [Clientes Unicos]
)
```

```DAX
GMV Por Seller =
DIVIDE(
    [GMV Itens],
    [Total Sellers]
)
```

## Logistics Measures

```DAX
Total Pedidos =
DISTINCTCOUNT(olist_orders_dataset[order_id])
```

```DAX
Pedidos Entregues =
CALCULATE(
    [Total Pedidos],
    olist_orders_dataset[order_status] = "delivered"
)
```

```DAX
Tempo Medio Entrega Dias =
AVERAGEX(
    FILTER(
        olist_orders_dataset,
        NOT ISBLANK(olist_orders_dataset[order_purchase_timestamp])
            && NOT ISBLANK(olist_orders_dataset[order_delivered_customer_date])
    ),
    DIVIDE(
        DATEDIFF(
            olist_orders_dataset[order_purchase_timestamp],
            olist_orders_dataset[order_delivered_customer_date],
            HOUR
        ),
        24
    )
)
```

```DAX
Tempo Mediano Entrega Dias =
MEDIANX(
    FILTER(
        olist_orders_dataset,
        NOT ISBLANK(olist_orders_dataset[order_purchase_timestamp])
            && NOT ISBLANK(olist_orders_dataset[order_delivered_customer_date])
    ),
    DIVIDE(
        DATEDIFF(
            olist_orders_dataset[order_purchase_timestamp],
            olist_orders_dataset[order_delivered_customer_date],
            HOUR
        ),
        24
    )
)
```

```DAX
Pedidos Atrasados =
CALCULATE(
    [Total Pedidos],
    FILTER(
        olist_orders_dataset,
        olist_orders_dataset[order_status] = "delivered"
            && NOT ISBLANK(olist_orders_dataset[order_delivered_customer_date])
            && NOT ISBLANK(olist_orders_dataset[order_estimated_delivery_date])
            && olist_orders_dataset[order_delivered_customer_date]
                > olist_orders_dataset[order_estimated_delivery_date]
    )
)
```

```DAX
% Pedidos Atrasados =
DIVIDE(
    [Pedidos Atrasados],
    [Pedidos Entregues]
)
```

```DAX
% Entregas No Prazo =
1 - [% Pedidos Atrasados]
```

```DAX
Atraso Medio Dias =
AVERAGEX(
    FILTER(
        olist_orders_dataset,
        olist_orders_dataset[order_status] = "delivered"
            && NOT ISBLANK(olist_orders_dataset[order_delivered_customer_date])
            && NOT ISBLANK(olist_orders_dataset[order_estimated_delivery_date])
            && olist_orders_dataset[order_delivered_customer_date]
                > olist_orders_dataset[order_estimated_delivery_date]
    ),
    DIVIDE(
        DATEDIFF(
            olist_orders_dataset[order_estimated_delivery_date],
            olist_orders_dataset[order_delivered_customer_date],
            HOUR
        ),
        24
    )
)
```

```DAX
Frete Medio Pedido =
DIVIDE(
    [Valor Frete],
    [Pedidos Itens]
)
```

## Satisfaction Measures

```DAX
Total Reviews =
COUNTROWS(olist_order_reviews_dataset)
```

```DAX
Nota Media =
AVERAGE(olist_order_reviews_dataset[review_score])
```

```DAX
Reviews 5 Estrelas =
CALCULATE(
    [Total Reviews],
    olist_order_reviews_dataset[review_score] = 5
)
```

```DAX
% Reviews 5 Estrelas =
DIVIDE(
    [Reviews 5 Estrelas],
    [Total Reviews]
)
```

```DAX
Reviews Baixas =
CALCULATE(
    [Total Reviews],
    olist_order_reviews_dataset[review_score] <= 2
)
```

```DAX
% Reviews Baixas =
DIVIDE(
    [Reviews Baixas],
    [Total Reviews]
)
```

```DAX
Reviews Com Comentario =
CALCULATE(
    [Total Reviews],
    NOT ISBLANK(olist_order_reviews_dataset[review_comment_message])
)
```

```DAX
% Reviews Com Comentario =
DIVIDE(
    [Reviews Com Comentario],
    [Total Reviews]
)
```

```DAX
Tempo Medio Resposta Review Dias =
AVERAGEX(
    FILTER(
        olist_order_reviews_dataset,
        NOT ISBLANK(olist_order_reviews_dataset[review_creation_date])
            && NOT ISBLANK(olist_order_reviews_dataset[review_answer_timestamp])
    ),
    DIVIDE(
        DATEDIFF(
            olist_order_reviews_dataset[review_creation_date],
            olist_order_reviews_dataset[review_answer_timestamp],
            HOUR
        ),
        24
    )
)
```

## Delivery Impact On Review Score

```DAX
Nota Media Pedidos Atrasados =
CALCULATE(
    [Nota Media],
    FILTER(
        olist_orders_dataset,
        olist_orders_dataset[order_status] = "delivered"
            && NOT ISBLANK(olist_orders_dataset[order_delivered_customer_date])
            && NOT ISBLANK(olist_orders_dataset[order_estimated_delivery_date])
            && olist_orders_dataset[order_delivered_customer_date]
                > olist_orders_dataset[order_estimated_delivery_date]
    )
)
```

```DAX
Nota Media Pedidos No Prazo =
CALCULATE(
    [Nota Media],
    FILTER(
        olist_orders_dataset,
        olist_orders_dataset[order_status] = "delivered"
            && NOT ISBLANK(olist_orders_dataset[order_delivered_customer_date])
            && NOT ISBLANK(olist_orders_dataset[order_estimated_delivery_date])
            && olist_orders_dataset[order_delivered_customer_date]
                <= olist_orders_dataset[order_estimated_delivery_date]
    )
)
```

```DAX
Impacto Atraso Nota =
[Nota Media Pedidos No Prazo] - [Nota Media Pedidos Atrasados]
```

## Category Review Score Fix

The category score required explicit propagation from product category to order reviews.

```DAX
Nota Media Categoria =
VAR PedidosDaCategoria =
    CALCULATETABLE(
        VALUES(olist_order_items_dataset[order_id]),
        CROSSFILTER(
            olist_products_dataset[product_id],
            olist_order_items_dataset[product_id],
            BOTH
        )
    )

RETURN
CALCULATE(
    [Nota Media],
    TREATAS(
        PedidosDaCategoria,
        olist_order_reviews_dataset[order_id]
    )
)
```

```DAX
Reviews Categoria =
VAR PedidosDaCategoria =
    CALCULATETABLE(
        VALUES(olist_order_items_dataset[order_id]),
        CROSSFILTER(
            olist_products_dataset[product_id],
            olist_order_items_dataset[product_id],
            BOTH
        )
    )

RETURN
CALCULATE(
    [Total Reviews],
    TREATAS(
        PedidosDaCategoria,
        olist_order_reviews_dataset[order_id]
    )
)
```
