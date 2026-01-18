# Data-Driven Feature Workflow - GitHub Copilot Instructions

## Overview

This workflow orchestrates building features that leverage data pipelines, analytics, and machine learning. It coordinates data scientists, data engineers, backend architects, and AI engineers to create complete data-driven solutions from analysis to production deployment.

## When to Use

**Prompt GitHub Copilot with:**
- "Build product recommendation system using data-driven-feature workflow"
- "Implement user analytics dashboard with data pipeline and ML"
- "Create fraud detection feature using data-driven approach"
- "Develop personalization engine with data pipelines and ML models"

## Workflow Phases

### Phase 1: Data Analysis and Design

#### Step 1: Data Requirements Analysis

**Prompt GitHub Copilot:**
"Analyze data requirements for [feature]. Identify data sources (databases, APIs, files), required transformations, aggregations, analytics needs, and potential ML opportunities. Provide data analysis report, feature engineering requirements, and ML feasibility assessment."

Data analysis deliverables:

**Data Source Identification**:
- Transactional databases
- Analytics databases (data warehouse)
- Third-party APIs
- Log files and event streams
- External data sources

**Data Profiling**:
```python
import pandas as pd

def profile_data(df: pd.DataFrame) -> dict:
    """Profile dataset for quality and characteristics"""
    profile = {
        'shape': df.shape,
        'dtypes': df.dtypes.to_dict(),
        'missing_values': df.isnull().sum().to_dict(),
        'duplicates': df.duplicated().sum(),
        'numeric_stats': df.describe().to_dict(),
        'categorical_stats': {
            col: df[col].value_counts().to_dict()
            for col in df.select_dtypes(include=['object']).columns
        }
    }
    return profile
```

**Feature Engineering Needs**:
- Derived features (calculations, aggregations)
- Time-based features (day of week, hour, seasonality)
- Categorical encoding (one-hot, label encoding)
- Text features (TF-IDF, embeddings)
- Feature scaling and normalization

**ML Feasibility**:
- Is there enough data? (thousands to millions of examples)
- Is the target variable defined?
- Is there signal in the data?
- What ML task? (classification, regression, clustering, ranking)
- Potential algorithms and approaches

#### Step 2: Data Pipeline Architecture

**Prompt GitHub Copilot:**
"Design data pipeline architecture for [feature]. Include ETL/ELT processes, data storage strategy (data warehouse/lake), streaming requirements (Kafka/Kinesis), batch processing (Airflow/Prefect), and integration with existing systems. Use data scientist's analysis to inform design."

Pipeline architecture components:

**Data Ingestion**:
```python
# Example: Data ingestion from multiple sources
from typing import List, Dict
import pandas as pd
from sqlalchemy import create_engine

class DataIngestion:
    def __init__(self, config: Dict):
        self.config = config
        self.sources = config['sources']
    
    def ingest_from_database(self, query: str, db_config: Dict) -> pd.DataFrame:
        """Ingest data from relational database"""
        engine = create_engine(db_config['connection_string'])
        return pd.read_sql(query, engine)
    
    def ingest_from_api(self, endpoint: str, params: Dict) -> pd.DataFrame:
        """Ingest data from REST API"""
        import requests
        response = requests.get(endpoint, params=params)
        return pd.DataFrame(response.json())
    
    def ingest_from_files(self, file_paths: List[str], file_format: str) -> pd.DataFrame:
        """Ingest data from files (CSV, Parquet, JSON)"""
        if file_format == 'csv':
            return pd.concat([pd.read_csv(f) for f in file_paths])
        elif file_format == 'parquet':
            return pd.concat([pd.read_parquet(f) for f in file_paths])
        elif file_format == 'json':
            return pd.concat([pd.read_json(f) for f in file_paths])
```

**Data Transformation**:
- Data cleaning (missing values, outliers)
- Data validation
- Feature engineering
- Data enrichment
- Aggregations and joins

