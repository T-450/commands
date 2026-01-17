# Machine Learning Pipeline Workflow - GitHub Copilot Instructions

## Overview

This workflow guides you through designing and implementing a complete, production-ready machine learning pipeline from data ingestion through model deployment and monitoring. It covers data processing, feature engineering, model training, evaluation, deployment, and continuous monitoring.

## When to Use

**Prompt GitHub Copilot with:**
- "Build ML pipeline for user churn prediction using ml-pipeline workflow"
- "Create production-ready recommendation system with complete ML pipeline"
- "Implement fraud detection ML pipeline with training, deployment, and monitoring"
- "Develop image classification pipeline using ml-pipeline approach"

## Pipeline Components

### 1. Data Ingestion

**Prompt GitHub Copilot:**
"Create data ingestion pipeline for [ML use case]. Support multiple data sources (databases, APIs, files), implement schema validation with Pydantic, add data versioning with DVC, and enable incremental loading for efficient updates."

Data ingestion implementation:

```python
from typing import List, Dict, Any
import pandas as pd
from pydantic import BaseModel, validator
from sqlalchemy import create_engine
import dvc.api

class DataSchema(BaseModel):
    """Schema validation for incoming data"""
    user_id: str
    timestamp: str
    feature_1: float
    feature_2: int
    target: int
    
    @validator('feature_1')
    def validate_feature_1(cls, v):
        if v < 0 or v > 1:
            raise ValueError('feature_1 must be between 0 and 1')
        return v

class DataIngestion:
    def __init__(self, config: Dict[str, Any]):
        self.config = config
        self.db_engine = create_engine(config['database_url'])
    
    def ingest_from_database(self, query: str, validate: bool = True) -> pd.DataFrame:
        """Ingest data from database with validation"""
        df = pd.read_sql(query, self.db_engine)
        
        if validate:
            # Validate each row against schema
            for idx, row in df.iterrows():
                try:
                    DataSchema(**row.to_dict())
                except Exception as e:
                    print(f"Validation error at row {idx}: {e}")
        
        return df
    
    def ingest_from_api(self, endpoint: str, params: Dict) -> pd.DataFrame:
        """Ingest data from REST API"""
        import requests
        response = requests.get(endpoint, params=params)
        data = response.json()
        return pd.DataFrame(data)
    
    def ingest_with_versioning(self, data_path: str) -> pd.DataFrame:
        """Load data with DVC versioning"""
        with dvc.api.open(data_path, mode='rb') as f:
            df = pd.read_parquet(f)
        return df
    
    def incremental_load(self, last_timestamp: str) -> pd.DataFrame:
        """Load only new data since last run"""
        query = f"""
        SELECT * FROM events 
        WHERE timestamp > '{last_timestamp}'
        ORDER BY timestamp
        """
        return self.ingest_from_database(query)
```

### 2. Feature Engineering

**Prompt GitHub Copilot:**
"Design feature engineering pipeline for [ML use case]. Implement feature transformations (scaling, encoding), create derived features, handle missing data and outliers, implement statistical validation, and integrate with feature store (Feast/Tecton)."

Feature engineering:

```python
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.impute import SimpleImputer
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
import numpy as np

class FeatureEngineer:
    def __init__(self):
        self.preprocessor = None
        self.feature_names = None
    
    def create_time_features(self, df: pd.DataFrame, timestamp_col: str) -> pd.DataFrame:
        """Create time-based features"""
        df = df.copy()
        df[timestamp_col] = pd.to_datetime(df[timestamp_col])
        
        df['hour'] = df[timestamp_col].dt.hour
        df['day_of_week'] = df[timestamp_col].dt.dayofweek
        df['is_weekend'] = df['day_of_week'].isin([5, 6]).astype(int)
        df['month'] = df[timestamp_col].dt.month
        df['day_of_month'] = df[timestamp_col].dt.day
        
        # Cyclical encoding for time features
        df['hour_sin'] = np.sin(2 * np.pi * df['hour'] / 24)
        df['hour_cos'] = np.cos(2 * np.pi * df['hour'] / 24)
        df['day_sin'] = np.sin(2 * np.pi * df['day_of_week'] / 7)
        df['day_cos'] = np.cos(2 * np.pi * df['day_of_week'] / 7)
        
        return df
    
    def create_aggregate_features(self, df: pd.DataFrame, group_col: str) -> pd.DataFrame:
        """Create aggregation features"""
        df = df.copy()
        
        # Rolling window aggregations
        df[f'{group_col}_count_7d'] = df.groupby(group_col)['event'].transform(
            lambda x: x.rolling('7D').count()
        )
        df[f'{group_col}_sum_30d'] = df.groupby(group_col)['value'].transform(
            lambda x: x.rolling('30D').sum()
        )
        
        # Statistical aggregations
        df[f'{group_col}_mean'] = df.groupby(group_col)['value'].transform('mean')
        df[f'{group_col}_std'] = df.groupby(group_col)['value'].transform('std')
        
        return df
    
    def build_preprocessor(self, numeric_features: List[str], 
                          categorical_features: List[str]) -> ColumnTransformer:
        """Build preprocessing pipeline"""
        numeric_transformer = Pipeline(steps=[
            ('imputer', SimpleImputer(strategy='median')),
            ('scaler', StandardScaler())
        ])
        
        categorical_transformer = Pipeline(steps=[
            ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
            ('onehot', OneHotEncoder(handle_unknown='ignore', sparse_output=False))
        ])
        
        preprocessor = ColumnTransformer(
            transformers=[
                ('num', numeric_transformer, numeric_features),
                ('cat', categorical_transformer, categorical_features)
            ],
            remainder='drop'
        )
        
        return preprocessor
    
    def handle_outliers(self, df: pd.DataFrame, columns: List[str], 
                       method: str = 'iqr') -> pd.DataFrame:
        """Remove or cap outliers"""
        df = df.copy()
        
        for col in columns:
            if method == 'iqr':
                Q1 = df[col].quantile(0.25)
                Q3 = df[col].quantile(0.75)
                IQR = Q3 - Q1
                lower_bound = Q1 - 1.5 * IQR
                upper_bound = Q3 + 1.5 * IQR
                
                # Cap outliers instead of removing
                df[col] = df[col].clip(lower=lower_bound, upper=upper_bound)
        
        return df
```

### 3. Model Training

**Prompt GitHub Copilot:**
"Implement model training pipeline for [ML task]. Include experiment tracking with MLflow/Weights&Biases, hyperparameter optimization with Optuna, cross-validation for robustness, and model versioning for reproducibility."

Model training:

```python
import mlflow
import mlflow.sklearn
from sklearn.model_selection import cross_val_score, GridSearchCV
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score
import optuna

class ModelTrainer:
    def __init__(self, experiment_name: str):
        mlflow.set_experiment(experiment_name)
        self.best_model = None
    
    def train_with_mlflow(self, X_train, y_train, X_test, y_test, 
                         model_params: dict):
        """Train model with MLflow tracking"""
        with mlflow.start_run(run_name=f"rf_model_{model_params['n_estimators']}"):
            # Log parameters
            mlflow.log_params(model_params)
            
            # Train model
            model = RandomForestClassifier(**model_params, random_state=42)
            model.fit(X_train, y_train)
            
            # Cross-validation
            cv_scores = cross_val_score(model, X_train, y_train, cv=5, 
                                       scoring='f1_weighted')
            mlflow.log_metric("cv_f1_mean", cv_scores.mean())
            mlflow.log_metric("cv_f1_std", cv_scores.std())
            
            # Evaluate on test set
            y_pred = model.predict(X_test)
            
            metrics = {
                'accuracy': accuracy_score(y_test, y_pred),
                'precision': precision_score(y_test, y_pred, average='weighted'),
                'recall': recall_score(y_test, y_pred, average='weighted'),
                'f1': f1_score(y_test, y_pred, average='weighted')
            }
            
            for metric_name, metric_value in metrics.items():
                mlflow.log_metric(metric_name, metric_value)
            
            # Log feature importance
            feature_importance = pd.DataFrame({
                'feature': X_train.columns,
                'importance': model.feature_importances_
            }).sort_values('importance', ascending=False)
            
            feature_importance.to_csv('feature_importance.csv', index=False)
            mlflow.log_artifact('feature_importance.csv')
            
            # Log model
            mlflow.sklearn.log_model(model, "model")
            
            return model, metrics
    
    def hyperparameter_tuning(self, X_train, y_train, X_test, y_test):
        """Hyperparameter tuning with Optuna"""
        def objective(trial):
            params = {
                'n_estimators': trial.suggest_int('n_estimators', 50, 300),
                'max_depth': trial.suggest_int('max_depth', 3, 15),
                'min_samples_split': trial.suggest_int('min_samples_split', 2, 20),
                'min_samples_leaf': trial.suggest_int('min_samples_leaf', 1, 10),
                'max_features': trial.suggest_categorical('max_features', 
                                                         ['sqrt', 'log2'])
            }
            
            model = RandomForestClassifier(**params, random_state=42)
            
            # Cross-validation score
            score = cross_val_score(model, X_train, y_train, cv=5, 
                                   scoring='f1_weighted').mean()
            
            return score
        
        # Run optimization
        study = optuna.create_study(direction='maximize')
        study.optimize(objective, n_trials=50)
        
        # Train best model
        best_params = study.best_params
        print(f"Best parameters: {best_params}")
        
        return self.train_with_mlflow(X_train, y_train, X_test, y_test, 
                                     best_params)
```

### 4. Model Evaluation

**Prompt GitHub Copilot:**
"Create comprehensive model evaluation framework for [ML task]. Include multiple metrics (accuracy, precision, recall, F1, AUC-ROC), confusion matrix analysis, A/B testing framework, bias detection for fairness, and performance monitoring dashboards."

Model evaluation:

```python
from sklearn.metrics import (
    confusion_matrix, classification_report, roc_auc_score, roc_curve
)
import matplotlib.pyplot as plt
import seaborn as sns

class ModelEvaluator:
    def __init__(self):
        self.metrics = {}
    
    def comprehensive_evaluation(self, y_true, y_pred, y_pred_proba=None):
        """Comprehensive model evaluation"""
        # Classification metrics
        self.metrics['confusion_matrix'] = confusion_matrix(y_true, y_pred)
        self.metrics['classification_report'] = classification_report(y_true, y_pred)
        
        # If probabilities available
        if y_pred_proba is not None:
            self.metrics['auc_roc'] = roc_auc_score(y_true, y_pred_proba, 
                                                    multi_class='ovr')
        
        # Visualizations
        self.plot_confusion_matrix(y_true, y_pred)
        
        if y_pred_proba is not None:
            self.plot_roc_curve(y_true, y_pred_proba)
        
        return self.metrics
    
    def plot_confusion_matrix(self, y_true, y_pred):
        """Plot confusion matrix"""
        cm = confusion_matrix(y_true, y_pred)
        plt.figure(figsize=(8, 6))
        sns.heatmap(cm, annot=True, fmt='d', cmap='Blues')
        plt.title('Confusion Matrix')
        plt.ylabel('True Label')
        plt.xlabel('Predicted Label')
        plt.savefig('confusion_matrix.png')
        plt.close()
    
    def plot_roc_curve(self, y_true, y_pred_proba):
        """Plot ROC curve"""
        fpr, tpr, _ = roc_curve(y_true, y_pred_proba)
        auc = roc_auc_score(y_true, y_pred_proba)
        
        plt.figure(figsize=(8, 6))
        plt.plot(fpr, tpr, label=f'AUC = {auc:.3f}')
        plt.plot([0, 1], [0, 1], 'k--', label='Random')
        plt.xlabel('False Positive Rate')
        plt.ylabel('True Positive Rate')
        plt.title('ROC Curve')
        plt.legend()
        plt.savefig('roc_curve.png')
        plt.close()
    
    def bias_detection(self, y_true, y_pred, sensitive_features):
        """Detect bias across sensitive features"""
        from fairlearn.metrics import MetricFrame, selection_rate
        
        metric_frame = MetricFrame(
            metrics={
                'accuracy': accuracy_score,
                'selection_rate': selection_rate,
                'false_positive_rate': lambda y_t, y_p: (
                    confusion_matrix(y_t, y_p)[0, 1] / 
                    sum(confusion_matrix(y_t, y_p)[0])
                )
            },
            y_true=y_true,
            y_pred=y_pred,
            sensitive_features=sensitive_features
        )
        
        return metric_frame
```

