Anime Recommendation System – End-to-End MLOps Project
📌 Project Overview

This project is a Hybrid Anime Recommendation System that combines collaborative filtering, content-based filtering, and deep learning embeddings, built with a complete MLOps pipeline including data ingestion, preprocessing, model training, experiment tracking, CI/CD, and cloud deployment.

☁️ 1. Database Setup (GCP Buckets & IAM)

Google Cloud Storage (GCS) buckets are used to store:

Raw datasets

Processed data

Trained models and artifacts

IAM users and roles are configured to provide secure access control.

Enables scalable, cloud-based data storage without exposing credentials.

📂 2. Project Structure Setup

The project follows a modular and scalable structure:

├── artifacts/
├── config/
├── notebook/
├── pipeline/
├── src/
├── static/
├── templates/
├── utils/
├── requirements.txt


Ensures separation of concerns

Improves maintainability and reusability

Aligns with industry-grade ML project standards

📥 3. Data Ingestion

A Data Ingestion class is implemented with proper try-except handling.

Uploads CSV files to cloud storage.

If dataset size exceeds 5 million rows, only 5M rows are downloaded and processed.

Smaller datasets are ingested fully.

Prevents memory overload and ensures stable processing.

📊 4. Jupyter Notebook – Core Analysis & Modeling

This is the heart of the recommendation system.

Dataset Handling

Three datasets are used:

anime (metadata)

anime_with_synopsis

animelist (user interactions)

animelist contains >5M records → only 5M rows used.

Selected columns:

user_id

anime_id

rating

Stored as rating_df.

Data Cleaning & Filtering

Only users with more than 400 ratings are retained.

Improves recommendation accuracy by focusing on active users.

Encoding

User IDs and Anime IDs are:

Converted into numeric indices for ML models

Reverse mappings are stored for decoding results

Train-Test Split

Dataset is shuffled

Split into training and testing sets for validation

🧠 Model Architecture

Uses embedding-based collaborative filtering

Key components:

User embedding (128 dimensions)

Anime embedding (128 dimensions)

Dot product for similarity calculation

Dense + Batch Normalization + Sigmoid output

Outputs a normalized preference score between 0 and 1

Model Compilation

Loss: Binary Crossentropy

Optimizer: Adam

Custom learning rate scheduler:

Warm-up phase

Optional sustain

Exponential decay

🔍 Similarity & Recommendation Logic

Implemented helper functions for:

Extracting and normalizing embeddings

Mapping anime IDs to English names

Sorting anime by popularity score

Fetching anime metadata and synopsis

Finding:

Similar anime

Similar users

Genre preference analysis

User-based recommendations

Content-based recommendations

Hybrid recommendation combining both approaches

⚙️ 5. DataProcessor Class (Pipeline)

Handles end-to-end data preparation:

Load and filter raw data

Normalize ratings

Encode users and anime

Split train/test datasets

Save artifacts using joblib

Clean and prepare anime metadata & synopsis

Runs as a complete preprocessing pipeline

✅ Produces clean, structured data for training and inference.

🤖 6. ModelTraining Class

Responsible for model training and artifact generation:

Loads processed datasets

Builds the recommendation model

Applies callbacks:

Learning rate scheduler

Early stopping

Model checkpointing

Logs metrics to Comet ML

Saves:

Trained model

User embeddings

Anime embeddings

📈 7. Experiment Tracking using Comet ML

Tracks training and validation loss

Logs models, embeddings, and checkpoints

Enables experiment comparison

Helps debug and tune hyperparameters

Ensures reproducibility of experiments

🔄 8. ML Pipeline

Automates the entire workflow:

Data ingestion → preprocessing → training → saving → prediction

Ensures:

Reproducibility

Scalability

Maintainability

🧑‍💻 9. Code Versioning using GitHub

Tracks all code changes

Enables collaboration

Maintains history and rollback capability

Integrates smoothly with CI/CD and MLOps tools

🌐 10. Prediction & User Application

Prediction helper functions load:

Trained model

User & anime embeddings

Flask is used to serve recommendations via web APIs.

ChatGPT integration:

Explains recommendations

Improves user interaction and understanding

🚀 11. CI/CD Deployment (Jenkins + Kubernetes)

Jenkins automates:

Build

Test

Docker image creation

Application is deployed to Google Kubernetes Engine (GKE).

Ensures:

High availability

Scalability

Automated deployments