**Data Storage**:
- Operational database (PostgreSQL, MySQL)
- Data warehouse (Snowflake, BigQuery, Redshift)
- Data lake (S3, GCS, Azure Data Lake)
- Feature store (Feast, Tecton)
- Cache layer (Redis)

**Orchestration**:
```python
# Example: Airflow DAG for data pipeline
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'data-team',
    'depends_on_past': False,
    'start_date': datetime(2024, 1, 1),
    'email_on_failure': True,
    'email_on_retry': False,
    'retries': 2,
    'retry_delay': timedelta(minutes=5)
}

dag = DAG(
    'user_analytics_pipeline',
    default_args=default_args,
    description='Process user analytics data',
    schedule_interval='@hourly',
    catchup=False
)

# Tasks
extract_task = PythonOperator(
    task_id='extract_data',
    python_callable=extract_user_data,
    dag=dag
)

transform_task = PythonOperator(
    task_id='transform_data',
    python_callable=transform_user_data,
    dag=dag
)

load_task = PythonOperator(
    task_id='load_to_warehouse',
    python_callable=load_to_warehouse,
    dag=dag
)

# Dependencies
extract_task >> transform_task >> load_task
```

### Phase 2: Backend Integration

#### Step 3: API and Service Design

**Prompt GitHub Copilot:**
"Design backend services to support data-driven feature: [feature]. Include APIs for data ingestion, analytics endpoints, real-time predictions, batch processing triggers, and ML model serving. Build on pipeline architecture from previous step."

Backend service architecture:

**Data Ingestion API**:
```python
from fastapi import FastAPI, BackgroundTasks
from pydantic import BaseModel

app = FastAPI()

class EventData(BaseModel):
    user_id: str
    event_type: str
    properties: dict
    timestamp: str

@app.post("/api/v1/events/track")
async def track_event(event: EventData, background_tasks: BackgroundTasks):
    """Track user events for analytics"""
    # Validate and enrich event
    enriched_event = enrich_event(event)
    
    # Send to streaming pipeline (Kafka)
    background_tasks.add_task(send_to_kafka, enriched_event)
    
    # Store in operational database
    background_tasks.add_task(store_event, enriched_event)
    
    return {"status": "accepted", "event_id": enriched_event.id}
```

**Analytics API**:
```python
@app.get("/api/v1/analytics/users/{user_id}/summary")
async def get_user_analytics(user_id: str, period: str = "7d"):
    """Get user analytics summary"""
    # Query from data warehouse
    analytics = await analytics_service.get_user_summary(user_id, period)
    
    return {
        "user_id": user_id,
        "period": period,
        "metrics": {
            "total_events": analytics['event_count'],
            "active_days": analytics['active_days'],
            "top_actions": analytics['top_actions'],
            "engagement_score": analytics['engagement_score']
        }
    }
```

**ML Prediction API**:
```python
@app.post("/api/v1/recommendations/users/{user_id}")
async def get_recommendations(user_id: str, limit: int = 10):
    """Get personalized recommendations using ML model"""
    # Get user features
    user_features = await feature_store.get_user_features(user_id)
    
    # Get predictions from model
    predictions = await ml_service.predict(user_features, top_k=limit)
    
    return {
        "user_id": user_id,
        "recommendations": predictions,
        "model_version": ml_service.model_version
    }
```

#### Step 4: Database and Storage Design

**Prompt GitHub Copilot:**
"Design optimal database schema and storage strategy for [feature]. Consider both transactional (OLTP) and analytical (OLAP) workloads, time-series data, ML feature stores, and caching strategy. Provide database schemas, indexing strategies, and partitioning recommendations."

Storage strategy:

**Transactional Database** (PostgreSQL):
```sql
-- Events table for real-time tracking
CREATE TABLE events (
    id BIGSERIAL PRIMARY KEY,
    user_id UUID NOT NULL,
    event_type VARCHAR(50) NOT NULL,
    properties JSONB,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_events_user_id ON events(user_id);
CREATE INDEX idx_events_created_at ON events(created_at);
CREATE INDEX idx_events_type ON events(event_type);
CREATE INDEX idx_events_properties ON events USING GIN(properties);

-- Partition by month for performance
CREATE TABLE events_2024_01 PARTITION OF events
FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
```

