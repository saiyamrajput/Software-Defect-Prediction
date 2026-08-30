# Software Defect Prediction

## Project Overview

This project investigates how effectively static software metrics can be used to predict whether a software module is defective. I use the KC1, CM1, and PC1 NASA PROMISE datasets, which contain software metrics and corresponding defect labels.

The experiments compare a majority-class baseline using DummyClassifier with Logistic Regression and Random Forest. I evaluate the models using a consistent setup that includes an 80/20 train-test split, multiple evaluation metrics, 5-fold stratified cross-validation, and a class-weighting experiment.

The main goal is to study how different machine-learning models perform for software defect prediction when the datasets are imbalanced.

## Research Question

How effectively can software metrics in the KC1, CM1, and PC1 datasets predict whether a software module is defective?

## Hypotheses

### Hypothesis 1

Machine-learning classifiers using KC1, CM1, and PC1 software metrics will identify defective software modules better than a majority-class baseline.

### Hypothesis 2

Logistic Regression and Random Forest will show different defect-prediction performance when evaluated using the same evaluation setup.

### Hypothesis 3

Metrics that focus on the defective class will reveal differences in model performance that accuracy alone may hide.

## Datasets

This study uses three NASA PROMISE software-defect datasets: KC1, CM1, and PC1. Each dataset contains 21 numeric static software metrics and one binary target variable, `defects`, indicating whether a software module is defective.

### KC1

* **Provenance:** Part of the NASA Metrics Data Program. The dataset contains static code measurements and defect labels from a software system used for storage management and the processing of ground data. The system was written in C++.
* **Target:** `defects` — `true` for defective modules and `false` otherwise.
* **Size:** 2,109 software modules and 22 columns.
* **Class distribution:** 1,783 non-defective modules (84.54%) and 326 defective modules (15.46%).
* **Missing values:** None.

### CM1

* **Provenance:** Part of the NASA Metrics Data Program. The dataset contains software-engineering measurements from a NASA spacecraft instrument written in C.
* **Target:** `defects` — `true` for defective modules and `false` otherwise.
* **Size:** 498 software modules and 22 columns.
* **Class distribution:** 449 non-defective modules (90.16%) and 49 defective modules (9.84%).
* **Missing values:** None.

### PC1

* **Provenance:** Part of the NASA Metrics Data Program. The dataset contains static source-code measurements and defect labels from flight software for an Earth-orbiting satellite written in C.
* **Target:** `defects` — `true` for defective modules and `false` otherwise.
* **Size:** 1,109 software modules and 22 columns.
* **Class distribution:** 1,032 non-defective modules (93.06%) and 77 defective modules (6.94%).
* **Missing values:** None.

### Dataset Comparison

| Attribute             |               KC1 |               CM1 |               PC1 |
| --------------------- | ----------------: | ----------------: | ----------------: |
| Language              |               C++ |                 C |                 C |
| Modules               |             2,109 |               498 |             1,109 |
| Features              |                21 |                21 |                21 |
| Defective modules     |               326 |                49 |                77 |
| Non-defective modules |             1,783 |               449 |             1,032 |
| Defective rate        |            15.46% |             9.84% |             6.94% |
| Non-defective rate    |            84.54% |            90.16% |            93.06% |
| Missing values        |                 0 |                 0 |                 0 |
| Feature distribution  | Positively skewed | Positively skewed | Positively skewed |

KC1 is the largest and least imbalanced of the three datasets, while PC1 has the smallest proportion of defective modules. Using all three datasets allows the models to be evaluated under different levels of class imbalance instead of relying on results from only one dataset.

### Dataset Availability

The datasets are publicly distributed through NASA/PROMISE software-defect dataset sources. The ARFF files used for the experiments are kept locally and are not included in this repository.

No explicit software license is stated in the ARFF metadata used for this project.

## Methodology

### Data Preparation

The 21 software metrics in each dataset are used as features, while `defects` is used as the target variable.

For the initial experiments, each dataset is divided into 80% training data and 20% test data using a stratified split. Stratification keeps the defective and non-defective class proportions similar in both subsets. A fixed random state is used to make the experiments reproducible.

