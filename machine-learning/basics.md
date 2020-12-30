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
        1. Training set (usually 70-80% of data): model learns on this
        1. Validation set (usually 10-15% of data): model hyperparameters are tuned in this
        1. Test set (usually 10-15%): models final performance is evaluated on this
3. Train a model
    1. Choose an algorithm (based on your problem/data)
        1. Supervised algorithms
            1. Linear Regression (https://en.wikipedia.org/wiki/Linear_regression)
                1. Draw a line that best fits data scattered on a graph
            1. Logistic Regression (https://en.wikipedia.org/wiki/Logistic_regression)
                1. Predicts a binary outcome based on a series of independent variables
                1. E.g. predict someone has heart disease based on health parameters
            1. k-Nearest Neighbors (https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm)
                1. Find the k examples which are most similar to each other
            1. Support Vector Machines (SVMs) (https://towardsdatascience.com/support-vector-machine-simply-explained-fee28eba5496)
                1. Can be used for classification or regression
                1. Find best way to separate data points using multiple planes known as Hyperplanes
            1. Decision Trees and Random Forests (https://en.wikipedia.org/wiki/Random_forest)
                1. Can be used for classification and regression (very valuable algorithm for structured data)
                1. Decision trees split data based on criteria, such as "is over 50", eventually getting to a point where the data can't be split anymore
                1. Random Forests are a combination of many decision trees, effectively leveraging and combining the choices of many models (technique known as ensembling)
            1. AdaBoost/Gradient Boosting Machines (https://machinelearningmastery.com/gentle-introduction-gradient-boosting-algorithm-machine-learning/)
                1. Can be used for classification or regression
                1. Asks the question, can a series of weak learners be turned into a strong learner?
                1. Trains a series of weak learners whos job is to improve each other (another example of ensemble method, combining multiple weaker models to create a better one)
                    1. XGBoost (https://xgboost.readthedocs.io/en/latest/)
                    1. CatBoost (https://catboost.ai/)
                    1. LightGBM (https://lightgbm.readthedocs.io/en/latest/)
            1. Neural networks (deep learning) (https://en.wikipedia.org/wiki/Artificial_neural_network)
                1. Can be used for classification or regression
                1. Takes a series of inputs, manipulates the inputs with linear (dot product between weights and inputs) and nonlinear functions (activation function). Do this multiple times
                1. The use of linear and non-linear functions (straight or non-straight lines) means neural networks can use this combination to estimate almost anything
                    1. Convolutional neural networks (typically used for computer vision) (https://poloclub.github.io/cnn-explainer/)
                    1. Recurrent neural networks (typically used for sequence modelling) (https://karpathy.github.io/2015/05/21/rnn-effectiveness/)
                    1. Transformer networks (can be used for vision and text) (https://huggingface.co/transformers/)
        1. Unsupervised algorithms
            1. Clustering
                1. K-Means clustering (https://en.wikipedia.org/wiki/K-means_clustering)
                    1. Choose k number of clusters, each cluster receives a center node (centroid) at random and with each iteration the center nodes attempt to move farther away from each other
                    1. Once the centroids stop moving, each sample is assigned a value equivalent to the closest centroid
            1. Visualization and dimensionality reduction
                1. Principal Component Analysis (https://en.wikipedia.org/wiki/Principal_component_analysis)
                    1. Reduce data dimensions while attempt to preserve variance
                1. Autoencoder (https://www.jeremyjordan.me/autoencoders/)
                    1. Learn a lower dimensional encoding of data
                1. t-Distributed Stochastic Neighbor Embedding (t-SNE) (https://en.wikipedia.org/wiki/T-distributed_stochastic_neighbor_embedding)
                    1. Good for visualizing data in a 2D or 3D space
            1. Anomaly detection
                1. Autoencoder (https://www.jeremyjordan.me/autoencoders/)
                1. One-class classification
                    1. Train a model on only one-class if anything lays outside of this class, it may be an anomaly
                    1. Algorithms: one-class K-Means, one-class SVM, isolation forest, and local outlier factor
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