**Feature Store**:
```python
# Using Feast for feature store
from feast import Entity, Feature, FeatureView, FileSource, ValueType
from datetime import timedelta

user = Entity(name="user_id", value_type=ValueType.STRING)

user_features_source = FileSource(
    path="data/user_features.parquet",
    event_timestamp_column="event_timestamp"
)

user_features = FeatureView(
    name="user_features",
    entities=["user_id"],
    ttl=timedelta(days=7),
    features=[
        Feature(name="total_purchases", dtype=ValueType.INT64),
        Feature(name="avg_order_value", dtype=ValueType.DOUBLE),
        Feature(name="days_since_last_purchase", dtype=ValueType.INT64),
        Feature(name="favorite_category", dtype=ValueType.STRING)
    ],
    source=user_features_source
)
```

### Phase 3: ML and AI Implementation

#### Step 5: ML Pipeline Development

**Prompt GitHub Copilot:**
"Implement ML pipeline for [feature]. Include feature engineering, model training with experiment tracking (MLflow/W&B), validation, hyperparameter tuning, and deployment strategy. Use data scientist's requirements and feature store design."

ML pipeline implementation:

**Feature Engineering**:
```python
import pandas as pd
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.compose import ColumnTransformer

class FeatureEngineer:
    def __init__(self):
        self.preprocessor = None
    
    def fit_transform(self, df: pd.DataFrame) -> pd.DataFrame:
        """Create and transform features"""
        # Create time-based features
        df['hour'] = df['timestamp'].dt.hour
        df['day_of_week'] = df['timestamp'].dt.dayofweek
        df['is_weekend'] = df['day_of_week'].isin([5, 6])
        
        # Aggregate features
        df['purchase_count_7d'] = df.groupby('user_id')['purchase'].transform(
            lambda x: x.rolling('7D').sum()
        )
        
        # Define preprocessing
        numeric_features = ['age', 'total_spent', 'purchase_count_7d']
        categorical_features = ['country', 'device_type']
        
        self.preprocessor = ColumnTransformer([
            ('num', StandardScaler(), numeric_features),
            ('cat', OneHotEncoder(handle_unknown='ignore'), categorical_features)
        ])
        
        return self.preprocessor.fit_transform(df)
```

**Model Training with MLflow**:
```python
import mlflow
import mlflow.sklearn
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import cross_val_score

mlflow.set_experiment("user_churn_prediction")

with mlflow.start_run(run_name="rf_model_v1"):
    # Log parameters
    mlflow.log_param("n_estimators", 100)
    mlflow.log_param("max_depth", 10)
    mlflow.log_param("min_samples_split", 5)
    
    # Train model
    model = RandomForestClassifier(
        n_estimators=100,
        max_depth=10,
        min_samples_split=5,
        random_state=42
    )
    
    # Cross-validation
    cv_scores = cross_val_score(model, X_train, y_train, cv=5, scoring='roc_auc')
    mlflow.log_metric("cv_auc_mean", cv_scores.mean())
    mlflow.log_metric("cv_auc_std", cv_scores.std())
    
    # Train final model
    model.fit(X_train, y_train)
    
    # Evaluate
    train_score = model.score(X_train, y_train)
    test_score = model.score(X_test, y_test)
    mlflow.log_metric("train_accuracy", train_score)
    mlflow.log_metric("test_accuracy", test_score)
    
    # Log model
    mlflow.sklearn.log_model(model, "model")
    
    # Log artifacts
    mlflow.log_artifact("feature_importance.png")
```

#### Step 6: AI Integration

**Prompt GitHub Copilot:**
"Build AI-powered features for [feature]. Integrate LLMs for natural language understanding, implement RAG (Retrieval Augmented Generation) if needed, create intelligent automation, and build on ML engineer's models."