No missing-value imputation is required because the three datasets contain no missing values. The predictor variables are numeric, so categorical encoding is also unnecessary.

Several features are positively skewed, but they are not automatically transformed only because of their skewness.

Logistic Regression is used with StandardScaler in a pipeline. The scaling parameters are learned from the training data so that information from the test set is not used during training.

### Models

#### DummyClassifier

A majority-class DummyClassifier is used as the baseline. It shows what happens if the model simply predicts the most common class.

This is especially important for these imbalanced datasets because a model can achieve high accuracy while detecting no defective modules.

#### Logistic Regression

Logistic Regression provides a relatively simple classification model for estimating the relationship between the software metrics and the probability that a module is defective.

#### Random Forest

Random Forest combines predictions from multiple decision trees and can capture more complex and non-linear relationships between the software metrics and defect outcomes.

Logistic Regression and Random Forest are compared because they learn patterns differently while being evaluated using the same experimental setup.

### Evaluation Metrics

Because the datasets are imbalanced, accuracy alone is not sufficient for evaluating defect-prediction performance.

#### Recall

Recall measures how many of the actually defective modules are correctly identified. Low recall means that many defective modules are being missed.

#### Precision

Precision measures how many modules predicted as defective are actually defective. It helps show whether improving recall is also producing many false positives.

#### F1-score

F1-score combines precision and recall into one metric. It is useful when both missed defects and false alarms matter.

#### ROC-AUC

ROC-AUC measures how well the model's predicted scores distinguish between defective and non-defective modules across different classification thresholds.

### Cross-Validation

I use 5-fold stratified cross-validation on the training data to compare DummyClassifier, Logistic Regression, and Random Forest across multiple validation folds instead of relying only on one train-test split.

Stratification keeps the defective and non-defective proportions similar across the folds.

### Class-Weighting Experiment

Because defective modules are the minority class in all three datasets, I also evaluate Random Forest using `class_weight="balanced"`.

The purpose of this experiment is to give more importance to the defective class during training and study whether this improves recall and reduces false negatives. The results are compared with the original Random Forest using the same test set.

## Results

### KC1

#### Test Set Results

| Model               | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| ------------------- | -------: | --------: | -----: | -------: | ------: |
| DummyClassifier     |    0.846 |     0.000 |  0.000 |    0.000 |       — |
| Logistic Regression |    0.860 |     0.636 |  0.215 |    0.322 |   0.804 |
| Random Forest       |    0.860 |     0.568 |  0.385 |    0.459 |   0.835 |

The DummyClassifier achieved about 84.6% accuracy but detected none of the 65 defective modules.

Logistic Regression and Random Forest both achieved about 86.0% accuracy, but Random Forest detected 25 defective modules compared with 14 for Logistic Regression. Random Forest therefore had higher recall, F1-score, and ROC-AUC, while Logistic Regression had higher precision.

This shows that similar accuracy values can hide important differences in defect-detection performance.

#### 5-Fold Stratified Cross-Validation

| Model               |      Accuracy |     Precision |        Recall |      F1-score |       ROC-AUC |
| ------------------- | ------------: | ------------: | ------------: | ------------: | ------------: |
| DummyClassifier     | 0.845 ± 0.001 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.500 ± 0.000 |
| Logistic Regression | 0.857 ± 0.009 | 0.616 ± 0.083 | 0.199 ± 0.038 | 0.300 ± 0.049 | 0.810 ± 0.026 |
| Random Forest       | 0.860 ± 0.005 | 0.600 ± 0.042 | 0.291 ± 0.029 | 0.390 ± 0.019 | 0.817 ± 0.012 |

Random Forest had higher mean recall, F1-score, and ROC-AUC than Logistic Regression, while Logistic Regression had slightly higher precision. However, Random Forest's mean recall was still only 0.291, so many defective modules were still missed.

#### Class-Weighting Experiment

| Random Forest Setting | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| --------------------- | -------: | --------: | -----: | -------: | ------: |
| Original              |    0.860 |     0.568 |  0.385 |    0.459 |   0.835 |
| Balanced              |    0.822 |     0.442 |  0.585 |    0.503 |   0.827 |

