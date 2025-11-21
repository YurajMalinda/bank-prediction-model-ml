# Bank Subscription Prediction Model

A machine learning web application that predicts whether a bank customer will subscribe to a term deposit based on their demographic and campaign information. This project is built using Flask, scikit-learn, and features a modern, responsive web interface.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [API Documentation](#api-documentation)
- [Deployment](#deployment)
- [Model Details](#model-details)
- [License](#license)
- [Author](#author)

## 🎯 Overview

This application uses a pre-trained Support Vector Machine (SVM) model to predict bank term deposit subscriptions. The model analyzes customer characteristics such as age, job type, marital status, education level, loan status, and campaign contact information to make predictions.

## ✨ Features

- **Interactive Web Interface**: Modern, responsive UI built with Tailwind CSS
- **Real-time Predictions**: Instant predictions via RESTful API
- **Input Validation**: Client-side validation with visual feedback
- **Error Handling**: Comprehensive error handling and user-friendly error messages
- **Docker Support**: Containerized application for easy deployment
- **CI/CD Pipeline**: Automated deployment to AWS ECS using GitHub Actions

## 🛠 Tech Stack

### Backend

- **Flask 2.0.2**: Web framework
- **scikit-learn 1.5.1**: Machine learning library
- **pandas 1.3.5**: Data manipulation
- **numpy 1.21.6**: Numerical computing
- **joblib 1.2.0**: Model serialization

### Frontend

- **HTML5/CSS3**: Structure and styling
- **Tailwind CSS**: Utility-first CSS framework
- **JavaScript (ES6+)**: Client-side interactivity
- **Font Awesome**: Icons

### Deployment

- **Docker**: Containerization
- **AWS ECS**: Container orchestration
- **AWS ECR**: Container registry
- **GitHub Actions**: CI/CD pipeline

## 📁 Project Structure

```
bank-prediction-model-ml/
├── app.py                 # Flask application and API endpoints
├── templates/
│   └── index.html        # Frontend web interface
├── svm_model.pkl         # Pre-trained SVM model
├── log_reg_model.pkl     # Pre-trained Logistic Regression model (not used)
├── scaler.pkl            # StandardScaler for feature normalization
├── requirements.txt      # Python dependencies
├── Dockerfile            # Docker configuration
├── .github/
│   └── workflows/
│       └── deploy.yml    # GitHub Actions CI/CD workflow
├── LICENSE               # MIT License
└── README.md            # Project documentation
```

## 🚀 Installation

### Prerequisites

- Python 3.9 or higher
- pip (Python package manager)
- Docker (optional, for containerized deployment)

### Local Setup

1. **Clone the repository**

   ```bash
   git clone <repository-url>
   cd bank-prediction-model-ml
   ```

2. **Create a virtual environment** (recommended)

   ```bash
   python -m venv venv

   # On Windows
   venv\Scripts\activate

   # On Linux/Mac
   source venv/bin/activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

4. **Ensure model files are present**
   - `svm_model.pkl`
   - `scaler.pkl`

## 💻 Usage

### Running Locally

1. **Start the Flask application**

   ```bash
   python app.py
   ```

2. **Access the application**
   - Open your browser and navigate to `http://localhost:80`
   - Fill in the form with customer information
   - Click "Predict" to get the prediction

### Using Docker

1. **Build the Docker image**

   ```bash
   docker build -t bank-prediction-model .
   ```

2. **Run the container**

   ```bash
   docker run -p 80:80 bank-prediction-model
   ```

3. **Access the application**
   - Open your browser and navigate to `http://localhost:80`

## 📡 API Documentation

### Endpoints

#### `GET /`

Returns the main web interface.

**Response**: HTML page

---

#### `POST /predict`

Makes a prediction based on customer data.

**Request Body** (JSON):

```json
{
  "age": 30,
  "job": "management",
  "marital": "married",
  "education": "university.degree",
  "housing": "yes",
  "loan": "no",
  "campaign": 2
}
```

**Request Parameters**:

- `age` (integer, required): Customer age (18-100)
- `job` (string, required): Job type
  - Options: `admin.`, `blue-collar`, `entrepreneur`, `housemaid`, `management`, `retired`, `self-employed`, `services`, `student`, `technician`, `unemployed`, `unknown`
- `marital` (string, required): Marital status
  - Options: `divorced`, `married`, `single`, `unknown`
- `education` (string, required): Education level
  - Options: `basic.4y`, `basic.6y`, `basic.9y`, `high.school`, `illiterate`, `professional.course`, `university.degree`, `unknown`
- `housing` (string, required): Housing loan status
  - Options: `no`, `yes`, `unknown`
- `loan` (string, required): Personal loan status
  - Options: `no`, `yes`, `unknown`
- `campaign` (integer, required): Number of contacts during this campaign (≥1)

**Response** (Success):

```json
{
  "prediction": "yes"
}
```

**Response** (Error):

```json
{
  "error": "Error message"
}
```

**Status Codes**:

- `200`: Success
- `400`: Bad Request (validation error or missing fields)

## 🚢 Deployment

> **Note**: AWS deployment services are currently disabled. The CI/CD pipeline will not work until AWS services are reactivated.

### AWS ECS Deployment

The project includes a GitHub Actions workflow for automated deployment to AWS ECS. However, this is currently disabled as AWS services have been shut down.

#### Prerequisites

- AWS account with ECS, ECR access
- GitHub repository with secrets configured:
  - `AWS_ACCESS_KEY_ID`
  - `AWS_SECRET_ACCESS_KEY`

#### Deployment Process

> **Status**: ⚠️ Currently disabled - AWS services are shut down

1. **Push to main branch**: The workflow automatically triggers on push to `main`
2. **Build and push**: Docker image is built and pushed to Amazon ECR
3. **Deploy**: ECS service is updated with the new task definition

**To reactivate**: Restore AWS services (ECS cluster, ECR repository) and ensure GitHub secrets are properly configured.

#### Manual Deployment

> **Note**: Manual deployment will fail if AWS services are not active.

1. **Build and tag Docker image**

   ```bash
   docker build -t bank-prediction-model .
   docker tag bank-prediction-model:latest <ecr-registry>/bank-prediction-model:latest
   ```

2. **Push to ECR**

   ```bash
   aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <ecr-registry>
   docker push <ecr-registry>/bank-prediction-model:latest
   ```

3. **Update ECS service**
   ```bash
   aws ecs update-service --cluster BankPredictionCluster --service bank-prediction-model-service --force-new-deployment
   ```

**Alternative**: For local development and testing, you can run the application using Docker locally (see [Usage](#-usage) section).

## 🤖 Model Details

### Model Information

- **Algorithm**: Support Vector Machine (SVM)
- **Preprocessing**: StandardScaler for feature normalization
- **Input Features**: 20 features including:
  - Demographic: age, job, marital, education
  - Financial: housing, loan, default
  - Campaign: contact, month, day_of_week, duration, campaign, pdays, previous, poutcome
  - Economic: emp.var.rate, cons.price.idx, cons.conf.idx, euribor3m, nr.employed

### Feature Encoding

Categorical features are encoded using label encoding:

- **Job**: 12 categories (admin., blue-collar, entrepreneur, etc.)
- **Marital**: 4 categories (divorced, married, single, unknown)
- **Education**: 8 categories (basic.4y, basic.6y, basic.9y, etc.)
- **Housing/Loan**: 3 categories (no, yes, unknown)

### Prediction Output

- `yes`: Customer is likely to subscribe to term deposit
- `no`: Customer is unlikely to subscribe to term deposit

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👤 Author

**Yuraj Malinda**

- Copyright (c) 2025 Yuraj Malinda

---

**Note**: This is a 4th semester final coursework project for the GDSE program at IJSE.
