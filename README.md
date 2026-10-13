# Privacy-Preserving Federated Depression Detection from Multisource Mobile Health Data

A Django + MySQL + Machine Learning web application for detecting depression from social-media text using multiple machine-learning classifiers and an ensemble voting classifier.

> **Important implementation note**
>
> The project title and documentation describe a privacy-preserving federated-learning architecture for multisource mobile-health data. The **current code included in this project archive**, however, is a Django web application that reads a centralized CSV dataset (`Social_Media_Datasets.csv`), trains scikit-learn text-classification models, stores user/prediction information in MySQL, and uses a `VotingClassifier` for prediction. It does **not** currently implement a complete production federated-learning protocol such as client-side training, FedAvg, secure aggregation, differential privacy, or encrypted model updates. Treat those items as the intended/research architecture rather than implemented features.

---

## 1. Project Overview

### Problem

Depression can affect emotional well-being, productivity, and quality of life. Early identification can support timely intervention.

This project uses natural-language text as a data source and applies machine-learning algorithms to classify text into:

- `0` → **No Depression Found**
- `1` → **Depression Found**

### Main workflow

```text
User enters social-media text
          |
          v
Django Web Application
          |
          v
Social_Media_Datasets.csv
          |
          v
Text preprocessing / CountVectorizer
          |
          v
Train/Test Split
          |
          +-------------------------------+
          |                               |
          v                               v
   Naive Bayes                         SVM
   Logistic Regression                Decision Tree
   Gradient Boosting
          |                               |
          +---------------+---------------+
                          |
                          v
                 VotingClassifier
                          |
                          v
              Depression Prediction
                          |
                          v
                    MySQL Database
                          |
             +------------+-------------+
             |                          |
             v                          v
       User prediction             Admin/Provider
       history                    accuracy/charts
```

---

## 2. Technology Stack

| Component | Technology |
|---|---|
| Programming Language | Python |
| Web Framework | Django |
| Database | MySQL |
| Machine Learning | Scikit-learn |
| Data Processing | Pandas |
| NLP Feature Extraction | CountVectorizer |
| Classification | Naive Bayes, SVM, Logistic Regression, Decision Tree, Gradient Boosting |
| Ensemble | VotingClassifier |
| Export | xlwt (`.xls`) |
| Frontend | HTML, CSS, Django Templates |
| Database Driver | MySQL backend / `mysqlclient` |
| Development Server | Django `runserver` |

The archived project was originally developed with a **Python 3.7 32-bit environment**, so using Python 3.7/3.8 is the safest compatibility choice for this legacy codebase.

---

## 3. Project Structure

After extracting the project, the important directory is:

```text
Privacy_Preserving_Federated_Depression_Detection/
│
├── Database/
│   └── privacy_preserving_federated_depression_detection.sql
│
├── privacy_preserving_federated_depression_detection/
│   ├── manage.py
│   ├── Social_Media_Datasets.csv
│   ├── Results.csv
│   │
│   ├── privacy_preserving_federated_depression_detection/
│   │   ├── settings.py
│   │   ├── urls.py
│   │   ├── asgi.py
│   │   └── wsgi.py
│   │
│   ├── Remote_User/
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── forms.py
│   │   └── migrations/
│   │
│   ├── Service_Provider/
│   │   ├── models.py
│   │   ├── views.py
│   │   └── migrations/
│   │
│   └── Template/
│       ├── htmls/
│       │   ├── RUser/
│       │   └── SProvider/
│       ├── images/
│       └── media/
│
└── README.md
```

### Important files

- `manage.py` — Django command-line entry point.
- `settings.py` — Django configuration and MySQL connection.
- `urls.py` — application routes.
- `Remote_User/views.py` — registration, login, and depression prediction.
- `Remote_User/models.py` — user and prediction database models.
- `Service_Provider/views.py` — model training, analytics, charts, and dataset export.
- `Social_Media_Datasets.csv` — machine-learning training dataset.
- `Database/privacy_preserving_federated_depression_detection.sql` — MySQL database dump.

---

# 4. Prerequisites

Install the following before running the project:

### Required

1. Python
2. MySQL Server
3. MySQL Workbench or phpMyAdmin
4. Git (optional)
5. A code editor such as VS Code or PyCharm

### Recommended versions

Because this is an older Django project:

