# SBLA-ANN-MODEL-
SMOTE-Enhanced BiLSTM-Self-Attention ANN Model for Effective Botnet Attack Detection
 

## 1. Project Description

This repository contains the implementation and experimental notebooks for SBLA-ANN, a deep-learning-based network intrusion detection model.

The model processes network-traffic feature vectors after they have been scaled and reshaped into sequential representations. Its architecture consists of:

1. A Synthetic Minority Oversampling Technique (SMOTE) 
2. A Bidirectional Long Short-Term Memory (BiLSTM) layer for learning contextual representations across sequence positions.
3. A self-attention mechanism (`Attention()([x, x])`) for weighting learned representations.
4. A flattening operation.
5. Fully connected ANN layers with ReLU activation and batch normalization.
6. A sigmoid and softmax output layer for binary and multiclass classification.

The repository evaluates the model on three network-traffic datasets using binary and multiclass classification settings. Both holdout evaluation and six-fold cross-validation notebooks are provided.


## 2. Dataset Information

The experiments use three network intrusion-detection datasets. 

| Dataset | Original samples | Original columns/features reported | Post-preprocessing information |
|---|---:|---:|---|
| CICIoV2024 | 1,408,219 | 13 | 1,408,219 records retained; 10 features after removal of three redundant target-related columns |
| BoT-IoT | 9,000,000 | 35 | 22 features retained after removing 13 redundant or non-informative columns |
| IoT-23 | 6,046,623 | 22 | Binary: all 6,046,623 records retained. Multiclass: 6,046,522 records across seven classes; 18 input features and one target |


### 2.1 CICIoV2024

CICIoV2024 contains 1,408,219 records and 13 reported features, including numerical, categorical, and object-valued attributes. The dataset includes traffic associated with DoS, Gas, Rpm, Speed, and Normal categories. The preprocessing described in the methodology retains all 1,408,219 records and removes three redundant target-related columns, leaving 10 features.

### 2.2 BoT-IoT

BoT-IoT contains 9,000,000 records and 35 reported features. The dataset includes attack and normal traffic, including Data Infiltration, TCP, and HTTP-related categories. The preprocessing retains 22 features and removes 13 attributes considered redundant or non-informative, including identifier fields (such as MAC and IP addresses) and redundant categorical descriptors (such as `category` and `attack`). Categorical fields such as protocol type are converted to integer codes using label encoding. For multiclass classification, the `subcategory` target is label-encoded; examples of classes described in the methodology include DDoS, Theft, Reconnaissance, and Benign traffic.

### 2.3 IoT-23

IoT-23 is loaded from a CSV file containing 6,046,623 network-traffic records and 22 columns. The attributes include connection and network information such as addresses, ports, protocol, service, connection state, duration, packet counts, byte counts, and labels.

For binary classification, records labelled exactly `Benign` are assigned to the benign class and all other records are assigned to the attack class. This produces 5,357,811 attack records and 688,812 benign records, retaining all 6,046,623 records.

For multiclass classification, `Unnamed: 0`, `ts`, and `uid` are removed as index, timestamp, and connection-identifier fields. The `PartOfA` prefix is removed where applicable, and whitespace is stripped from class names. Classes with fewer than 50 records are excluded. The resulting dataset contains 6,046,522 records across seven classes: `HorizontalPortScan`, `Okiru`, `Benign`, `DDoS`, `C&C`, `C&C-HeartBeat`, and `Attack`. The methodology reports 18 input features and one target label.

### Dataset files and paths

The notebooks refer to the following dataset locations or filenames:

- **BoT-IoT:** CSV data files loaded from the `BoT-IoT` directory; confirm the exact filenames and path variables in the relevant notebook.

- **CICIoV2024:** main path CICIoV2024 with following files in it
	- decimal_benign.csv
	- decimal_DoS.csv
	- decimal_spoofing-GAS.csv
	- decimal_spoofing-RPM.csv
	- decimal_spoofing-SPEED.csv
	- decimal_spoofing-STEERING_WHEEL.csv

- **IoT-23:** `IoT-23/iot23_combined_new.csv`

The datasets are not included in this repository. Obtain them from their original sources or authorized repositories and comply with their respective terms of use.

## 3. Repository Structure

The  notebooks are organized into two experimental groups.

### 3.1 Holdout experiments

| Notebook | Dataset | Classification |
|---|---|---|
| `Binary_BoT-IoT-BSLA-ANN.ipynb` | BoT-IoT | Binary |
| `Multi_Bot_IoT_BSLA-ANN.ipynb` | BoT-IoT | Multiclass |
| `Binary_CICIoV2024_BSLA-ANN.ipynb` | CICIoV2024 | Binary |
| `Multi_CICIoV2024_BSLA-ANN.ipynb` | CICIoV2024 | Multiclass |
| `Binary_IoT23_BSLA-ANN.ipynb` | IoT-23 | Binary |
| `Multi_IoT23_BSLA-ANN.ipynb` | IoT-23 | Multiclass |

