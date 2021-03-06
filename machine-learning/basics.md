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
    1. Type of learning
        1. Batch learning
            1. All of your data exists in a big static warehouse and you train a model on it
            1. Learning may take a while and isn't done often
        1. Online learning
            1. Data is constantly being updated and you constantly train new models on it
            1. Each learning step is usually fast and cheap
            1. Runs in production and learns continuously
        1. Transfer learning
            1. Take the knowledge one model has learned and use it with your own. Gives you the ability to leverage state of the art models for your own problems
                1. Helpful if you don't have much data or vast compute resources. Use resources such as TensorFlow Hub or PyTorch Hub for broad model options and HuggingFace transformers (NLP models) and Detectron2 (computer vision models) for specific models
        1. Active learning
            1. Also referred as "human in the loop" learning. A human expert interacts with a model and provides updates to labels for samples which the model is most uncertain about
            1. Example: https://blogs.nvidia.com/blog/2020/01/16/what-is-active-learning/
        1. Ensembling
    1. Overfit then regularize
        1. Happens when your validation loss (how your model is performing on the validation dataset, lower is better) starts to increase
        1. Or when the model performs far better on the training set than on the test set (e.g. 99% accuracy on training set, 67% accuracy on the test set)
        1. Regularization: a collection of techniques to prevent/reduce overfitting
            1. L1 (lasso) and L2 (ridge) regularization
            1. Dropout: randomly remove parts of your model so the rest of it has to become better
            1. Early stopping: stop your model from training before the validation loss starts to increase too much or more generally, any other metric has stopped improving
            1. Data augmentation: manipulate your dataset in artificial ways to make it 'harder to learn'. For example, if you're dealing with images, randomly rotate, skew, flip and adjust the height of your images. This makes your model have to learn similar patterns across different styles of the same image
            1. Batch normalization: standardize inputs (zero mean and normalize) as well as adding two parameters (beta, how much to offset the parameters for each layer and epsilon to avoid division by zero) before they go into the next layer
    1. Tune hyperparameters
        1. Setting a learning rate (often the most important hyperparameter), generally, high learning rate = algorithm rapidly adapts to new data. Low learning rate = algorithm adapts slower to new data
            1. Finding the optimal learning rate:
                1. Train the model for a few hundred iterations starting with a very low learning rate (e.g. 10e-6) and slowly increase it to a very large value (e.g. 1)
                1. Plot the loss versus the learning rate (using a log scale for learning rate), you should see a U-shapped curve, the optimal learning is about 1-2 notches to the left of the bottom of the U-curve
            1. Learning rate schedule (use Adam optimizer) involves decreasing the learning rate slowly as model learns more
            1. Cyclic learning rate (https://arxiv.org/abs/1506.01186)
                1. Dynamically change the learning rate up and down between some threshold and potentially speed up training
        1. Read this useful paper: https://arxiv.org/abs/1803.09820
        1. Other hyperparameters to tune:
            1. Number of layers (deep learning networks)
            1. Batch size (how many examples of data your model sees at once. If in doubt, use batch size 32
            1. Number of trees (decision tree algorithms)
            1. Number of iterations (how many times the model goes through the data). Note: instaed of tuning iterations, use early-stopping instead
            1. etc. depends on the algorithn you are using. Search "[algorithm name] hyperparameter tuning"
4. Analysis/evaluation
    1. Evaluation metrics
        1. Classification
            1. Accuracy
            1. Precision
            1. Recall
            1. f1
            1. Confusion matrix
            1. Mean average precision (object detection)
        1. Regression
            1. MSE (mean squared error)
            1. MAE (mean absolute error)
            1. R^2 (r-squared)
        1. Task-based metric
    1. Feature importance
        1. Which features contributed most to the model? Should some be removed? Useful for model explainability
    1. Training/inference cost
        1. How long does a model take to train? Is this feasible?
        1. How long does inference take? Is it suitable for production?
    1. What-if tool (https://pair-code.github.io/what-if-tool/): how does my model compare to other models? What if I changed something in the data?
    1. Least confident examples: what does the model get wrong?
    1. Bias/variance trade-off: high bias results in underfitting and a lack of generalization to new samples, high variance results in overfitting due to the model finding patterns in the data which is actually random noise (https://elitedatascience.com/bias-variance-tradeoff)
5. Serve model
    1. MLOps (https://huyenchip.com/2020/06/22/mlops.html)
6. Retrain model
    1. Are old predictions still valid?
    1. See how the model performs after serving based on various evaluation metrics and revisit the above steps as required
  
## Machine Learning Tools

1. Libraries/code space
    1. Jupyter notebook
    1. TensorFlow
    1. PyTorch
    1. Onnx
2. Experiment tracking
    1. TensorBoard
    1. neptune.ai
3. Pre-trained models
    1. Detectron2
    1. TensorFlow
    1. PyTorch
    1. HuggingFace Transformers
4. Data and model tracking
    1. Artifacts (by weights & biases)
    1. Data version control (dvc)
5. Cloud compute services
    1. GCP
    1. AWS
    1. Azure
6. AutoML & Hyperparameter tuning
    1. Sweeps (by weights & biases)
    1. Google Cloud AutoML
    1. tpot
    1. Azure AutoML
7. Explainability
    1. shap
    1. what if...
8. ML lifecycle
    1. kubeflow
    1. Seldom
    1. mlflow

## Mathematics

1. Linear Algebra
2. Matrix manipulation
3. Multivariable calculus
4. The chain rule
5. Probability + distributions
6. Optimization