### 5. Model Deployment

**Prompt GitHub Copilot:**
"Implement model deployment pipeline for [ML model]. Create FastAPI serving endpoint, support both real-time and batch predictions, implement model registry with versioning, add rollback capabilities, and include health checks."

Model deployment:

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import mlflow.pyfunc
import joblib
from typing import List, Dict

app = FastAPI()

class PredictionInput(BaseModel):
    features: List[float]

class PredictionOutput(BaseModel):
    prediction: int
    probability: float
    model_version: str

class ModelServer:
    def __init__(self, model_registry_uri: str):
        self.model_registry_uri = model_registry_uri
        self.model = None
        self.model_version = None
        self.load_model()
    
    def load_model(self, stage: str = "Production"):
        """Load model from MLflow registry"""
        model_uri = f"models:/user_churn_model/{stage}"
        self.model = mlflow.pyfunc.load_model(model_uri)
        
        # Get model version
        client = mlflow.tracking.MlflowClient()
        model_version_info = client.get_latest_versions("user_churn_model", 
                                                        stages=[stage])[0]
        self.model_version = model_version_info.version
    
    def predict(self, features: List[float]) -> Dict:
        """Make prediction"""
        import numpy as np
        
        # Prepare input
        X = np.array(features).reshape(1, -1)
        
        # Get prediction
        prediction = self.model.predict(X)[0]
        
        # Get probability if available
        try:
            proba = self.model.predict_proba(X)[0]
            probability = float(max(proba))
        except:
            probability = None
        
        return {
            'prediction': int(prediction),
            'probability': probability,
            'model_version': self.model_version
        }
    
    def batch_predict(self, features_list: List[List[float]]) -> List[Dict]:
        """Batch predictions for efficiency"""
        import numpy as np
        
        X = np.array(features_list)
        predictions = self.model.predict(X)
        
        try:
            probabilities = self.model.predict_proba(X).max(axis=1)
        except:
            probabilities = [None] * len(predictions)
        
        return [
            {
                'prediction': int(pred),
                'probability': float(prob) if prob is not None else None,
                'model_version': self.model_version
            }
            for pred, prob in zip(predictions, probabilities)
        ]

# Initialize model server
model_server = ModelServer(model_registry_uri="mlruns")

@app.post("/predict", response_model=PredictionOutput)
async def predict(input_data: PredictionInput):
    """Single prediction endpoint"""
    try:
        result = model_server.predict(input_data.features)
        return result
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.post("/batch_predict")
async def batch_predict(inputs: List[PredictionInput]):
    """Batch prediction endpoint"""
    try:
        features_list = [input_data.features for input_data in inputs]
        results = model_server.batch_predict(features_list)
        return results
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.get("/health")
async def health_check():
    """Health check endpoint"""
    return {
        "status": "healthy",
        "model_version": model_server.model_version,
        "model_loaded": model_server.model is not None
    }

@app.post("/reload_model")
async def reload_model(stage: str = "Production"):
    """Reload model from registry"""
    try:
        model_server.load_model(stage)
        return {
            "status": "success",
            "model_version": model_server.model_version
        }
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

### 6. Monitoring and Retraining