AI integration examples:

**LLM Integration**:
```python
from openai import AsyncOpenAI
import asyncio

class AIAssistant:
    def __init__(self, api_key: str):
        self.client = AsyncOpenAI(api_key=api_key)
    
    async def generate_product_description(self, product_data: dict) -> str:
        """Generate product description using LLM"""
        prompt = f"""
        Generate an engaging product description for:
        Name: {product_data['name']}
        Category: {product_data['category']}
        Features: {', '.join(product_data['features'])}
        Target audience: {product_data['target_audience']}
        """
        
        response = await self.client.chat.completions.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": "You are a product marketing expert."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.7
        )
        
        return response.choices[0].message.content
```

**RAG Implementation**:
```python
from langchain.vectorstores import Pinecone
from langchain.embeddings import OpenAIEmbeddings
from langchain.chains import RetrievalQA
from langchain.llms import OpenAI

class RAGSystem:
    def __init__(self, pinecone_index: str):
        self.embeddings = OpenAIEmbeddings()
        self.vectorstore = Pinecone.from_existing_index(
            pinecone_index,
            self.embeddings
        )
        self.qa_chain = RetrievalQA.from_chain_type(
            llm=OpenAI(temperature=0),
            chain_type="stuff",
            retriever=self.vectorstore.as_retriever(search_kwargs={"k": 3})
        )
    
    async def answer_question(self, question: str) -> str:
        """Answer question using RAG"""
        return await self.qa_chain.arun(question)
```

### Phase 4: Implementation and Optimization

#### Step 7: Data Pipeline Implementation

**Prompt GitHub Copilot:**
"Implement production data pipelines for [feature]. Include real-time streaming with Kafka/Kinesis, batch processing with Airflow/Prefect, data quality monitoring, and schema validation. Build on all previous designs."

Production pipeline:

**Streaming Pipeline** (Kafka + Flink):
```python
from pyflink.datastream import StreamExecutionEnvironment
from pyflink.datastream.connectors import FlinkKafkaConsumer

def create_streaming_pipeline():
    env = StreamExecutionEnvironment.get_execution_environment()
    
    # Kafka source
    kafka_consumer = FlinkKafkaConsumer(
        topics='user-events',
        deserialization_schema=...,
        properties={'bootstrap.servers': 'kafka:9092'}
    )
    
    # Process stream
    env.add_source(kafka_consumer) \
        .map(parse_event) \
        .key_by(lambda x: x['user_id']) \
        .window(...) \
        .reduce(aggregate_user_metrics) \
        .add_sink(...)
    
    env.execute("User Events Streaming Pipeline")
```

**Data Quality Checks**:
```python
from great_expectations.dataset import PandasDataset

def validate_data_quality(df: pd.DataFrame) -> bool:
    """Validate data quality using Great Expectations"""
    ge_df = PandasDataset(df)
    
    # Define expectations
    ge_df.expect_column_values_to_not_be_null('user_id')
    ge_df.expect_column_values_to_be_unique('event_id')
    ge_df.expect_column_values_to_be_in_set('event_type', ['click', 'purchase', 'view'])
    ge_df.expect_column_values_to_be_between('amount', 0, 10000)
    
    # Validate
    validation_result = ge_df.validate()
    return validation_result.success
```

#### Step 8: Performance Optimization

**Prompt GitHub Copilot:**
"Optimize data processing and model serving performance for [feature]. Focus on query optimization with proper indexes, caching strategies for frequent queries, model inference speed optimization, and batch prediction strategies."

Performance optimizations:

**Query Optimization**:
```sql
-- Create materialized view for expensive queries
CREATE MATERIALIZED VIEW user_analytics_summary AS
SELECT 
    user_id,
    COUNT(*) as event_count,
    COUNT(DISTINCT DATE(created_at)) as active_days,
    MAX(created_at) as last_activity,
    SUM(CASE WHEN event_type = 'purchase' THEN 1 ELSE 0 END) as purchase_count
FROM events
WHERE created_at >= NOW() - INTERVAL '30 days'
GROUP BY user_id;

-- Refresh periodically
REFRESH MATERIALIZED VIEW CONCURRENTLY user_analytics_summary;

-- Add index for fast lookups
CREATE INDEX idx_user_analytics_user_id ON user_analytics_summary(user_id);
```

