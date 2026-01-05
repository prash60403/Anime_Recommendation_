# 🎬 Anime Recommendation System – End-to-End MLOps Project

## 📌 Project Overview
This project implements a **Hybrid Anime Recommendation System** that combines **collaborative filtering**, **content-based filtering**, and **deep learning embeddings**.  
It follows a complete **MLOps workflow** including data ingestion, preprocessing, model training, experiment tracking, CI/CD, and cloud deployment.

---

## 🏗️ System Architecture
- Data Ingestion & Preprocessing
- Embedding-based Recommendation Model
- Experiment Tracking (Comet ML)
- Prediction APIs (Flask + ChatGPT)
- CI/CD & Deployment (Jenkins + Kubernetes)

---

## ☁️ 1. Database Setup (GCP Buckets & IAM)
- Google Cloud Storage (GCS) buckets store:
  - Raw datasets
  - Processed data
  - Trained models & artifacts
- IAM users and roles ensure **secure access control**.
- Supports scalable and cloud-native storage.

---

## 📂 2. Project Structure
├── artifacts/
├── config/
├── notebook/
├── pipeline/
├── src/
├── static/
├── templates/
├── utils/
├── requirements.txt

##  data base setup using gcp buckets and iam user setup
2.project setup - create folders for artifacts, config, notebook, pipeline, src, static, template, utils, 
and files as requirement.txt
3.Data ingestion 
 - in data ingestion create data ingestion class and perform try and exception handling for uploading the csv file to csv
   if data is abov 5m download only 5m data else upload as it is.
4. juipiter notebook analysis- 
  this is the main part of predicting the output 
  - import the libraries
  - now the problem is data might be inconsistent and not so accurate to recommend
  - we have 3 dataset anime with synopsis, anime, animalist so we need to read all 3 dataset and how we can use that
  - animalist has more than 5m data so we are only using 5m data
  - animalist has user_id,anime_id,rating,watching_status,watched_episodes but we only need user_id, anime_id, raing and assign it as rating_df
  - now i need the user_id that are ratings greater than 400 and i will place it in rating df
  - It simply converts each unique user ID into a unique number so that users can be represented as numeric values for machine learning, while also keeping a way to convert back to the original user ID.
  - This code converts each unique anime ID into a unique numeric value for machine learning, keeps a reverse mapping to get back the original anime ID, and adds a new column with these encoded numeric anime values to the dataframe
  - splitting train and test data
  - model  architecture 
  - Creates Embeddings
  - It creates embeddings for users and anime, meaning it converts them into 128-dimensional vectors so the model can learn relationships.
  - Finds Similarity :It uses a Dot product to measure how similar a user and anime are based on their embeddings.
  - Prediction Layer : The output goes through dense + batch normalization + sigmoid to produce a final rating/liking score between 0 and 1.
  - Builds & Compiles Model: It builds a Keras model that takes user+anime as input and compiles it using binary crossentropy loss with Adam optimizer to train the recommendation system.
  - This function defines a custom learning rate schedule that linearly increases the learning rate from start_lr to max_lr during warm-up epochs, optionally sustains it, and then exponentially decays it toward min_lr for the remaining training epochs.
  - This function extracts a layer’s weight matrix from the model and normalizes each row, so all weight vectors have unit length and can be compared fairly.
  - This function takes an anime ID and returns its English name from the dataset, and if the English name is missing, it falls back to the original name.
  - This code sorts the DataFrame by the Score column in descending order, placing missing values at the end and updating the original DataFrame.
  - This function(getAnimeFrame) returns the row from the DataFrame that matches the given anime ID (number) or anime name (string).
  - This(getSynopsis) function returns the synopsis of an anime by matching either its ID or its name from the synopsis DataFrame.
  - This function(find_similar_animes) finds and returns a list of anime similar to a given anime by comparing their learned weight vectors, ranking them by similarity score, and showing the most related anime names and genres.
  - This function (find_similar_users) finds users similar to a given user by comparing their embedding vectors, ranks them by similarity score, and returns the most similar users while excluding the input user.
  - This function extracts all genres from the DataFrame, counts how often each genre appears, optionally visualizes them, and returns a list of the user’s favorite genres.
  - This function(get_user_preferences) finds a user’s top-rated anime (top 25%), analyzes their genres to understand preferences, optionally plots them, and returns the anime names with genres the user likes most.
  - This function (get_user_recommendations) recommends anime to a user by looking at what similar users like, removing anime the user already prefers, counting the most common remaining anime, and returning the top recommendations with genre and synopsis**.
  -This function hybrid_recommendation creates a hybrid anime recommendation by combining user-based recommendations (from similar users) and content-based recommendations (from similar anime), weighting both and returning the top suggested anime**.