```text
Python: 3.7 or 3.8
MySQL: 5.7+ / 8.x
Django: 3.0.x
```

Using a modern Django version may break this project because the code uses the older `django.conf.urls.url()` API.

---

# 5. Download / Extract the Project

Extract the project archive.

You should eventually have:

```text
Privacy_Preserving_Federated_Depression_Detection/
```

Then open a terminal inside:

```text
Privacy_Preserving_Federated_Depression_Detection/privacy_preserving_federated_depression_detection/
```

This is the directory that contains `manage.py`.

---

# 6. Create a Python Virtual Environment

Open Command Prompt / PowerShell / Terminal.

```bash
cd Privacy_Preserving_Federated_Depression_Detection/privacy_preserving_federated_depression_detection
```

Create the environment:

### Windows

```bash
py -3.7 -m venv venv
```

or:

```bash
python -m venv venv
```

Activate it:

```bash
venv\Scripts\activate
```

### macOS / Linux

```bash
python3 -m venv venv
source venv/bin/activate
```

After activation, your terminal should show something similar to:

```text
(venv)
```

---

# 7. Install Required Python Packages

Install the main dependencies:

```bash
pip install django==3.0.14
pip install pandas
pip install scikit-learn
pip install xlwt
pip install mysqlclient
```

You can also install everything together:

```bash
pip install django==3.0.14 pandas scikit-learn xlwt mysqlclient
```

If `mysqlclient` fails on Windows, install the appropriate MySQL client/build prerequisites or use a compatible Python version such as Python 3.7/3.8.

Check the installation:

```bash
python -m django --version
python --version
pip list
```

---

# 8. Configure MySQL

The project uses MySQL.

The current `settings.py` contains:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'privacy_preserving_federated_depression_detection',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
```

This means the default configuration expects:

```text
Database: privacy_preserving_federated_depression_detection
Username: root
Password: empty
Host: 127.0.0.1
Port: 3306
```

## If your MySQL root account has a password

Open:

```text
privacy_preserving_federated_depression_detection/settings.py
```

Change:

```python
'PASSWORD': '',
```

to:

```python
'PASSWORD': 'YOUR_MYSQL_PASSWORD',
```

For example:

```python
'PASSWORD': 'root123',
```

Do not commit real production passwords to GitHub.

---

# 9. Create the Database

## Option A — Recommended: Import the supplied SQL dump

The project already contains:

```text
Database/
└── privacy_preserving_federated_depression_detection.sql
```

### Using MySQL command line

Login:

```bash
mysql -u root -p
```

Then:

```sql
CREATE DATABASE privacy_preserving_federated_depression_detection;
EXIT;
```

Import the SQL file:

```bash
mysql -u root -p privacy_preserving_federated_depression_detection < Database/privacy_preserving_federated_depression_detection.sql
```

If your MySQL installation is on Windows and the command is not recognized, use the full path to `mysql.exe`.

### Using phpMyAdmin

1. Start MySQL.
2. Open phpMyAdmin.
3. Click **New**.
4. Create:

```text
privacy_preserving_federated_depression_detection
```

5. Open the newly created database.
6. Click **Import**.
7. Select:

```text
Database/privacy_preserving_federated_depression_detection.sql
```

8. Click **Go**.

The SQL dump already contains the project tables and sample database records.

---

# 10. Database Tables

The SQL dump contains Django/system tables and project-specific tables.

The main application tables are:

### `remote_user_clientregister_model`

Stores registered remote users.

Fields:

```text
id
username
email
password
phoneno
country
state
city
address
gender
```

### `remote_user_depression_detection`

Stores depression predictions.

Fields:

```text
id
ridno
post_id
text
Prediction
```

### `remote_user_detection_accuracy`

Stores model accuracy results.

Fields:

```text
id
names
ratio
```

### `remote_user_detection_ratio`

Stores the percentage distribution of:

```text
No Depression Found
Depression Found
```

---

# 11. Database Connection Test

After importing the database and configuring `settings.py`, run:

```bash
python manage.py check
```

A successful configuration should not report a database configuration error.

You can also test the database with:

```bash
python manage.py migrate
```

If the SQL dump was imported, Django may report that migrations are already applied or have little/no work to do.

---

# 12. How to Insert Training Data

The machine-learning training data is stored in:

```text
Social_Media_Datasets.csv
```

The application reads this file in both:

```text
Remote_User/views.py
```

and:

```text
Service_Provider/views.py
```

using:

```python
pd.read_csv('Social_Media_Datasets.csv')
```

Therefore, keep the CSV in the same directory as `manage.py`.

Current dataset structure:

```text
id,post_id,text,label
```

Example:

```csv
id,post_id,text,label
1,abc123,"I am feeling very happy today",0
2,abc124,"I feel hopeless and depressed",1
```

### Meaning of `label`

```text
0 = No Depression
1 = Depression
```

## Adding a new training record

Open:

```text
Social_Media_Datasets.csv
```

Add a row:

```csv
7254,newpost001,"This is an example social media text",0
```

For a depression-class example:

```csv
7255,newpost002,"This is another example text associated with depression",1
```

Save the CSV.

Then restart the Django server if necessary and use:

```text
Service Provider
      ↓
