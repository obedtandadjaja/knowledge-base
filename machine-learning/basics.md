# Machine Learning

Machine learning is turning things (data) into numbers and finding patterns in those numbers.

Given input and output, the machine tries to figure out the "steps" or "rules".

If you can build a simple rule-based system that doesn't require machine learning, do that.

Machine learning is good for:
- Problems with long lists of rules
- Continually changing environments
- Discovering insights withing large collections of data

## Machine Learning Process

1. Data collection
    1. What data exists?
    1. Where can you get the data?
    1. Is the data structured (nominal/categorical, numerical, ordinal [based on undefined unit of measurement], time series) or unstructured?
    1. What kind of problem are we trying to solve?
    1. What privacy concerns are there?
    1. Is the data public?
    1. Where should we store the data?
2. Data preparation
    1. Exploratory data analysis
        1. What are the feature variables (input) and the target variables (output)?
        1. Are there missing values? Should you remove them aor fill them with feature imputation?
        1. Where are the outliers? How many are there? Are they out by much (3+ standard deviations)?
        1. Are there questions you could ask a domain exper about the data?
    1. Data preprocessing
        1. Feature imputation: filling missing values
            1. Single imputation: fill with mean, median of column
            1. Multiple imputation: model other missing values and fill with what your model finds
            1. KNN (k-nearest neighbors): fill data with a value from another example which is familiar
            1. Random imputation
            1. Last observation carried forward (for time series)
            1. Moving window
            1. Most frequent
        1. Feature encoding (turning values into numbers). A machine learning model requires all values to be numerical
            1. OneHotEncoding: turn all unique values into lists of 0's and 1's, e.g. [0,1,0,0]
            1. LabelEncoder: turn labels into distinct numerical values
            1. Embedding encoding: learn a representation amongst all the different data points. It is a relatively low-dimensional space into which you can translate high-dimensional vectors. Embeddings make it easier to do machine learning on large inputs like sparse vectors representing words
        1. Feature normalization (scaling) or standardization. When your numerical variables are on different scales (e.g. 1-5 and 5000-20000), some machine learning algorithms don't perform very well
        1. Feature scaling: shifts values so they appear between 0 and 1
        1. Feature standardization: standardizes all values so they have a mean of 0 and unit variance. It happens by subtracting the mean and dividing by the standard diviation of that particular feature. Doing this means values do not end up between 0 and 1 (their range uncapped). This method is more robust to outliers than feature scaling.
        1. Feature engineering: transform data into more meaningful representations by adding in domain knowledge
            1. Decompose - extract values from date into discrete fields
            1. Discretization (turning large groups into smaller groups)
                1. Bucketing
                1. Combining similar categories into the same categorical variable
            1. Crossing features - combine multiple features
            1. Indicator features - use other parts of the data to indicate something significant (based on domain knowledge), e.g. under_100km_auto_under_10yo
        1. Feature selection: select the most valuable features of the dataset to model. Reduces overfitting and training time and improving accuracy
            1. Dimensionaility reduction. PCA (Principal Component Analysis) uses linear algebra to reduce data to less dimensions
            1. Feature importance (post modelling). Fit a model to a set of data, then inspect which features were most important to the results, remove the least important ones
        1. Dealing with imbalances: 10,000 examples of one class but only 100 examples of another
            1. Use SMOTE (syntetic minority over-sampling technique)
            1. Read "Learning from Imbalanced Data" paper.
    1. Data splitting
3. Train a model
    1. Choose an algorithm (based on your problem/data)
    1. Overfit then regularize
    1. Tune hyperparameters
4. Analysis/evaluation
    1. Evaluation metrics
    1. Feature importance
    1. Training/inference cost
5. Serve model
6. Retrain model
    1. Are old predictions still valid?
  
## Machine Learning Tools

1. Libraries/code space
  a. Jupyter notebook
  b. TensorFlow
  c. PyTorch
  d. Onnx
2. Experiment tracking
  a. TensorBoard
  b. neptune.ai
3. Pre-trained models
  a. Detectron2
  b. TensorFlow
  c. PyTorch
  d. HuggingFace Transformers
4. Data and model tracking
  a. Artifacts (by weights & biases)
  b. Data version control (dvc)
5. Cloud compute services
  a. GCP
  b. AWS
  c. Azure
6. AutoML & Hyperparameter tuning
  a. Sweeps (by weights & biases)
  b. Google Cloud AutoML
  c. tpot
  d. Azure AutoML
7. Explainability
  a. shap
  b. what if...
8. ML lifecycle
  a. kubeflow
  b. Seldom
  c. mlflow

## Mathematics

1. Linear Algebra
2. Matrix manipulation
3. Multivariable calculus
4. The chain rule
5. Probability + distributions
6. Optimization
