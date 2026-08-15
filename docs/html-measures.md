# HTML Measures

This dashboard uses the HTML Content custom visual to render custom dashboard pages directly from DAX measures.

The final report uses one HTML measure per page plus reusable component measures for CSS, cards, rankings, tables and insights.

## Page Measures

| Measure | Page |
|---|---|
| `HTML Capa Dashboard` | Landing Page |
| `HTML Pagina Visao Executiva` | Executive Overview |
| `HTML Pagina Vendas Categorias` | Sales and Categories |
| `HTML Pagina Logistica` | Logistics |
| `HTML Pagina Clientes Sellers` | Customers and Sellers |
| `HTML Pagina Satisfacao` | Satisfaction |

## Menu Measures

The left navigation is rendered with one menu measure per page. The active page is highlighted by changing the `active` class.

| Measure | Active Item |
|---|---|
| `HTML Menu - 01 Visao Executiva` | Executive Overview |
| `HTML Menu - 02 Vendas Categorias` | Sales and Categories |
| `HTML Menu - 03 Logistica` | Logistics |
| `HTML Menu - 04 Clientes Sellers` | Customers and Sellers |
| `HTML Menu - 05 Satisfacao` | Satisfaction |

Navigation is handled with transparent native Power BI buttons placed over the HTML menu items.

## Shared Menu Pattern

```DAX
HTML Menu CSS =
"
<style>
* {
    box-sizing: border-box;
}

body {
    margin: 0;
    font-family: Segoe UI, Arial, sans-serif;
    overflow: hidden;
}

.side-menu {
    width: 100%;
    height: 100%;
    min-height: 720px;
    background: linear-gradient(180deg, #102728 0%, #143234 55%, #1f2937 100%);
    padding: 18px 8px;
    color: #ECFDF5;
}

.menu-label {
    margin: 0 10px 10px;
    color: rgba(236, 253, 245, 0.68);
    font-size: 11px;
    font-weight: 700;
    letter-spacing: 0.06em;
    text-transform: uppercase;
}

.menu-item {
    display: flex;
    align-items: center;
    gap: 10px;
    min-height: 42px;
    padding: 0 12px;
    margin-bottom: 5px;
    color: #FFFFFF;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
}

.menu-item.active {
    background: rgba(255,255,255,0.12);
    border: 1px solid rgba(255,255,255,0.08);
}

.menu-number {
    width: 20px;
    height: 20px;
    display: grid;
    place-items: center;
    color: #99F6E4;
    border: 1px solid rgba(153, 246, 228, 0.50);
    border-radius: 6px;
    font-size: 10px;
    font-weight: 800;
}

.menu-text {
    white-space: nowrap;
}
</style>
"
```

## Page Composition Example

The Sales and Categories page combines CSS, category ranking, payment composition and commercial priorities.

```DAX
HTML Pagina Vendas Categorias =
[HTML CSS Vendas Categorias]
&
"
<div class='dashboard-page'>
    <div class='page-title'>Brazilian E-Commerce Dashboard</div>
    <div class='page-subtitle'>Analise de receita, categorias, meios de pagamento e oportunidades comerciais.</div>

    <div class='grid-sales'>
        <div>
"
&
[HTML Vendas - Top Categorias]
&
"
        </div>

        <div>
"
&
[HTML Vendas - Meios Pagamento]
&
"
        </div>
    </div>

"
&
[HTML Vendas - Prioridades Comerciais]
&
"
</div>
"
```

## Component Pattern - Ranking Bars

The ranking components use `TOPN`, `ADDCOLUMNS`, `CALCULATE` and `CONCATENATEX` to produce HTML rows dynamically.

```DAX
HTML Logistica - Status Pedidos =
VAR BaseStatus =
    ADDCOLUMNS(
        SUMMARIZE(
            olist_orders_dataset,
            olist_orders_dataset[order_status]
        ),
        "QtdPedidos",
            CALCULATE(
                [Total Pedidos]
            )
    )

VAR MaxStatus =
    MAXX(BaseStatus, [QtdPedidos])

RETURN
"
<div class='panel'>
    <div class='panel-title'>Status dos pedidos</div>
    <div class='panel-subtitle'>Distribuicao operacional da base.</div>
"
&
CONCATENATEX(
    BaseStatus,
    VAR StatusPedido = olist_orders_dataset[order_status]
    VAR QtdPedidos = [QtdPedidos]
    VAR Largura = DIVIDE(QtdPedidos, MaxStatus) * 100
    VAR Cor =
        SWITCH(
            TRUE(),
            StatusPedido = "delivered", "#0F766E",
            StatusPedido = "canceled", "#DC2626",
            StatusPedido = "unavailable", "#F59E0B",
            "#2563EB"
        )
    RETURN
    "
    <div class='bar-row'>
        <div class='bar-name'>" & StatusPedido & "</div>
        <div class='bar-track'>
            <div class='bar-fill' style='width:" & FORMAT(Largura, "0") & "%; background:" & Cor & ";'></div>
        </div>
        <div class='bar-value'>" & FORMAT(QtdPedidos, "#,##0") & "</div>
    </div>
    ",
    "",
    [QtdPedidos],
    DESC
)
&
"</div>"
```

## Design Notes

- CSS is embedded in DAX measures to keep the report portable inside the `.pbix`.
- Visual hierarchy is driven by page title, KPI row, primary analysis and supporting insights.
- Colors are used consistently: blue for primary values, green for positive outcomes, red for negative outcomes and orange for attention points.
- Scroll containers were added in dense rankings to preserve layout without hiding analytical content.
- Final user-facing copy avoids implementation language such as "Power BI", "measure", "visual" or "proof of concept".

The `.pbix` file is the source of truth for the complete final HTML measures.
