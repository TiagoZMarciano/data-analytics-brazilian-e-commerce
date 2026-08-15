# Data

This folder contains the raw CSV files from the Brazilian E-Commerce Public Dataset by Olist.

Original source:

https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

## Expected Files

The Power BI report expects the following files:

- `olist_customers_dataset.csv`
- `olist_geolocation_dataset.csv`
- `olist_orders_dataset.csv`
- `olist_order_items_dataset.csv`
- `olist_order_payments_dataset.csv`
- `olist_order_reviews_dataset.csv`
- `olist_products_dataset.csv`
- `olist_sellers_dataset.csv`
- `product_category_name_translation.csv`

## Notes

- The dataset is public and anonymized.
- Column names were preserved from the original CSV files.
- If the Power BI file asks for a data source path, update the source path to this local `data/` folder.
- Numeric columns such as `price`, `freight_value` and `payment_value` should be imported as decimal numbers.
- Date/time columns should be imported as date/time values.

## Data Size

The raw dataset is relatively large for a portfolio repository. If needed, the CSV files can be excluded from version control and downloaded directly from Kaggle.