Train & Test Social Text Data Sets
```

The project will reload the CSV and train the models again.

### Important

Keep the four required columns:

```text
id
post_id
text
label
```

Do not rename `text` or `label`, because the source code directly uses:

```python
X = df['text']
y = df['results']
```

---

# 13. How the Training Works

When the Service Provider selects **Train & Test Social Text Data Sets**, the application:

1. Reads `Social_Media_Datasets.csv`.
2. Converts the `label` column into the project result format.
3. Uses `CountVectorizer` to convert text into numerical features.
4. Splits the data into:
   - 80% training data
   - 20% testing data
5. Trains five classifiers:
   - Naive Bayes
   - SVM
   - Logistic Regression
   - Decision Tree
   - Gradient Boosting
6. Calculates accuracy, classification report, and confusion matrix.
7. Stores model accuracy in MySQL.
8. Builds a `VotingClassifier`.
9. Uses the ensemble model for the actual user prediction.

The implementation uses:

```python
CountVectorizer()
```

for text feature extraction.

---

# 14. How to Insert a User

There are two ways.

## Method 1 — Through the Website

Open:

```text
http://127.0.0.1:8000/
```

Go to:

```text
Remote User
      ↓
Register
```

Fill in:

```text
Username
Email
Password
Phone
Country
State
City
Address
Gender
```

Submit the form.

The application executes logic equivalent to:

```python
ClientRegister_Model.objects.create(
    username=username,
    email=email,
    password=password,
    phoneno=phoneno,
    country=country,
    state=state,
    city=city,
    address=address,
    gender=gender
)
```

## Method 2 — Insert directly into MySQL

You can insert a user manually:

```sql
INSERT INTO remote_user_clientregister_model
(username, email, password, phoneno, country, state, city, address, gender)
VALUES
(
    'testuser',
    'test@example.com',
    'test123',
    '9876543210',
    'India',
    'Telangana',
    'Hyderabad',
    'Hyderabad',
    'Male'
);
```

> For a real application, passwords should never be stored as plain text. This legacy project currently stores and checks them directly.

---

# 15. How to Insert a Prediction

Normally, predictions are inserted automatically when a remote user submits text through:

```text
Remote User
      ↓
Predict Depression Type
```

The application stores:

```text
RID No
Post ID
Text
Prediction
```

The corresponding model is:

```python
class depression_detection(models.Model):
    ridno = models.CharField(max_length=300)
    post_id = models.CharField(max_length=300)
    text = models.CharField(max_length=30000)
    Prediction = models.CharField(max_length=300)
