# SBLA-ANN-MODEL-
 Botnet Attack Detection Using a Bidirectional Long Short-Term Memory Network with Self-Attention and Synthetic Minority Oversampling
SBLA-ANN: Botnet Attack Detection Using a Bidirectional Long Short-Term Memory Network with Self-Attention and Synthetic Minority Oversampling
1. Project Description  
SBLA-ANN is a deep-learning-based system for detecting network intrusions, designed to classify network traffic as benign or malicious and to identify attack categories in multiclass settings. This repository provides the model implementation, dataset-specific experiment workflows, evaluation procedures, and reproducibility information.

The model processes network-traffic feature vectors after they have been scaled and reshaped into sequential representations. Its architecture consists of:

A Bidirectional Long Short-Term Memory (BiLSTM) layer for learning contextual representations across sequence positions.
A Synthetic Minority Over-sampling Technique (SMOTE) for handling class imbalance 
A self-attention mechanism (`Attention()(\[x, x])`) for weighting learned representations.
A flattening operation.
Fully connected ANN layers with ReLU activation and batch normalization.
A sigmoid and softmax output layer for binary or multiclass classification.
The implementation is evaluated on three network-traffic datasets under binary and multiclass classification settings, using both holdout evaluation and five-fold stratified cross-validation.

Key Features
BiLSTM feature representation: learns bidirectional contextual representations across the ordered input-feature dimensions.
Self-attention: applies attention to the BiLSTM sequence representations.
Binary and multiclass detection: supports benign-versus-attack classification and attack-category classification.
Multiple benchmark datasets: evaluates the approach on BoT-IoT, CICIoV2024, and IoT-23.
Two evaluation protocols: includes stratified holdout evaluation and five-fold stratified cross-validation.
Class-imbalance handling: applies SMOTE to training data only.
Comprehensive evaluation: reports classification metrics, confusion matrices, ROC curves, learning curves, and resource-related measurements.
Table of Contents
Project Description
Key Features
Dataset Information
Repository Structure
Methodology
Requirements and Dependencies
Usage Instructions
Experimental Reproducibility
License
Contact

2. Dataset Information
The experiments use four network intrusion-detection datasets.
Dataset	Original samples	Original columns/features reported	Post-preprocessing information
CICIoV2024	1,408,219	13	1,408,219 records retained; 10 features after removal of three redundant target-related columns
BoT-IoT	9,000,000	35	22 features retained after removing 13 redundant or non-informative columns
IoT-23	6,046,623	22	Binary: all 6,046,623 records retained. Multiclass: 6,046,522 records across seven classes; 18 input features and one target
2.1 CICIoV2024
CICIoV2024 contains 1,408,219 records and 13 reported features, including numerical, categorical, and object-valued attributes. The dataset includes traffic associated with DoS, Gas, Rpm, Speed, and Normal categories. The preprocessing described in the methodology retains all 1,408,219 records and removes three redundant target-related columns, leaving 10 features.
2.2 BoT-IoT
BoT-IoT contains 9,000,000 records and 35 reported features. The dataset includes attack and normal traffic, including Data Infiltration, TCP, and HTTP-related categories. The preprocessing retains 22 features and removes 13 attributes considered redundant or non-informative, including identifier fields (such as MAC and IP addresses) and redundant categorical descriptors (such as `category` and `attack`). Categorical fields such as protocol type are converted to integer codes using label encoding. For multiclass classification, the `subcategory` target is label-encoded; examples of classes described in the methodology include DDoS, Theft, Reconnaissance, and Benign traffic.
2.3 IoT-23
IoT-23 is loaded from a CSV file containing 6,046,623 network-traffic records and 22 columns. The attributes include connection and network information such as addresses, ports, protocol, service, connection state, duration, packet counts, byte counts, and labels.
For binary classification, records labelled exactly `Benign` are assigned to the benign class and all other records are assigned to the attack class. This produces 5,357,811 attack records and 688,812 benign records, retaining all 6,046,623 records.
For multiclass classification, `Unnamed: 0`, `ts`, and `uid` are removed as index, timestamp, and connection-identifier fields. The `PartOfA` prefix is removed where applicable, and whitespace is stripped from class names. Classes with fewer than 50 records are excluded. The resulting dataset contains 6,046,522 records across seven classes: `HorizontalPortScan`, `Okiru`, `Benign`, `DDoS`, `C\&C`, `C\&C-HeartBeat`, and `Attack`. The methodology reports 18 input features and one target label.
Dataset files and paths
The notebooks refer to the following dataset locations or filenames:
BoT-IoT: CSV data files loaded from the `BoT-IoT` directory; confirm the exact filenames and path variables in the relevant notebook.
CICIoV2024: main path CICIoV2024 with following files in it
decimal_benign.csv
decimal_DoS.csv
decimal_spoofing-GAS.csv
decimal_spoofing-RPM.csv
decimal_spoofing-SPEED.csv
decimal_spoofing-STEERING_WHEEL.csv
IoT-23: `IoT-23/iot23\_combined\_new.csv`
The datasets are not distributed with this repository. Download them from the sources listed below, and comply with each dataset's license and terms of use.
Dataset download links
Dataset	Source
BoT-IoT	Kaggle – BoT-IoT dataset
CICIoV2024	Kaggle – CICIoV2024 dataset
IoT-23	Kaggle – IoT-23 dataset

