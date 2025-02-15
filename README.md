
https://accounts.google.com/AddSession?service=accountsettings&sarp=1&continue=https%3A%2F%2Fconsole.cloud.google.com%2Fhome%2Fdashboard%3Fproject%3Dqwiklabs-gcp-04-86619f2a752a#Email=student-03-312f3bb96fa1@qwiklabs.net

student-03-312f3bb96fa1@qwiklabs.net

Ax0Q3xH0qvqE

qwiklabs-gcp-04-86619f2a752a

Task 1

PROJECT_ID=$(gcloud config get-value project)
REGION=us-central1
echo "PROJECT_ID=${PROJECT_ID}"
echo "REGION=${REGION}"


USER=$(gcloud config get-value account 2> /dev/null)
echo "USER=${USER}"

gcloud services enable cloudaicompanion.googleapis.com --project ${PROJECT_ID}

gcloud projects add-iam-policy-binding ${PROJECT_ID} --member user:${USER} --role=roles/cloudaicompanion.user
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member user:${USER} --role=roles/serviceusage.serviceUsageViewer


Task 3

How do I learn which datasets and tables are available to me in BigQuery?

Task 4 

SELECT u.id as user_id, u.first_name, u.last_name, avg(oi.sale_price) as avg_sale_price   
FROM `bigquery-public-data.thelook_ecommerce.users` as u   
JOIN `bigquery-public-data.thelook_ecommerce.order_items` as oi   
ON u.id = oi.user_id   
GROUP BY 1,2,3   
ORDER BY avg_sale_price DESC   
LIMIT 10

Task 5 

# select the sum of sale_price by Date(created_at) and product_id casted to day from bigquery-public-data.thelook_ecommerce.order_id as t1 joined this with products table in the same dataset as t2

# select the sum of sale_price by Date(created_at) and product_id casted to day from bigquery-public-data.thelook_ecommerce.order_id as t1 joined this with products table in the same dataset as t2
SELECT
  SUM(sale_price),
  DATE(created_at) AS created_at_day,
  CAST(product_id as INT64)
FROM
  `bigquery-public-data.thelook_ecommerce.order_items` AS t1
JOIN
  `bigquery-public-data.thelook_ecommerce.products` AS t2 ON t1.product_id = t2.id
GROUP BY
  created_at_day,
  product_id

Task 6 

CREATE MODEL bqml_tutorial.sales_forecasting_model
OPTIONS(MODEL_TYPE='ARIMA_PLUS',
time_series_timestamp_col='date_col',
time_series_data_col='total_sales',
time_series_id_col='product_id') AS
SELECT sum(sale_price) as total_sales,
DATE(created_at) as date_col,
product_id
FROM `bigquery-public-data.thelook_ecommerce.order_items`
AS t1
INNER JOIN `bigquery-public-data.thelook_ecommerce.products`
AS t2
ON t1.product_id = t2.id
GROUP BY 2, 3;

To get a forecast in SQL from the model, you can use the following query:

SELECT
*
FROM
  ML.FORECAST(MODEL `PROJECT_ID.DATASET_ID.MODEL_NAME`,
STRUCT(
      7 AS horizon,
      0.95 AS confidence_level
)
)


  