```

A prediction can also be inserted directly into MySQL:

```sql
INSERT INTO remote_user_depression_detection
(ridno, post_id, text, Prediction)
VALUES
(
    '1001',
    'post001',
    'Example social media text',
    'No Depression Found'
);
```

Valid prediction values used by the application are:

```text
No Depression Found
Depression Found
```

---

# 16. Run the Project Locally

Make sure:

- MySQL Server is running.
- The database exists.
- `settings.py` contains the correct MySQL password.
- Virtual environment is activated.

Then:

```bash
cd Privacy_Preserving_Federated_Depression_Detection/privacy_preserving_federated_depression_detection
```

Activate the environment:

```bash
venv\Scripts\activate
```

Run:

```bash
python manage.py runserver
```

You should see something similar to:

```text
Starting development server at http://127.0.0.1:8000/
```

Open:

```text
http://127.0.0.1:8000/
```

---

# 17. Application URLs

The project defines the following main routes.

| Feature | URL |
|---|---|
| Home | `/` |
| Remote User Login | `/login/` |
| Remote User Registration | `/Register1/` |
| Depression Prediction | `/Predict_Depression_Type/` |
| User Profile | `/ViewYourProfile/` |
| Service Provider Login | `/serviceproviderlogin/` |
| View Remote Users | `/View_Remote_Users/` |
| Train Models | `/train_model/` |
| View Predictions | `/View_Depression_Type/` |
| Prediction Ratio | `/View_Depression_Type_Ratio/` |
| Download Predictions | `/Download_Predicted_DataSets/` |

Example:

```text
http://127.0.0.1:8000/serviceproviderlogin/
```

---

# 18. Service Provider Login

The current source code uses a hardcoded Service Provider login:

```text
Username: Admin
Password: Admin
```

This is defined directly in:

```text
Service_Provider/views.py
```

The relevant logic checks:

```python
if admin == "Admin" and password == "Admin":
```

### Security warning

This is suitable only for a college/demo project.

For production:

- Store admin credentials securely.
- Use Django authentication.
- Hash passwords.
- Add authorization/permissions.
- Remove hardcoded credentials.

---

# 19. Complete Usage Flow

## Step 1 — Start MySQL

Start:

```text
MySQL Server
```

## Step 2 — Activate Python environment

```bash
venv\Scripts\activate
```

## Step 3 — Start Django

```bash
python manage.py runserver
```

## Step 4 — Open application

```text
http://127.0.0.1:8000/
```

## Step 5 — Register a remote user

```text
Remote User → Register
```

## Step 6 — Login

Use the registered account.

## Step 7 — Predict depression

Open:

```text
Predict Depression Type
```

Enter:

```text
RID No
Post ID
Social Media Text
```

Submit.

The system:

```text
Text
 ↓
CountVectorizer
 ↓
Multiple ML Models
 ↓
Voting Classifier
 ↓
Prediction
 ↓