**Prompt GitHub Copilot:**
"Set up ML model monitoring and retraining pipeline for [model]. Implement data drift detection with Evidently, track model performance metrics, set up automated alerts for degradation, and trigger retraining when performance drops below threshold."

Monitoring implementation:

```python
from evidently.dashboard import Dashboard
from evidently.tabs import DataDriftTab, ClassificationPerformanceTab
from evidently.model_profile import Profile
from evidently.model_profile.sections import DataDriftProfileSection
import pandas as pd

class ModelMonitor:
    def __init__(self, reference_data: pd.DataFrame):
        self.reference_data = reference_data
        self.performance_threshold = 0.80
    
    def detect_data_drift(self, current_data: pd.DataFrame):
        """Detect data drift using Evidently"""
        # Create data drift profile
        drift_profile = Profile(sections=[DataDriftProfileSection()])
        drift_profile.calculate(self.reference_data, current_data)
        
        drift_report = drift_profile.json()
        
        # Check if drift detected
        drift_detected = drift_report['data_drift']['data']['metrics']['dataset_drift']
        
        if drift_detected:
            print("⚠️  Data drift detected!")
            # Trigger retraining
            self.trigger_retraining()
        
        return drift_report
    
    def monitor_performance(self, y_true, y_pred):
        """Monitor model performance"""
        current_f1 = f1_score(y_true, y_pred, average='weighted')
        
        if current_f1 < self.performance_threshold:
            print(f"⚠️  Performance degradation: F1 = {current_f1:.3f}")
            self.trigger_retraining()
        
        # Log to monitoring system
        self.log_metrics({
            'f1_score': current_f1,
            'accuracy': accuracy_score(y_true, y_pred),
            'timestamp': pd.Timestamp.now()
        })
    
    def trigger_retraining(self):
        """Trigger automated retraining"""
        print("🔄 Triggering model retraining...")
        # In production, this would trigger your training pipeline
        # e.g., Airflow DAG, AWS Step Functions, etc.
        pass
    
    def log_metrics(self, metrics: dict):
        """Log metrics to monitoring system"""
        # Send to Prometheus, CloudWatch, etc.
        pass
```

## Complete Pipeline Orchestration

```python
# Airflow DAG for complete ML pipeline
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'ml-team',
    'depends_on_past': False,
    'start_date': datetime(2024, 1, 1),
    'retries': 1,
    'retry_delay': timedelta(minutes=5)
}

dag = DAG(
    'ml_pipeline',
    default_args=default_args,
    description='End-to-end ML pipeline',
    schedule_interval='@daily',
)

ingest_task = PythonOperator(
    task_id='ingest_data',
    python_callable=ingest_data,
    dag=dag,
)

engineer_task = PythonOperator(
    task_id='engineer_features',
    python_callable=engineer_features,
    dag=dag,
)

train_task = PythonOperator(
    task_id='train_model',
    python_callable=train_model,
    dag=dag,
)

evaluate_task = PythonOperator(
    task_id='evaluate_model',
    python_callable=evaluate_model,
    dag=dag,
)

deploy_task = PythonOperator(
    task_id='deploy_model',
    python_callable=deploy_model,
    dag=dag,
)

# Dependencies
ingest_task >> engineer_task >> train_task >> evaluate_task >> deploy_task
```

## Best Practices

- **Version Everything**: Data, code, models, features
- **Reproducibility**: Make experiments reproducible
- **Monitoring**: Always monitor in production
- **Automation**: Automate retraining and deployment
- **Testing**: Test models like software
- **Documentation**: Document data, features, models
- **Scalability**: Design for production scale
- **Security**: Protect sensitive data and models

## Related Patterns

- `data-driven-feature.md` - Build data-driven features
- `performance-optimization.md` - Optimize model serving
- `data-pipeline.md` (tool) - Data pipeline templates
- `experiment-tracking.md` (tool) - ML experiment tracking
- `model-serving.md` (tool) - Model deployment
- `feature-store.md` (tool) - Feature store setup