5. DataProcessor – Function-wise Explanation (Simple Points)
1. __init__()
Initializes file paths, variables, and encoding dictionaries
Creates output directory
Sets up logging
2. load_data(usecols)
Loads the ratings CSV file
Reads only required columns (user_id, anime_id, rating)
3. filter_users(min_rating=400)
Removes users who have rated fewer than 400 anime
Keeps only active users for better model learning
4. scale_ratings()
Normalizes ratings between 0 and 1
Helps the model train more effectively
5. encode_data()
Converts user IDs and anime IDs into numerical indexe
Creates encode–decode dictionaries for users and anime
6. split_data(test_size=1000)
Shuffles the dataset
Splits data into training and testing sets
Separates inputs (user, anime) and output (rating)
7. save_artifacts()
Saves:
Encoders & decoders
Train/test data
Ratings DataFrame
Uses joblib for reuse in model training
8. process_anime_data()
Loads anime metadata and synopsis files
Cleans missing/unknown values
Extracts anime name, genres, score, episodes, etc.
Sorts anime by score
Saves cleaned anime and synopsis datasets
9. run()
Executes the full data processing pipeline step by step
Handles errors and logs progress
10. __main__ block
Creates a DataProcessor object
Starts the complete preprocessing workflow
✅ In short:
This class prepares clean, encoded, and well-structured data needed for training and running the anime recommendation system.

6. ModelTraining – Function-wise Explanation (Point-wise, Simple)
1. __init__(self, data_path)
Stores the processed data path
Initializes Comet ML for experiment tracking
Sets up logging for training process
2. load_data()
Loads:
Training input (X_train_array)
Testing input (X_test_array)
Training ratings (y_train)
Testing ratings (y_test)
Returns all datsets for model training
3. train_model()
Loads training and testing data
Reads total number of users and anime
Builds the recommendation model using BaseModel
Defines:
Batch size
Learning rate schedule (warm-up → decay)
Sets callbacks:
Learning rate scheduler
Model checkpoin
Early stopping
Trains the model with validation data
Logs training & validation loss to Comet ML
Loads the best saved model weights
Calls function to save final model and embeddings
4. extract_weights(layer_name, model)
Extracts embedding weights from a specific layer
Normalizes embeddings for similarity calculations
Returns processed weight vectors
5. save_model_weights(model)
Saves the trained model
Extracts:
User embeddings
Anime embeddings
Stores embeddings using joblib
Uploads model and weights to Comet ML
Confirms successful saving via logs
6. __main__ block
Creates a ModelTraining object
Starts the complete model training pipeline

7 Experiment Tracking using Comet ML (Simple Explanation)
Comet ML is used to track and monitor machine learning experiments in real time.
It automatically logs training loss, validation loss, metrics, models, and files during training.
Helps compare different experiments (hyperparameters, models, epochs) easily from a web dashboard.
Stores model weights, checkpoints, and artifacts safely for future use or rollback.
Makes debugging and performance analysis easier by visualizing learning curves.

8. Building an ML Pipeline (Simple Explanation)
An ML pipeline organizes the entire workflow from raw data to final predictions.
It ensures each step runs in the correct order automatically.
Makes the system reproducible, scalable, and easy to maintain.
Building a pipeline means connecting all ML steps into one smooth, automated flow, ensuring consistent results and easy updates.

Code Versioning using GitHub
GitHub is used to track and manage source code changes.
Enables collaboration with branches, commits, and pull requests.
Maintains a clear history of code updates.
Helps revert bugs by going back to a stable commit.
Integrates easily with CI/CD and MLOps tools.

9. Prediction helper functions load the trained model and embeddings to generate accurate anime recommendations based on user and content similarity.
Flask serves these recommendations through a web app, while ChatGPT enhances the experience by explaining and interacting with users naturally.

10. CI/CD deployment uses Jenkins to automatically build, test, and containerize the application whenever code changes are pushed.
The Dockerized app is then deployed to Google Kubernetes Engine (GKE), ensuring scalable, reliable, and automated production releases.
