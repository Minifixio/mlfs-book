# Air Quality Prediction Using MLOps Framework

You can find the dashboard [here](https://minifixio.github.io/mlfs-book/).

This project forecasts PM2.5 levels for the next 7–10 days by integrating weather data and historical air quality data into an automated MLOps pipeline. An enhancement to the initial project was the inclusion of a new feature, **lagged air quality for the previous 1–3 days**, to capture temporal dependencies and improve prediction accuracy.


## Notebooks Overview

1. **`1_air_quality_feature_backfill.ipynb`**  
   This notebook initializes the pipeline by downloading historical weather and air quality data from external sources. It processes these datasets and registers them as Feature Groups in Hopsworks, enabling their use in model training and inference.

2. **`2_air_quality_feature_pipeline.ipynb`**  
   This notebook runs daily to ingest recent air quality and weather data and update Feature Groups. It also retrieves weather forecasts for the next 7–10 days, ensuring the pipeline has the latest data for predictions.

3. **`3_air_quality_training_pipeline.ipynb`**  
   This notebook manages model training. It selects features from the Feature Groups, creates training datasets, trains a regression model to predict PM2.5, evaluates its performance, and registers the trained model in Hopsworks for deployment.

4. **`4_air_quality_batch_inference.ipynb`**  
   Running daily, this notebook performs batch predictions for PM2.5 levels over the next 7–10 days using the trained model and weather forecasts. It stores predictions in Hopsworks and generates visualizations, including a forecast dashboard and a hindcast graph to monitor accuracy.

## Changes to Implement Lagged Air Quality Feature

The addition of `pm25_lagging_3_days` required updates across all stages of the pipeline, including feature backfilling, daily updates, model training, and inference.

### Backfill and Daily Updates
In the initial backfill, the lagged feature was computed for historical data using a 3-day rolling mean shifted by one day to avoid data leakage. During daily updates, the pipeline calculated `pm25_lagging_3_days` dynamically by retrieving the last three days of PM2.5 data from Hopsworks and averaging them. Both historical and daily values were stored in the air quality Feature Group.

### Model Training
The training process was modified to include the lagged feature alongside weather features. This involved selecting `pm25_lagging_3_days` from the air quality Feature Group and joining it with weather data. The model, an `XGBRegressor`, was trained on this expanded feature set to leverage both temporal and meteorological factors.

### Batch Inference
During inference, the lack of future PM2.5 data required an iterative approach. The pipeline initialized predictions using the last three days of observed PM2.5 values. For each forecasted day, it combined historical and previous predictions to compute `pm25_lagging_3_days` dynamically. This feature, along with weather forecasts, was used to predict PM2.5 for the day, iterating forward to generate a full forecast. Predictions were stored in a monitoring Feature Group and visualized in forecast and hindcast dashboards.


## Summary
Incorporating `pm25_lagging_3_days` improved the pipeline by adding temporal context to the model. Key updates included feature engineering during data backfilling, dynamic computation of lagged values during inference, and seamless integration into the training process. These changes enhanced the pipeline’s ability to provide accurate, actionable PM2.5 predictions.

GitHub Actions automated the execution of daily updates and inference pipelines, ensuring timely data refreshes and predictions and publish it to a GitHub pages.

<br>
<br>
<br>

--- 
# mlfs-book
O'Reilly book - Building Machine Learning Systems with a feature store: batch, real-time, and LLMs


## ML System Examples


[Dashboards for Example ML Systems](https://featurestorebook.github.io/mlfs-book/)

## Course Comparison

| Course                         | MLOps | LLLMs             | Feature/Training/Inference | Working AI Systems | Focus |
|--------------------------------|-------|----------------------------|--------------------|------------------|
| Building AI Systems (O'Reilly) | Yes   | Fine-Tuning & RAG | Yes                        | High               | Project-based, Software Engineering, Fundamentals    |
| [Made With ML](https://madewithml.com/)                   | No          | Yes   | No                         | No                 | Software Engineering, Model Training   |
| [7 Steps MLOps](https://www.pauliusztin.me/courses/the-full-stack-7-steps-mlops-framework)            | Yes   | Separate Course    | Yes                        | Low                | Learning Tools and Project    |