### 3.2 Five-fold cross-validation experiments

| Notebook | Dataset | Classification |
|---|---|---|
| `Binary-BoT-IoT_SBLA-ANN_5cv-Final.ipynb` | BoT-IoT | Binary |
| `Multi_Bot_IoT_SBLA-ANN_5cv.ipynb` | BoT-IoT | Multiclass |
| `Binary-CICoV2024_SBLA-ANN_5cv.ipynb` | CICIoV2024 | Binary |
| `Multi_CICIoV2024_SBLA-ANN_5cv.ipynb` | CICIoV2024 | Multiclass |
| `Binary-IoT23_SBLA-ANN_5cv.ipynb` | IoT-23 | Binary |
| `Multi_IoT23_SBLA-ANN_5cv.ipynb` | IoT-23 | Multiclass |

## 4. Methodology

The experimental workflow consists of data cleaning, preprocessing, class balancing, sequential feature representation, model training, and evaluation.

### 4.1 Data cleaning and preprocessing

The research-methodology uses the following preprocessing steps:

1. **Missing-value treatment:** Missing numerical values are replaced using median imputation.
2. **Removal of non-informative attributes:** Dataset-specific identifiers, metadata, duplicate information, redundant columns, and target-related fields are removed where appropriate to reduce noise and prevent data leakage.
3. **Categorical encoding:** Categorical attributes are converted to numerical representations where required. Target labels are converted to integer class indices; `LabelEncoder` is used in the relevant multiclass processing.
4. **Dataset-specific label processing:** For example, IoT-23 labels are standardized by removing the `PartOfA` prefix where applicable and stripping leading/trailing whitespace. Classes with fewer than 50 records are excluded from the IoT-23 multiclass experiment.

The precise columns and transformations are dataset-specific. Refer to the corresponding notebook for the executable preprocessing code.

### 4.2 Data splitting

**Holdout evaluation**

The holdout notebooks use stratified sampling with `random_state=42`:

- 70% training data.
- 15% validation data.
- 15% test data.

The initial 30% temporary partition is divided equally between validation and testing, with stratification preserved.

**Five-fold cross-validation**

The five-fold notebooks use `StratifiedKFold` with five splits, shuffling enabled, and `random_state=42`. In each iteration:

- Four folds (80% of the data) form the training portion.
- The remaining fold (20%) is split equally into validation and test partitions.
- This gives approximately 80% training, 10% validation, and 10% test data per iteration.
- The fold assignment rotates so each fold serves as the held-out portion once.

This describes the implementation in the five-fold notebooks: the held-out fold is split into validation and test subsets rather than being used simultaneously as two separate folds.

### 4.3 Feature scaling

`StandardScaler` is used for Z-score standardization. It is fitted using only the training portion and then applied to the corresponding validation and test portions. This prevents information from the held-out data from influencing the fitted scaling parameters.

### 4.4 Class imbalance handling with SMOTE

The Synthetic Minority Over-sampling Technique (SMOTE) is applied only to the training data. Validation and test partitions retain their original class distributions and are not oversampled.

The notebooks use `SMOTE` with `random_state=16` and a dataset-/task-specific `sampling_strategy`. The accompanying methodology describes the target resampling value as an intermediate target below the majority-class size. Different values are selected for the datasets and for binary versus multiclass settings because their class distributions differ.

The actual `sampling_strategy` values are defined in the individual notebooks and should be reported from those configurations rather than assumed to be identical across experiments.

### 4.5 Sequential feature representation

After scaling and training-set resampling, each feature matrix is reshaped from a two-dimensional representation `(samples, F)` into a three-dimensional representation `(samples, F, 1)`:

```python
X_reshaped = X_scaled.reshape(
    (X_scaled.shape[0], X_scaled.shape[1], 1)
)
```

Here, `F` is the number of input features. Each feature dimension is treated as an ordered sequence position with one value at that position. These positions represent feature dimensions, not chronologically ordered network observations. Accordingly, the BiLSTM acts as a contextual feature encoder across the ordered feature representation; it should not be described as learning temporal dependencies between network events from this reshaping alone.

### 4.6 SBLA-ANN architecture

The supplied notebooks implement the following architecture:

1. **Input:** Receives the reshaped feature representation.
2. **Bidirectional LSTM:** Processes the sequence in forward and backward directions and returns sequence representations.
3. **Self-attention:** Keras `Attention` is applied to the BiLSTM output using the same tensor as query and value:

   ```python
   x = Attention()([x, x])
   ```