Repository Contents
The project is organized around the following experiment groups:
Holdout experiments: binary and multiclass classification for each of the four datasets.
Five-fold cross-validation experiments: binary and multiclass classification for each dataset, using stratified folds.
Dataset directories: locations for the source CSV files used by the preprocessing and evaluation workflows.
Evaluation outputs: classification metrics, confusion matrices, ROC curves, learning curves, and resource-usage summaries generated during execution.

3. Repository Structure
The experiments are organized into two evaluation protocols.
3.1 Holdout experiments
Notebook	Dataset	Classification
`Binary\_BoT-IoT-SBLA-ANN.ipynb`	BoT-IoT	Binary
`Multi\_Bot\_IoT\_SBLA-ANN.ipynb`	BoT-IoT	Multiclass
`Binary\_CICIoV2024\_SBLA-ANN.ipynb`	CICIoV2024	Binary
`Multi\_CICIoV2024\_SBLA-ANN.ipynb`	CICIoV2024	Multiclass
`Binary\_IoT23\_SBLA-ANN.ipynb`	IoT-23	Binary
`Multi\_IoT23\_SBLA-ANN.ipynb`	IoT-23	Multiclass
3.2 Five-fold cross-validation experiments
Notebook	Dataset	Classification
`Binary-BoT-IoT\_SBLA-ANN\_5cv-Final.ipynb`	BoT-IoT	Binary
`Multi\_Bot\_IoT\_SBLA-ANN\_5cv.ipynb`	BoT-IoT	Multiclass
`Binary-CICoV2024\_SBLA-ANN\_5cv.ipynb`	CICIoV2024	Binary
`Multi\_CICIoV2024\_SBLA-ANN\_5cv.ipynb`	CICIoV2024	Multiclass
`Binary-IoT23\_SBLA-ANN\_5cv.ipynb`	IoT-23	Binary
`Multi\_IoT23\_SBLA-ANN\_5cv.ipynb`	IoT-23	Multiclass
4. Methodology
The experimental workflow consists of data cleaning, preprocessing, class balancing, sequential feature representation, model training, and evaluation.
4.1 Data cleaning and preprocessing
The preprocessing pipeline uses the following steps:
Missing-value treatment: Missing numerical values are replaced using median imputation.
Removal of non-informative attributes: Dataset-specific identifiers, metadata, duplicate information, redundant columns, and target-related fields are removed where appropriate to reduce noise and prevent data leakage.
Categorical encoding: Categorical attributes are converted to numerical representations where required. Target labels are converted to integer class indices; `LabelEncoder` is used in the relevant multiclass processing.
Dataset-specific label processing: For example, IoT-23 labels are standardized by removing the `PartOfA` prefix where applicable and stripping leading/trailing whitespace. Classes with fewer than 50 records are excluded from the IoT-23 multiclass experiment.
The precise columns and transformations are dataset-specific and are applied as part of the corresponding dataset preprocessing workflow.
4.2 Data splitting
Holdout evaluation
The holdout experiments use stratified sampling with `random\_state=42`:
70% training data.
15% validation data.
15% test data.
The initial 30% temporary partition is divided equally between validation and testing, with stratification preserved.
Five-fold cross-validation
Five-fold cross-validation uses `StratifiedKFold` with five splits, shuffling enabled, and `random\_state=42`. In each iteration:
Four folds (80% of the data) form the training portion.
The remaining fold (20%) is split equally into validation and test partitions.
This gives approximately 80% training, 10% validation, and 10% test data per iteration.
The fold assignment rotates so each fold serves as the held-out portion once.
The held-out fold is split into validation and test subsets rather than being used simultaneously as two separate folds.
4.3 Feature scaling
`StandardScaler` is used for Z-score standardization. It is fitted using only the training portion and then applied to the corresponding validation and test portions. This prevents information from the held-out data from influencing the fitted scaling parameters.
4.4 Class imbalance handling with SMOTE
The Synthetic Minority Over-sampling Technique (SMOTE) is applied only to the training data. Validation and test partitions retain their original class distributions and are not oversampled.
SMOTE is applied with `random\_state=16` and a dataset- and task-specific `sampling\_strategy`. The target resampling value is selected according to each dataset's class distribution and may differ between binary and multiclass experiments.
The `sampling\_strategy` is configured separately for each dataset and classification task; values may therefore differ across experiments.
4.5 Sequential feature representation
After scaling and training-set resampling, each feature matrix is reshaped from a two-dimensional representation `(samples, F)` into a three-dimensional representation `(samples, F, 1)`:
```python
X\_reshaped = X\_scaled.reshape(
    (X\_scaled.shape\[0], X\_scaled.shape\[1], 1)
)
```
Here, `F` is the number of input features. Each feature dimension is treated as an ordered sequence position with one value at that position. These positions represent feature dimensions, not chronologically ordered network observations. Accordingly, the BiLSTM acts as a contextual feature encoder across the ordered feature representation; it should not be described as learning temporal dependencies between network events from this reshaping alone.
4.6 SBLA-ANN architecture
The SBLA-ANN architecture consists of the following components:
Input: Receives the reshaped feature representation.
Bidirectional LSTM: Processes the sequence in forward and backward directions and returns sequence representations.
Self-attention: Keras `Attention` is applied to the BiLSTM output using the same tensor as query and value:
```python
   x = Attention()(\[x, x])
   ```
