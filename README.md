# Well Drilling Decision Support System

This project implements a machine learning-based decision support system for well drilling operations. The system predicts potential failures during drilling operations and recommends whether to continue drilling or perform repairs based on real-time data.

## Overview

The system connects to a well drilling API, collects operational data, and uses a trained machine learning model to make "Drill" or "Repair" decisions to optimize operations and prevent failures.

## Features

* Automated decision-making for well drilling operations
* Real-time data processing and prediction
* Adaptive model retraining to improve performance
* Handling of API timeouts and errors
* Support for multiple machine learning classifiers (SVC, LogisticRegression, KNeighborsClassifier)

## Requirements

* Python 3.x
* pandas
* scikit-learn
* requests

## How It Works

This system leverages historical well drilling data and machine learning to make informed decisions about whether to "Drill" or "Repair" during a drilling operation. Here's a breakdown of its key components:

### Data Preparation

The system begins by preparing the data using three CSV files containing historical well drilling information. This process involves:

* **Combining data from multiple sources:** Integrating information from the different CSV files into a unified dataset.
* **Creating a 'previous\_failure' feature:** Engineering a new feature that indicates if a well has experienced a failure in the past.
* **Selecting relevant features for the model:** Identifying and choosing the most pertinent features from the dataset that will contribute to accurate predictions.
* **Preprocessing categorical and numerical features:** Preparing the selected features for the machine learning model by handling different data types appropriately. This includes techniques like encoding categorical variables and scaling numerical ones.

### Model Training

A machine learning pipeline is constructed to train the decision-making model. This pipeline consists of the following stages:

* **Feature preprocessing:** Applying transformations to the selected features. This typically involves:
    * **Scaling for numerical features:** Standardizing or normalizing numerical data to ensure that features with larger ranges do not disproportionately influence the model.
    * **One-hot encoding for categorical features:** Converting categorical variables into a numerical format that the machine learning algorithm can understand.
* **A classifier:** Implementing a machine learning classification algorithm to predict whether to drill or repair. The default classifier is a Support Vector Machine (SVM), but the system also offers options for:
    * `LogisticRegression`
    * `KNeighborsClassifier`
* **Class weighting:** Addressing the issue of imbalanced data (where one class might have significantly more instances than the other) by assigning different weights to the classes during training. This helps the model to pay more attention to the minority class.

### Decision Making Process

During each turn of the drilling operation, the system follows these steps to decide whether to drill or repair:

1.  **The system receives the current well conditions:** Obtaining real-time data about the state of the well.
2.  **Processes the data through the model:** Feeding the current well conditions into the trained machine learning model after applying the necessary preprocessing steps.
3.  **Determines whether to "Drill" or "Repair":** The model outputs a prediction indicating the optimal action to take.
4.  **Sends the decision to the API:** The predicted action ("Drill" or "Repair") is communicated to the well drilling API.
5.  **Processes the response for the next iteration:** The system receives feedback from the API, which likely includes updated well conditions, and prepares for the next decision-making cycle.

### Adaptive Learning

To continuously improve its performance, the system incorporates an adaptive learning mechanism:

* **Every 500 iterations:**
    * **Updates the class weights:** Re-evaluating and adjusting the class weights based on the accumulated data from the recent iterations. This allows the system to adapt to potential shifts in the data distribution.
    * **Retrains the model:** Re-training the entire machine learning model using all the accumulated data. This ensures that the model learns from the latest experiences and can make more accurate predictions over time.

### Error Handling

The system is designed with robust error handling capabilities to ensure smooth operation:

* **API timeouts (with fallback endpoints):** Implementing mechanisms to handle situations where the primary API endpoint does not respond in a timely manner. This includes the use of alternative or fallback API endpoints to maintain connectivity.
* **Request failures:** Managing errors that occur during communication with the API, such as network issues or invalid requests.
* **Proper session cleanup:** Ensuring that drilling sessions are properly terminated and resources are released when they are no longer needed.

### API Endpoints

The system interacts with a well drilling API located at `http://10.68.195.110:8000` using the following endpoints:

* `/well/start/{well_id}` - Initiates a new drilling session for a specific well.
* `/well/drill/{session_id}` - Sends a command to perform a drilling operation for a given session.
* `/well/repair/{session_id}` - Sends a command to perform a repair operation for a given session.
* `/well/current/{session_id}` - Retrieves the current status and conditions of a specific drilling session (used as a fallback).
* `/well/end/{session_id}` - Terminates a specific drilling session.

### Configuration

Several key parameters of the system can be modified to adjust its behavior and performance:

* **Model selection:** Choosing between `SVC` (Support Vector Classifier), `LogisticRegression`, or `KNeighborsClassifier` for the classification task.
* **Class weights:** Manually setting or adjusting the weights assigned to different classes to handle data imbalance.
* **Feature selection:** Specifying which features from the prepared data should be used for training the model.
* **Retraining frequency:** Modifying the interval (currently every 500 iterations) at which the model is retrained.

### Limitations

It's important to consider the following limitations of the system:

* **The system requires connectivity to the well drilling API:** The system's functionality is entirely dependent on its ability to communicate with the specified API endpoints. Any disruption in connectivity will prevent the system from operating.
* **Performance is dependent on the quality of historical training data:** The accuracy and effectiveness of the machine learning model are directly influenced by the quality, representativeness, and relevance of the historical well drilling data used for training. Biased or incomplete data can lead to suboptimal decision-making.
* **The model may require further tuning for specific well conditions:** The default model configuration and training might not be optimal for all types of wells or drilling conditions. Further experimentation and tuning of hyperparameters and model architecture might be necessary to achieve the best performance in specific scenarios.