Class weighting increased the number of detected defective modules from 25 to 38 out of 65 and reduced false negatives from 40 to 27.

Recall increased from 0.385 to 0.585 and F1-score increased from 0.459 to 0.503. However, false positives increased from 19 to 48, which reduced precision and accuracy.

On KC1, class weighting improved defect detection but produced more false alarms.

### CM1

#### Test Set Results

| Model               | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| ------------------- | -------: | --------: | -----: | -------: | ------: |
| DummyClassifier     |    0.900 |     0.000 |  0.000 |    0.000 |       — |
| Logistic Regression |    0.900 |     0.500 |  0.100 |    0.167 |   0.637 |
| Random Forest       |    0.890 |     0.333 |  0.100 |    0.154 |   0.624 |

The DummyClassifier achieved 90.0% accuracy while detecting none of the 10 defective modules.

Logistic Regression and Random Forest each detected only 1 defective module. Logistic Regression had slightly higher precision, F1-score, and ROC-AUC than Random Forest.

Despite accuracy values around 89–90%, both models missed 9 of the 10 defective modules.

#### 5-Fold Stratified Cross-Validation

| Model               |      Accuracy |     Precision |        Recall |      F1-score |       ROC-AUC |
| ------------------- | ------------: | ------------: | ------------: | ------------: | ------------: |
| DummyClassifier     | 0.902 ± 0.005 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.500 ± 0.000 |
| Logistic Regression | 0.897 ± 0.012 | 0.313 ± 0.270 | 0.129 ± 0.112 | 0.179 ± 0.153 | 0.803 ± 0.052 |
| Random Forest       | 0.882 ± 0.009 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.790 ± 0.099 |

Logistic Regression provided stronger defect-classification performance than Random Forest under this cross-validation setup.

Random Forest had zero mean recall and F1-score at the current classification threshold. However, its ROC-AUC of 0.790 shows that its predicted probabilities still contained some ability to distinguish between the two classes.

#### Class-Weighting Experiment

| Random Forest Setting | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| --------------------- | -------: | --------: | -----: | -------: | ------: |
| Original              |    0.890 |     0.333 |  0.100 |    0.154 |   0.624 |
| Balanced              |    0.830 |     0.231 |  0.300 |    0.261 |   0.638 |

Class weighting increased the number of detected defective modules from 1 to 3 out of 10 and reduced false negatives from 9 to 7.

Recall increased from 0.100 to 0.300 and F1-score increased from 0.154 to 0.261. However, false positives increased from 2 to 10, while precision and accuracy decreased.

On CM1, class weighting improved recall but also produced more false alarms.

### PC1

#### Test Set Results

| Model               | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| ------------------- | -------: | --------: | -----: | -------: | ------: |
| DummyClassifier     |    0.932 |     0.000 |  0.000 |    0.000 |       — |
| Logistic Regression |    0.928 |     0.333 |  0.067 |    0.111 |   0.865 |
| Random Forest       |    0.941 |     0.625 |  0.333 |    0.435 |   0.875 |

The DummyClassifier achieved about 93.2% accuracy but detected none of the 15 defective modules.

Logistic Regression detected 1 defective module, while Random Forest detected 5. Random Forest had higher precision, recall, F1-score, accuracy, and ROC-AUC.

However, Random Forest still missed 10 of the 15 defective modules.

#### 5-Fold Stratified Cross-Validation

| Model               |      Accuracy |     Precision |        Recall |      F1-score |       ROC-AUC |
| ------------------- | ------------: | ------------: | ------------: | ------------: | ------------: |
| DummyClassifier     | 0.930 ± 0.003 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.500 ± 0.000 |
| Logistic Regression | 0.926 ± 0.008 | 0.186 ± 0.229 | 0.112 ± 0.138 | 0.139 ± 0.172 | 0.841 ± 0.019 |
| Random Forest       | 0.931 ± 0.015 | 0.530 ± 0.223 | 0.238 ± 0.105 | 0.327 ± 0.142 | 0.843 ± 0.024 |