MySQL
```

## Step 8 — Service Provider analysis

Login:

```text
Service Provider
```

Credentials in the current demo:

```text
Admin / Admin
```

Then use:

```text
Train & Test Social Text Data Sets
View Prediction Of Social Text Type
View Social Text Type Ratio
Download Predicted Data Sets
View All Remote Users
```

---

# 20. Model Training Details

The current project trains:

### 1. Multinomial Naive Bayes

Used for text classification and suitable for word-frequency-based features.

### 2. Linear SVM

Uses:

```python
svm.LinearSVC()
```

### 3. Logistic Regression

Uses:

```python
LogisticRegression()
```

### 4. Decision Tree

Uses:

```python
DecisionTreeClassifier()
```

### 5. Gradient Boosting

Uses:

```python
GradientBoostingClassifier(
    n_estimators=100,
    learning_rate=1.0,
    max_depth=1,
    random_state=0
)
```

### 6. Voting Classifier

The individual models are combined using:

```python
VotingClassifier(models)
```

The final classifier is used to predict the user's submitted text.

---

# 21. Results

The project stores model accuracy in:

```text
remote_user_detection_accuracy
```

The supplied SQL dump contains example accuracy records.

These are **example results from the supplied database dump**, not a guaranteed result for every new run. Because the code uses `train_test_split()` without a fixed `random_state`, accuracy can change between training runs.

The application can display:

```text
Naive Bayes
SVM
Logistic Regression
Decision Tree Classifier
Gradient Boosting Classifier
```

and their corresponding accuracy percentages.

---

# 22. Prediction Ratio

The application calculates the percentage of stored predictions belonging to:

```text
No Depression Found
```

and:

```text
Depression Found
```

The calculation is based on records in:

```text
remote_user_depression_detection
```

The results are stored in:

```text
remote_user_detection_ratio
```

---

# 23. Download Predictions

The Service Provider can download the stored prediction records using:

```text
Download Predicted Data Sets
```

The application creates:

```text
Predicted_Datasets.xls
```

with:

```text
RID No
Post ID
Text
Prediction
```

---

# 24. Updating the Dataset Safely

Before modifying the training dataset:

1. Make a backup.

```text
Social_Media_Datasets_backup.csv
```

2. Keep the same columns.

```text
id
post_id
text
label
```

3. Ensure `label` is only:

```text
0
1
```

4. Avoid empty text values.

5. Avoid duplicate IDs.

6. Save the file as UTF-8 CSV.

7. Retrain the models.

---

# 25. Troubleshooting

## Error: `ModuleNotFoundError: No module named 'django'`

Run:

```bash
pip install django==3.0.14
```

Make sure the virtual environment is active.

---

## Error: `No module named 'pandas'`

Run:

```bash
pip install pandas
```

---

## Error: `No module named 'sklearn'`

Run:

```bash
pip install scikit-learn
```

---

## Error: `No module named 'xlwt'`

Run:

```bash
pip install xlwt
```

---

## Error: MySQL connection failed

Check:

```text
MySQL Server is running
```

Then verify:

```python
'NAME': 'privacy_preserving_federated_depression_detection',
'USER': 'root',
'PASSWORD': 'YOUR_PASSWORD',
'HOST': '127.0.0.1',
'PORT': '3306',
```

---

## Error: `Access denied for user 'root'`

Your MySQL password is probably different from the password in `settings.py`.

Update:

```python
'PASSWORD': 'your_actual_mysql_password'
```

---

## Error: `Unknown database`

Create the database:

```sql
CREATE DATABASE privacy_preserving_federated_depression_detection;
```

Then import:

```text
Database/privacy_preserving_federated_depression_detection.sql
```

---

## Error: `No module named MySQLdb`

Install:

```bash
pip install mysqlclient
```

---

## Error related to `django.conf.urls.url`

This project uses an older Django URL API.

Do not immediately install the newest Django version.

Use a compatible Django version:

```bash
pip uninstall django
pip install django==3.0.14
```

---

## Error: `FileNotFoundError: Social_Media_Datasets.csv`

The code uses:

```python
pd.read_csv('Social_Media_Datasets.csv')
```

Therefore, run the server from the directory containing both:

```text
manage.py
Social_Media_Datasets.csv
```

Correct:

```bash
cd privacy_preserving_federated_depression_detection
python manage.py runserver
```

---

## Error: MySQL is using another port

If MySQL uses a port such as:

```text
3307
```

change:

```python
'PORT': '3306',
```

to:

```python
'PORT': '3307',
```

---

# 26. Fresh Database Setup Without the SQL Dump

If you want to create a completely new database instead of importing the supplied dump:

### 1. Create database

```sql
CREATE DATABASE privacy_preserving_federated_depression_detection;
```

### 2. Configure `settings.py`

Set your MySQL credentials.

### 3. Run migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

### 4. Create an admin user if required

```bash
python manage.py createsuperuser
```

However, note that the project's custom Service Provider login is separate from Django's standard admin authentication.

---

# 27. Important Data Locations

| Data | Location |
|---|---|
| ML training dataset | `Social_Media_Datasets.csv` |
| Latest generated training results | `Results.csv` |
| MySQL dump | `Database/privacy_preserving_federated_depression_detection.sql` |
| User data | MySQL |
| Prediction data | MySQL |
| Model accuracy | MySQL |
| Prediction ratio | MySQL |
| HTML templates | `Template/htmls/` |
| Images | `Template/images/` |
| Uploaded/media files | `Template/media/` |

---

# 28. Security Considerations

This repository is an academic/demo implementation and should not be deployed as-is for real medical or mental-health use.

The current implementation has several areas that should be improved:

### Plain-text passwords

The custom user table stores passwords directly.

Use Django's password hashing system in a production implementation.

### Hardcoded Service Provider credentials

Current demo:

```text
Admin / Admin
```

Move authentication to Django's authentication and authorization framework.

### Debug mode

Current project:

```python
DEBUG = True
```

For production:

```python
DEBUG = False
```

### Secret key

The project currently contains a hardcoded Django secret key.

Generate a new secret key and store it in an environment variable.

### Database credentials

Do not store production passwords directly in `settings.py`.

Use environment variables.

### Sensitive health information

Depression-related text is sensitive personal information. A production system should implement:

- Encryption in transit
- Encryption at rest
- Strong authentication
- Authorization
- Audit logging
- Data minimization
- Consent
- Secure deletion
- Privacy-preserving ML techniques
- Appropriate regulatory/legal review

---

# 29. Important Research/Implementation Gap

The project documentation describes a target system containing:

```text
Mobile Health Data
        ↓