Flatten: Converts the attention output into a one-dimensional representation.
Dense layers: Fully connected layers use ReLU activation.
Batch normalization: Applied within the dense network.
Output: A sigmoid and softmax layer produces class probabilities for binary or multiclass classification.
The architecture's units, dense-layer sizes, and L2 regularization settings are configurable experiment parameters.
4.7 Model training and callbacks
Model training uses the Adam optimizer and sparse categorical cross-entropy loss. The common training configuration includes a batch size of 256 and a maximum of 100 epochs. Training uses validation data and callbacks including:
`EarlyStopping`, monitoring `val\_loss`, with patience 10 and restoration of the best weights.
`ReduceLROnPlateau`, monitoring `val\_loss`, with factor 0.5, patience 5, and minimum learning rate `1e-6`.
`ModelCheckpoint`, saving the best model as `best\_model.keras` based on validation loss.
Experiment-specific model parameters and configuration values are defined in the corresponding experiment workflow.
4.8 Hyperparameter selection
A Grid Search strategy is used that considers BiLSTM units, dense-layer neuron configurations, batch size, and L2 regularization strengths. Candidate configurations are compared using validation performance, and the selected configuration is intended for subsequent experiments.

4.9 Performance evaluation
Classification performance is evaluated using the following metrics and visualizations:
Accuracy, precision, recall, and F1-score.
False Positive Rate (FPR).
Area Under the ROC Curve (ROC-AUC).
Geometric Mean (G-Mean).
Matthews Correlation Coefficient (MCC).
Confusion matrices and normalized confusion matrices.
ROC curves.
Training and validation accuracy/loss curves.
The evaluation workflow also includes model-complexity and resource-usage analyses where applicable. Exact measurements depend on the selected dataset, classification task, and evaluation protocol.
5. Requirements and Dependencies
The implementation uses Python and the following libraries:
NumPy
Pandas
Scikit-learn
TensorFlow
Keras
imbalanced-learn
SciPy
Matplotlib
Seaborn
psutil
IPython
The implementation also uses standard Python modules, including:
`os`
`time`
`itertools`
`subprocess`
`collections`
GPU monitoring is supported through `nvidia-smi` in the relevant experiment workflows. NVIDIA GPU monitoring requires a compatible NVIDIA driver and an available `nvidia-smi` executable. CPU execution may be possible, although training time can increase substantially.
Installation
Create and activate a Python environment, then install the required packages:
```bash
pip install numpy pandas scikit-learn tensorflow keras imbalanced-learn scipy matplotlib seaborn psutil ipython jupyter
```
6. Usage Instructions
Step 1: Obtain the repository
Clone the repository or download the project files.
Step 2: Install dependencies
Install the required Python libraries using the installation command above.
Step 3: Obtain the datasets
Download the four datasets from their respective sources. Place the data files in the directories described in the Dataset Information section, or update the dataset paths in the experiment configuration.
Step 4: Open Jupyter Notebook
Start Jupyter:
```bash
jupyter notebook
```
Alternatively, open the notebooks using JupyterLab or Google Colab.
Step 5: Select an experiment
Choose the experiment workflow corresponding to the required dataset, classification task, and evaluation strategy.
For example:
Binary classification on BoT-IoT using holdout evaluation: `Binary\_BoT-IoT-SBLA-ANN.ipynb`
Multiclass classification on CICIoV2024 using five-fold cross-validation: `Multi\_CICIoV2024\_SBLA-ANN\_5cv.ipynb`
Step 6: Configure dataset paths
Configure the dataset path variables according to your local directory structure.
Step 7: Execute the notebook
Run the experiment workflow in sequence to perform data preparation, model construction, training, and evaluation.
Step 8: Examine the results
Review the generated metric tables, confusion matrices, ROC curves, training curves, and resource-usage summaries.
7. Experimental Reproducibility
The experiments use fixed random seeds for data splitting, cross-validation, and SMOTE. The common settings include:
Data splitting random state: `42`
Five-fold cross-validation random state: `42`
SMOTE random state: `16`
Exact reproducibility can also depend on the software environment, TensorFlow version, hardware, GPU operations, and dataset versions. For research comparisons, retain the original datasets, notebook configurations, and generated results.

8. License
No license has currently been specified for this repository. All rights are reserved by default. Please contact the repository owner for permission before using, modifying, or redistributing the code.
9. Contact
For questions about the implementation or research, contact:
Project/Researcher: [Sadaf AlRaisi]
Email: [sadafalraisi20@gmail.com]
Project paper link : [https://github.com/sadafalraisi/SBLA-ANN-MODEL-.git]