4. **Flatten:** Converts the attention output into a one-dimensional representation.
5. **Dense layers:** Fully connected layers use ReLU activation.
6. **Batch normalization:** Applied within the dense network.
7. **Output:** A softmax layer produces class probabilities for binary or multiclass classification.

The architecture's units, dense-layer sizes, and L2 regularization settings are configured in the individual notebooks.

### 4.7 Model training and callbacks

The supplied notebooks use the Adam optimizer and sparse categorical cross-entropy loss. The common training configuration includes a batch size of 256 and a maximum of 100 epochs. Training uses validation data and callbacks including:

- `EarlyStopping`, monitoring `val_loss`, with patience 10 and restoration of the best weights.
- `ReduceLROnPlateau`, monitoring `val_loss`, with factor 0.5, patience 5, and minimum learning rate `1e-6`.
- `ModelCheckpoint`, saving the best model as `best_model.keras` based on validation loss.

Check each notebook for the exact model parameters and any experiment-specific changes.

### 4.8 Hyperparameter selection

A Grid Search strategy is used that considers BiLSTM units, dense-layer neuron configurations, batch size, and L2 regularization strengths. Candidate configurations are compared using validation performance, and the selected configuration is intended for subsequent experiments.


### 4.9 Performance evaluation

The notebooks evaluate classification performance using metrics and visualizations that include:

- Accuracy, precision, recall, and F1-score.
- False Positive Rate (FPR).
- Area Under the ROC Curve (ROC-AUC).
- Confusion matrices and normalized confusion matrices.
- ROC curves.
- Training and validation accuracy/loss curves.

The relevant notebooks also include model-complexity and resource-usage analyses. Consult each notebook for the exact measurements and outputs produced.

## 5. Requirements and Dependencies

The notebooks use Python and the following libraries:

- NumPy
- Pandas
- Scikit-learn
- TensorFlow
- Keras
- imbalanced-learn
- SciPy
- Matplotlib
- Seaborn
- psutil
- IPython

Some notebook cells also use standard Python modules, including:

- `os`
- `time`
- `itertools`
- `subprocess`
- `collections`

The notebooks use GPU monitoring through `nvidia-smi` in relevant cells. NVIDIA GPU monitoring requires a compatible NVIDIA driver and an available `nvidia-smi` executable. CPU execution may be possible, although training time can increase substantially.

### Installation

Create and activate a Python environment, then install the required packages:

```bash
pip install numpy pandas scikit-learn tensorflow keras imbalanced-learn scipy matplotlib seaborn psutil ipython jupyter
```

## 6. Usage Instructions

### Step 1: Obtain the repository

Clone the repository or download the project files.

### Step 2: Install dependencies

Install the required Python libraries using the installation command above.

### Step 3: Obtain the datasets

Download the three datasets from their respective sources. Place the data files in the directories expected by the notebooks, or update the dataset paths in the relevant notebook.

### Step 4: Open Jupyter Notebook

Start Jupyter:

```bash
jupyter notebook
```

Alternatively, open the notebooks using JupyterLab or Google Colab.

### Step 5: Select an experiment

Choose the notebook corresponding to the required dataset, classification task, and evaluation strategy.

For example:

- Binary classification on BoT-IoT using holdout evaluation: `Binary_BoT-IoT-SBLA-ANN.ipynb`
- Multiclass classification on CICIoV2024 using five-fold cross-validation: `Multi_CICIoV2024_SBLA-ANN_5cv.ipynb`

### Step 6: Configure dataset paths

Check the dataset path variables and update them according to the local directory structure.

### Step 7: Execute the notebook

Run the notebook cells in order. The notebook performs data preparation, model construction, training, and evaluation.

### Step 8: Examine the results

Review the generated metric tables, confusion matrices, ROC curves, training curves, and resource-usage summaries.

## 7. Experimental Reproducibility

The notebooks specify random seeds for data splitting, cross-validation, and SMOTE. In particular, the common settings include:

- Data splitting random state: `42`
- Five-fold cross-validation random state: `42`
- SMOTE random state: `16`

Exact reproducibility can also depend on the software environment, TensorFlow version, hardware, GPU operations, and dataset versions. For research comparisons, retain the original datasets, notebook configurations, and generated results.


## 8. License

MIT License.

## 9. Contact

For questions about the implementation or research, contact:

- **Project/Researcher:** [Sadaf ALRaisi]
- **Email:** [sadafalraisi20@gmail.com]
- **Project Link:** [(https://github.com/sadafalraisi/SBLA-ANN-MODEL-.git)]