**Model Serving Optimization**:
```python
import torch
from functools import lru_cache

class ModelServer:
    def __init__(self, model_path: str):
        self.model = torch.jit.load(model_path)  # TorchScript for faster inference
        self.model.eval()
    
    @lru_cache(maxsize=10000)
    def _get_cached_prediction(self, user_id: str) -> dict:
        """Cache predictions for 5 minutes"""
        return self.predict(user_id)
    
    @torch.no_grad()  # Disable gradient computation
    def predict(self, user_id: str) -> dict:
        """Optimized prediction"""
        features = self.get_features(user_id)
        
        # Batch predictions if possible
        with torch.cuda.amp.autocast():  # Mixed precision
            output = self.model(features)
        
        return self.postprocess(output)
```

### Phase 5: Testing and Deployment

#### Step 9: Comprehensive Testing

**Prompt GitHub Copilot:**
"Create test suites for data pipelines and ML components: [feature]. Include data validation tests, pipeline integration tests, model performance tests, A/B testing framework, and monitoring tests."

Testing strategy:

**Data Pipeline Tests**:
```python
import pytest

def test_data_ingestion():
    """Test data ingestion from source"""
    ingester = DataIngestion(config)
    df = ingester.ingest_from_database(query, db_config)
    
    assert len(df) > 0
    assert all(col in df.columns for col in required_columns)
    assert df['user_id'].notna().all()

def test_feature_engineering():
    """Test feature transformations"""
    engineer = FeatureEngineer()
    features = engineer.transform(sample_data)
    
    assert features.shape[1] == expected_feature_count
    assert not features.isnull().any().any()
```

**Model Performance Tests**:
```python
def test_model_accuracy():
    """Test model meets minimum accuracy threshold"""
    model = load_model('production')
    accuracy = model.score(X_test, y_test)
    assert accuracy >= 0.85

def test_model_inference_latency():
    """Test model serves predictions quickly"""
    import time
    
    start = time.time()
    prediction = model.predict(sample_input)
    latency = time.time() - start
    
    assert latency < 0.1  # 100ms SLA
```

#### Step 10: Production Deployment

**Prompt GitHub Copilot:**
"Deploy data-driven feature to production: [feature]. Include pipeline orchestration with Airflow/Prefect, ML model deployment with versioning, monitoring dashboards for data quality and model performance, and rollback strategies."

Deployment:

**Model Deployment**:
```python
# Deploy model with versioning
import mlflow.pyfunc

class ModelDeployment:
    def deploy_model(self, model_uri: str, stage: str = "production"):
        """Deploy model to production"""
        client = mlflow.tracking.MlflowClient()
        
        # Register model
        model_details = mlflow.register_model(model_uri, "user_churn_model")
        
        # Transition to production
        client.transition_model_version_stage(
            name="user_churn_model",
            version=model_details.version,
            stage=stage
        )
        
        return model_details.version
```

## Best Practices

- **Data Quality First**: Validate data at every step
- **Feature Store**: Centralize feature management
- **Experiment Tracking**: Track all experiments with MLflow/W&B
- **Model Versioning**: Version models and datasets
- **A/B Testing**: Test ML models in production
- **Monitoring**: Monitor data drift and model performance
- **Incremental Learning**: Update models with new data
- **Reproducibility**: Make everything reproducible

## Related Patterns

- `ml-pipeline.md` - Detailed ML pipeline implementation
- `api-scaffold.md` (tool) - API scaffolding
- `data-pipeline.md` (tool) - Data pipeline templates
- `feature-store.md` (tool) - Feature store setup
- `model-serving.md` (tool) - ML model serving
- `performance-optimization.md` - Optimize data processing
