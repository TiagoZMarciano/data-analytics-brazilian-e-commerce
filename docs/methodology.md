# Methodology

This project was developed as a complete business intelligence workflow in Power BI.

## 1. Dataset Understanding

The project uses the Brazilian E-Commerce Public Dataset by Olist, which includes order, item, payment, review, product, customer, seller and geolocation data.

Initial analysis focused on understanding:

- Table grain
- Join keys
- Date columns
- Revenue columns
- Review score behavior
- Customer and seller geography

## 2. Data Loading

All CSV files were loaded into Power BI with original column names preserved.

The main numeric columns used in calculations were:

- `price`
- `freight_value`
- `payment_value`
- `review_score`

The main date/time columns used were:

- `order_purchase_timestamp`
- `order_delivered_customer_date`
- `order_estimated_delivery_date`
- `review_creation_date`
- `review_answer_timestamp`

## 3. Semantic Modeling

The semantic model was built around `olist_orders_dataset` and `olist_order_items_dataset`.

A calendar table was added to support time-based analysis:

```DAX
Dim_Calendario =
CALENDAR(
    MIN(olist_orders_dataset[Data Compra]),
    MAX(olist_orders_dataset[Data Compra])
)
```

The model also includes a dedicated measure table, `Tabela_Medidas`, to centralize DAX and HTML measures.

## 4. Measure Development

Measures were created in layers:

1. Base measures: revenue, orders, customers, sellers, reviews
2. Operational measures: delivery time, delays, freight
3. Experience measures: review score, low reviews, delivery impact
4. HTML measures: page layout, cards, bars, tables and menu

This layered approach improves maintainability and makes debugging easier.

## 5. Visual Design

The dashboard was designed as a business-facing analytics product.

Design principles:

- Clean layout
- Strong visual hierarchy
- Limited color palette
- Consistent spacing
- KPI-first reading
- Business-oriented copy
- No implementation language in final pages

The final report uses HTML/CSS inside Power BI to create a custom visual experience while preserving Power BI filtering and navigation behavior.

## 6. Page Development

The report was structured into six pages:

1. Landing Page
2. Executive Overview
3. Sales and Categories
4. Logistics
5. Customers and Sellers
6. Satisfaction

Each page answers a specific business question and uses focused metrics instead of excessive visual density.

## 7. Validation And Refinement

During development, several issues were identified and corrected:

- Category GMV repeating total values due to filter propagation.
- Order status counts repeating the same value due to missing context transition.
- Median delivery time error caused by date/duration expressions.
- Category review score repeating because product category context did not reach reviews.
- Layout overflow in HTML visuals after adding the left navigation menu.

Key corrections included:

- Using item-level GMV for category analysis.
- Applying `CALCULATE` inside `ADDCOLUMNS`.
- Using `DATEDIFF(..., HOUR) / 24` for date interval calculations.
- Using `TREATAS` to connect category orders to reviews.
- Adding responsive CSS and scroll containers for dense rankings.

## 8. Final Deliverables

Final assets:

- Power BI report: `Dashboard of Brazilian E-Commerce by Olist.pbix`
- PDF export: `Dashboard of Brazilian E-Commerce by Olist.pdf`
- Semantic model screenshot: `Modelo_Semantico_Dashboard.jpg`
- Documentation: `README.md` and `docs/`

## 9. Future Roadmap

Possible next steps:

- Build a seller detail page.
- Add customer segmentation.
- Add review text classification.
- Create geographic distance metrics.
- Compare promised delivery time versus actual performance by route.
- Publish the dashboard to Power BI Service.