Local Client Training
        ↓
Encrypted Model Updates
        ↓
Federated Server
        ↓
Secure Aggregation
        ↓
Global Model
        ↓
Updated Client Models
```

A complete federated implementation would normally include:

- Multiple clients
- Local datasets on each client
- Local model training
- Federated Averaging (FedAvg)
- Global model aggregation
- Client/server communication
- Secure aggregation
- Differential privacy
- Encryption
- Client dropout handling
- Model evaluation across clients

The supplied Django source does not currently contain these components. It instead performs centralized training from the CSV file.

If extending the project into a true federated-learning system, the recommended architecture is:

```text
                  Federated Server
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
          Client 1    Client 2    Client N
             |           |           |
        Local Data  Local Data  Local Data
             |           |           |
        Local Model Local Model Local Model
             |           |           |
             +-----------+-----------+
                         |
                  Model Updates
                         |
                  Secure Aggregation
                         |
                    Global Model
```

---

# 30. Recommended Improvements

For a stronger final-year/production-oriented version, consider adding:

### Federated Learning

Use a framework such as:

- Flower
- TensorFlow Federated
- PySyft

### Better NLP

Replace basic `CountVectorizer` with:

- TF-IDF
- Word embeddings
- Transformer models
- BERT-style models

### Privacy

Implement:

- Differential privacy
- Secure aggregation
- Encryption
- Client authentication

### Better evaluation

Add:

```text
Accuracy
Precision
Recall
F1-score
ROC-AUC
Confusion Matrix
```

### Reproducible experiments

Use:

```python
train_test_split(
    X,
    y,
    test_size=0.20,
    random_state=42,
    stratify=y
)
```

This makes experiments more reproducible.

### Model persistence

Instead of training every time a user predicts, save the trained vectorizer and model using:

```text
joblib
```

Then load them for prediction.

This would significantly improve response time.

---

# 31. Quick Start

For experienced users:

```bash
# Go to project
cd Privacy_Preserving_Federated_Depression_Detection/privacy_preserving_federated_depression_detection

# Create environment
python -m venv venv

# Activate Windows environment
venv\Scripts\activate

# Install dependencies
pip install django==3.0.14 pandas scikit-learn xlwt mysqlclient

# Configure MySQL in settings.py

# Create/import database
# Import:
# Database/privacy_preserving_federated_depression_detection.sql

# Check project
python manage.py check

# Start server
python manage.py runserver
```

Open:

```text
http://127.0.0.1:8000/
```

---

# 32. Demo Credentials

### Service Provider

```text
Username: Admin
Password: Admin
```

### Remote User

The SQL dump contains sample users, but it is recommended to create a new user through:

```text
Remote User → Register
```

For security, change the authentication implementation before using this project outside a controlled academic demonstration.

---

# 33. Author / Academic Project

**Project Title:**

**Privacy-Preserving Federated Depression Detection from Multisource Mobile Health Data**

**Domain:**

```text
Machine Learning
Natural Language Processing
Healthcare AI
Privacy-Preserving AI
Federated Learning
Django Web Development
MySQL
```

---

## License / Usage

This project is intended for academic, educational, and research purposes.

It should **not** be used as a medical diagnostic system. A prediction from this software is not a clinical diagnosis and should not replace assessment by a qualified mental-health professional.

---

## Final Run Checklist

Before running the application, verify:

- [ ] Python installed
- [ ] Virtual environment activated
- [ ] Django 3.0.x installed
- [ ] Pandas installed
- [ ] Scikit-learn installed
- [ ] xlwt installed
- [ ] MySQL installed and running
- [ ] Database created
- [ ] SQL dump imported
- [ ] MySQL username/password configured
- [ ] `Social_Media_Datasets.csv` is beside `manage.py`
- [ ] `python manage.py check` succeeds
- [ ] `python manage.py runserver` starts successfully
- [ ] `http://127.0.0.1:8000/` opens
- [ ] Remote User can register/login
- [ ] Prediction page works
- [ ] Service Provider can log in
- [ ] Training page works
- [ ] Accuracy/results can be viewed
- [ ] Predictions can be downloaded
# Privacy-Preserving-Federated-Depression-Detection
