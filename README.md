# email-multiclass-classifier
End‑to‑end NLP system that cleans, vectorizes, and classifies emails via TF‑IDF + keyword features into five categories; includes model persistence, confidence scores, and a MySQL database + CLI for storage and retrieval.


Email Classification Service
A compact NLP system to classify emails into Spam, Promotions, Social, Updates, and Primary using TF‑IDF n‑grams, subject emphasis, and optional keyword cues, with a persisted model and a MySQL‑backed CLI workflow.

Project timeline
Phase 1: Final-RP-1.ipynb — initial research notebook with EDA, baseline preprocessing, and multi‑model comparison.

Phase 2: sajjan-RP-1.pdf — formal document summarizing methodology, EDA visuals, results, and conclusions.

Phase 3: updated_project.ipynb — optimized pipeline with TF‑IDF bigrams, class‑balanced models, inference service, and MySQL storage utilities.

Features
Text cleaning: URL/email removal, case normalization, stopwords, stemming, selective punctuation retention.

Vectorization: TF‑IDF with unigrams+bigrams, controlled vocab via min_df/max_df.

Modeling: Multiple classifiers with stratified splits; probability outputs enabled; class imbalance handled.

Inference: Single entry point returning category, confidence, and optional per‑class probabilities.

Storage: MySQL persistence with retrieval by category, spam listing, and retention cleanup.

Repository structure
updated_project.ipynb — training, inference service, database layer, CLI.

Final-RP-1.ipynb — original research/EDA and baseline models.

sajjan-RP-1.pdf — project write‑up with analyses and results.

Requirements
Python 3.10+ (pandas, numpy, scikit‑learn, nltk, joblib, mysql‑connector).

MySQL 8.x (or compatible).

NLTK data: stopwords.

Setup
Create environment and install dependencies:

bash
python -m venv .venv
source .venv/bin/activate  # on Windows: .venv\Scripts\activate
pip install -r requirements.txt  # or install libs used in the notebooks
python -c "import nltk; nltk.download('stopwords')"
Configure database credentials via environment variables:

bash
export EMAILDB_HOST=localhost
export EMAILDB_USER=your_user
export EMAILDB_PASS=your_password
export EMAILDB_NAME=email_management
Create table schema:

sql
CREATE DATABASE IF NOT EXISTS email_management;
USE email_management;

CREATE TABLE IF NOT EXISTS emails (
  id INT AUTO_INCREMENT PRIMARY KEY,
  subject TEXT,
  body LONGTEXT,
  sender_email VARCHAR(320),
  category VARCHAR(32) NOT NULL,
  confidence DECIMAL(5,4) NOT NULL,
  is_processed BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  INDEX idx_category (category),
  INDEX idx_created_at (created_at)
);
Training
Input: CSV file email_data.csv with columns: subject, body, category.

Run training from updated_project.ipynb (function: improved_training) to build features, compare models, and save best_email_classifier.pkl.

Example CLI call inside the notebook:

python
# in updated_project.ipynb
improved_training()  # trains, evaluates, and saves the best model bundle
Inference (service API)
Load the persisted bundle and classify a subject/body pair:

python
service = EmailClassificationService(model_path="best_email_classifier.pkl")
service.classify_email("Your order has shipped", "Tracking information inside")
Returns: { category, confidence, optional per‑class probabilities, spam keyword score }.

CLI workflow
A simple terminal menu for end‑to‑end testing (classify+store, list spam, fetch by category, cleanup):

python
# in updated_project.ipynb
complete_email_system()
Data and labels
Target labels: spam, promotions, social, updates, primary.

Keep label names consistent across training, inference, and database writes.

Reproducibility
Use stratified splits, fixed random seeds, and pinned library versions.

Prefer environment‑based configuration for credentials and paths.

Roadmap
Add cross‑validation metrics (macro/micro F1) to the saved bundle.


Also Check our journal: https://www.ijsat.org/research-paper.php?id=7826
Provide a lightweight schema migration script.

Optional threshold tuning per class and calibrated probabilities.

Containerize training/inference for portable deployment.