Random Forest had higher precision, recall, F1-score, and slightly higher ROC-AUC than Logistic Regression.

Its mean recall was still only 0.238, showing that many defective modules remained undetected.

#### Class-Weighting Experiment

| Random Forest Setting | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| --------------------- | -------: | --------: | -----: | -------: | ------: |
| Original              |    0.941 |     0.625 |  0.333 |    0.435 |   0.875 |
| Balanced              |    0.946 |     0.615 |  0.533 |    0.571 |   0.896 |

Class weighting increased the number of detected defective modules from 5 to 8 out of 15 and reduced false negatives from 10 to 7.

Recall increased from 0.333 to 0.533 and F1-score increased from 0.435 to 0.571. Precision decreased only slightly, while accuracy and ROC-AUC also improved slightly.

Among the three datasets, PC1 showed the strongest overall improvement from the balanced Random Forest experiment.

## Initial Findings

The experiments showed that accuracy alone is not a reliable measure of defect-prediction performance on these imbalanced datasets. The DummyClassifier achieved high accuracy while detecting no defective modules.

Random Forest generally performed better than Logistic Regression for detecting defective modules, especially on KC1 and PC1. However, the results were not consistent across every dataset, and Logistic Regression performed better under the CM1 cross-validation experiment.

Class weighting generally increased recall and reduced false negatives. The tradeoff was that it could also increase false positives and reduce precision or accuracy.

Overall, there was no single model or configuration that was best across every dataset and metric. The results show why software defect prediction should be evaluated using multiple metrics and across multiple datasets.

## Limitations

1. **Limited number of datasets:** Only KC1, CM1, and PC1 were evaluated, so the results may not generalize to software projects with different characteristics.

2. **Limited model comparison:** The study mainly compares Logistic Regression and Random Forest against a majority-class baseline. Other models may produce different results.

3. **Limited hyperparameter tuning:** The models were evaluated using mostly default hyperparameter settings. Systematic tuning could change their performance.

4. **Variation across datasets:** Model behavior was not consistent across KC1, CM1, and PC1, which makes it difficult to identify one model as the best general choice for software defect prediction.

## Repository Structure

```text
Software-Defect-Prediction/
|
|-- notebooks/
|   |-- KC1_initial_eda.ipynb
|   |-- CM1_initial_eda.ipynb
|   |-- PC1_initial_eda.ipynb
|
|-- data/
|   |-- raw/                       # local dataset directory (gitignored)
|       |-- kc1.arff
|       |-- cm1.arff
|       |-- pc1.arff
|
|-- .gitignore
|-- README.md
|-- requirements.txt
```

The three Jupyter notebooks contain the data exploration, preprocessing, model evaluation, cross-validation, and class-weighting experiments for KC1, CM1, and PC1.

The `data/raw/` directory is required locally to run the notebooks but is excluded from the repository through `.gitignore`.

## How to Reproduce

1. Clone the repository.

2. Install the required Python dependencies:

```bash
pip install -r requirements.txt
```

3. Create the following local directory in the project root:

```text
data/raw/
```

4. Obtain the KC1, CM1, and PC1 ARFF datasets from the NASA/PROMISE dataset source used for this project and place them inside `data/raw/`:

```text
data/
|-- raw/
    |-- kc1.arff
    |-- cm1.arff
    |-- pc1.arff
```

The dataset files are not included in this repository.

5. Launch Jupyter Notebook or open the project in an environment that supports Jupyter notebooks.

6. Run the notebooks in `notebooks/`:

```text
KC1_initial_eda.ipynb
CM1_initial_eda.ipynb
PC1_initial_eda.ipynb
```

Each notebook contains the preprocessing, baseline/model evaluation, cross-validation, and class-weighting experiment for its corresponding dataset.

## Next Experiments

Future work can extend the current study in several directions:

* Compare additional classification models.
* Perform systematic hyperparameter tuning.
* Compare class weighting with other imbalance-handling techniques such as oversampling or undersampling.
* Investigate classification-threshold adjustment and its effect on precision and recall.
* Evaluate the models on additional software-defect datasets.
* Examine whether the differences between models are statistically meaningful across repeated evaluations.
