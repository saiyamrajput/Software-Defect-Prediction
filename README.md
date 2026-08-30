# Software Defect Prediction

## Project Overview

This project investigates how effectively static software metrics can be used to predict whether a software module is defective or not. We use the KC1, CM1, and PC1 NASA PROMISE datasets, which contain software metrics and corresponding defect labels. The experiments compare a majority-class baseline (DummyClassifier), Logistic Regression, and Random Forest using a consistent evaluation approach, including cross-validation and class-weighting experiments. The overall purpose is to evaluate how different machine-learning models perform for software defect prediction under class imbalance.

## Research Question

How effectively can software metrics in the KC1, CM1, and PC1 datasets predict whether a software module is defective?

## Hypotheses

### Hypothesis 1

Machine-learning classifiers using KC1, CM1, and PC1 software metrics will identify defective software modules better than a majority-class baseline.

### Hypothesis 2

Logistic Regression and Random Forest will exhibit different defect-prediction performance when evaluated using the same evaluation protocol.

### Hypothesis 3

Evaluation metrics that focus on the defective class will reveal differences in classifier performance that accuracy alone may obscure.

## Datasets

This study uses three NASA PROMISE software-defect datasets: KC1, CM1, and PC1. Each dataset contains 21 numeric static software metrics and one binary target variable, `defects`, indicating whether a software module is defective.

### KC1

* **Provenance:** Part of the NASA Metrics Data Program. The dataset contains static code measurements and defect labels from a software system used in storage management for receiving and processing ground data and written in C++.
* **Target:** `defects` - true for defective modules and false otherwise.
* **Size:** 2,109 software modules and 22 columns.
* **Class distribution:** 1,783 non-defective modules (84.54%) and 326 defective modules (15.46%).
* **Missing values:** None.
* **Licensing:** Publicly distributed and commonly used in software-defect prediction research. Citation or acknowledgment is expected, while no explicit software license is stated in the supplied ARFF metadata.

### CM1

* **Provenance:** Part of the NASA Metrics Data Program. The dataset contains software-engineering measurements collected from a NASA spacecraft instrument written in C.
* **Target:** `defects` - true for defective modules and false otherwise.
* **Size:** 498 software modules and 22 columns.
* **Class distribution:** 449 non-defective modules (90.16%) and 49 defective modules (9.84%).
* **Missing values:** None.
* **Licensing:** Publicly distributed and commonly used in software-defect prediction research. Citation or acknowledgment is expected, while no explicit software license is stated in the supplied ARFF metadata.

### PC1

* **Provenance:** Part of the NASA Metrics Data Program. The dataset contains static source-code measurements and defect labels from flight software for an Earth-orbiting satellite written in C.
* **Target:** `defects` - true for defective modules and false otherwise.
* **Size:** 1,109 software modules and 22 columns.
* **Class distribution:** 1,032 non-defective modules (93.06%) and 77 defective modules (6.94%).
* **Missing values:** None.
* **Licensing:** Publicly distributed and commonly used in software-defect prediction research. Citation or acknowledgment is expected, while no explicit software license is stated in the supplied ARFF metadata.

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

KC1 is the largest and least imbalanced of the three datasets, while PC1 has the smallest proportion of defective modules. Using all three datasets allows the models to be evaluated under different levels of class imbalance rather than relying on results from a single dataset.

## Methodology

### Data Preparation

The first 21 columns of each dataset are used as software-metric features, while `defects` is used as the target variable.

For the initial model experiments, each dataset is divided into 80% training data and 20% test data using stratified splitting. Stratification preserves the original defective/non-defective class distribution in both subsets. A fixed random state is used to make the experiments reproducible.

No missing-value imputation is required because KC1, CM1, and PC1 contain no missing values. Categorical encoding is also unnecessary because the predictor variables are numeric software metrics.

Although several software metrics are positively skewed, they are not automatically transformed solely because of skewness.

For Logistic Regression, feature scaling is performed without leaking information from the test set. The scaler is fitted only on the training features and is then used to transform both the training and test features.

### Models

#### DummyClassifier

A majority-class DummyClassifier is used as a baseline to determine whether the machine-learning models provide meaningful predictive value beyond simply predicting the most common class. This is especially important for the imbalanced datasets, where high accuracy can be achieved while failing to identify defective modules.

#### Logistic Regression

Logistic Regression provides a relatively simple classification model that estimates the relationship between the software metrics and the probability that a module is defective.

#### Random Forest

Random Forest combines predictions from multiple decision trees and can capture more complex, non-linear relationships between software metrics and defect outcomes.

Logistic Regression and Random Forest were compared because they learn patterns in different ways. Logistic Regression models a relatively simple linear relationship between the software metrics and the probability of a defect, while Random Forest combines predictions from multiple decision trees and can capture more complex, non-linear relationships between the features and defect outcomes.

### Evaluation Metrics

Since the datasets are imbalanced, accuracy alone is not sufficient for evaluating defect-prediction score. A model may achieve high accuracy by predicting mostly non-defective modules while still failing to detect actual defects.

#### Recall

Recall is particularly important for software defect prediction because it measures how many of the actually defective modules were correctly identified by the model. A low recall means that many defective modules were missed, even if the overall accuracy appears high.

#### Precision

Precision measures how many modules predicted as defective were actually defective. It is important because increasing recall by predicting too many modules as defective can create additional false positives and reduce precision.

#### F1 score

F1-score is used to summarize the balance between precision and recall in a single metric. This is useful for imbalanced defect-prediction datasets because a model may achieve high recall with many false positives or high precision while missing many defective modules.

#### ROC-AUC

ROC-AUC measures how well a classifier can distinguish between defective and non-defective modules across different classification thresholds. A higher ROC-AUC indicates that the model is generally better at ranking defective modules above non-defective ones.

### Cross Validation

5-fold stratified cross-validation is used to evaluate the models across multiple train/test partitions instead of relying only on the results from a single 80/20 split. Stratification keeps the defective and non-defective class proportions similar in each fold, providing a more reliable estimate of model performance.

### Class-Weighting Experiment

Because the defective class is much smaller than the non-defective class in KC1, CM1 and PC1, an additional Random Forest experiment is performed using class_weight="balanced". This gives greater importance to the minority defective class during training, with the goal of improving defect detection and reducing false negatives rather than allowing the model to favor the majority non-defective class.

## Results

### KC1

#### Test Set Results

The initial models were evaluated using the same stratified 80/20 train-test split.

| Model               | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| ------------------- | -------: | --------: | -----: | -------: | ------: |
| DummyClassifier     |    0.846 |     0.000 |  0.000 |    0.000 |       — |
| Logistic Regression |    0.860 |     0.636 |  0.215 |    0.322 |   0.804 |
| Random Forest       |    0.860 |     0.568 |  0.385 |    0.459 |   0.835 |

Although the DummyClassifier achieved approximately 84.6% accuracy, it identified none of the 65 defective modules in the test set. Logistic Regression and Random Forest both achieved approximately 86.0% accuracy but their defect-detection performance is different. Random Forest detected 25 of the 65 defective modules compared with 14 for Logistic Regression, resulting in higher recall, F1-score and ROC-AUC. Logistic Regression achieved higher precision.

These results demonstrate that accuracy alone may obscure.

#### 5-Fold Stratified Cross-Validation

To reduce reliance on a single train/test split, DummyClassifier, Logistic Regression, and Random Forest were also compared using 5-fold stratified cross-validation on the training data.

| Model               |      Accuracy |     Precision |        Recall |      F1-score |       ROC-AUC |
| ------------------- | ------------: | ------------: | ------------: | ------------: | ------------: |
| DummyClassifier     | 0.845 ± 0.001 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.500 ± 0.000 |
| Logistic Regression | 0.857 ± 0.009 | 0.616 ± 0.083 | 0.199 ± 0.038 | 0.300 ± 0.049 | 0.810 ± 0.026 |
| Random Forest       | 0.860 ± 0.005 | 0.600 ± 0.042 | 0.291 ± 0.029 | 0.390 ± 0.019 | 0.817 ± 0.012 |

#### Class-Weighting Experiment

Random Forest was then evaluated using `class_weight="balanced"` on the same KC1 test set.

| Random Forest         | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| --------------------- | -------: | --------: | -----: | -------: | ------: |
| Original              |    0.860 |     0.568 |  0.385 |    0.459 |   0.835 |
| Balanced              |    0.822 |     0.442 |  0.585 |    0.503 |   0.827 |

Class weighting increased the number of correctly detected defective modules from 25 to 38 out of 65 and reduced false negatives from 40 to 27. Recall increased from 0.385 to 0.585 and F1-score increased from 0.459 to 0.503.

However, false positives increased from 19 to 48, causing precision to decrease from 0.568 to 0.442 and accuracy to decrease from 0.860 to 0.822. ROC-AUC also decreased slightly. Therefore, class weighting improved defect detection on KC1 but introduced more false alarms demonstrating the tradeoff between recall and precision.

### CM1

#### Test Set Results

The initial models were evaluated using the same stratified 80/20 train-test split.

| Model               | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| ------------------- | -------: | --------: | -----: | -------: | ------: |
| DummyClassifier     |    0.900 |     0.000 |  0.000 |    0.000 |       — |
| Logistic Regression |    0.900 |     0.500 |  0.100 |    0.167 |   0.637 |
| Random Forest       |    0.890 |     0.333 |  0.100 |    0.154 |   0.624 |

Although the DummyClassifier achieved 90.0% accuracy, it identified none of the 10 defective modules in the test set. Logistic Regression and Random Forest each detected only 1 of the 10 defective modules, resulting in a recall of 0.100 for both models. Logistic Regression achieved slightly higher accuracy, precision, F1-score, and ROC-AUC than Random Forest.

These results again show that high accuracy can obscure poor defect-detection performance on an imbalanced dataset. Despite achieving approximately 89–90% accuracy, both machine-learning models missed 9 of the 10 defective modules.

#### 5-Fold Stratified Cross-Validation

To reduce reliance on a single train-test split, DummyClassifier, Logistic Regression, and Random Forest were also compared using 5-fold stratified cross-validation on the training data.

| Model               |      Accuracy |     Precision |        Recall |      F1-score |       ROC-AUC |
| ------------------- | ------------: | ------------: | ------------: | ------------: | ------------: |
| DummyClassifier     | 0.902 ± 0.005 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.500 ± 0.000 |
| Logistic Regression | 0.897 ± 0.012 | 0.313 ± 0.270 | 0.129 ± 0.112 | 0.179 ± 0.153 | 0.803 ± 0.052 |
| Random Forest       | 0.882 ± 0.009 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.790 ± 0.099 |

The cross-validation results differed from those observed for KC1. Logistic Regression achieved non-zero precision, recall, and F1-score and therefore provided stronger defect-classification performance than Random Forest under this evaluation.

Random Forest produced zero mean recall and F1-score at the current classification threshold, meaning that it did not correctly classify defective modules during the validation folds. However, its mean ROC-AUC of 0.790 indicates that its predicted probabilities still contained some ability to distinguish between defective and non-defective modules.

#### Class-Weighting Experiment

Random Forest was then evaluated using `class_weight="balanced"` on the same CM1 test set.

| Random Forest Setting | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| --------------------- | -------: | --------: | -----: | -------: | ------: |
| Original              |    0.890 |     0.333 |  0.100 |    0.154 |   0.624 |
| Balanced              |    0.830 |     0.231 |  0.300 |    0.261 |   0.638 |

Class weighting increased the number of correctly detected defective modules from 1 to 3 out of 10 and reduced false negatives from 9 to 7. Recall increased from 0.100 to 0.300 and F1-score increased from 0.154 to 0.261.

However, false positives increased from 2 to 10, causing precision to decrease from 0.333 to 0.231 and accuracy to decrease from 0.890 to 0.830. ROC-AUC increased slightly from 0.624 to 0.638. Therefore, class weighting improved defect detection on CM1 but introduced more false alarms, again demonstrating the tradeoff between recall and precision.

### PC1

#### Test Set Results

The initial models were evaluated using the same stratified 80/20 train-test split.

| Model               | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| ------------------- | -------: | --------: | -----: | -------: | ------: |
| DummyClassifier     |    0.932 |     0.000 |  0.000 |    0.000 |       — |
| Logistic Regression |    0.928 |     0.333 |  0.067 |    0.111 |   0.865 |
| Random Forest       |    0.941 |     0.625 |  0.333 |    0.435 |   0.875 |

Although the DummyClassifier achieved approximately 93.2% accuracy, it identified none of the 15 defective modules in the test set. Logistic Regression detected only 1 of the 15 defective modules, while Random Forest detected 5.

Random Forest achieved higher accuracy, precision, recall, F1-score, and ROC-AUC than Logistic Regression. Its recall increased from 0.067 for Logistic Regression to 0.333, showing substantially better defect detection under this train-test split.

However, Random Forest still missed 10 of the 15 defective modules. Therefore, despite outperforming Logistic Regression, its high accuracy did not mean that most defective modules were successfully detected.

#### 5-Fold Stratified Cross-Validation

To reduce reliance on a single train-test split, DummyClassifier, Logistic Regression, and Random Forest were also compared using 5-fold stratified cross-validation on the training data.

| Model               |      Accuracy |     Precision |        Recall |      F1-score |       ROC-AUC |
| ------------------- | ------------: | ------------: | ------------: | ------------: | ------------: |
| DummyClassifier     | 0.930 ± 0.003 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.000 ± 0.000 | 0.500 ± 0.000 |
| Logistic Regression | 0.926 ± 0.008 | 0.186 ± 0.229 | 0.112 ± 0.138 | 0.139 ± 0.172 | 0.841 ± 0.019 |
| Random Forest       | 0.931 ± 0.015 | 0.530 ± 0.223 | 0.238 ± 0.105 | 0.327 ± 0.142 | 0.843 ± 0.024 |

The cross-validation results support the same general pattern observed on the PC1 test set. Random Forest achieved higher precision, recall, F1-score, and slightly higher ROC-AUC than Logistic Regression.

However, Random Forest's mean recall was only 0.238, meaning that it still failed to identify many defective modules across the validation folds. As with KC1 and CM1, the DummyClassifier's high accuracy combined with zero recall further demonstrates why accuracy alone is not sufficient for evaluating defect prediction on an imbalanced dataset.

#### Class-Weighting Experiment

Random Forest was then evaluated using `class_weight="balanced"` on the same PC1 test set.

| Random Forest Setting | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| --------------------- | -------: | --------: | -----: | -------: | ------: |
| Original              |    0.941 |     0.625 |  0.333 |    0.435 |   0.875 |
| Balanced              |    0.946 |     0.615 |  0.533 |    0.571 |   0.896 |

Class weighting increased the number of correctly detected defective modules from 5 to 8 out of 15 and reduced false negatives from 10 to 7. Recall increased from 0.333 to 0.533 and F1-score increased from 0.435 to 0.571.

Precision decreased only slightly from 0.625 to 0.615 as false positives increased from 3 to 5. Unlike the KC1 and CM1 experiments, accuracy also increased slightly from 0.941 to 0.946, while ROC-AUC increased from 0.875 to 0.896.

Therefore, class weighting produced the strongest improvement on PC1 among the three datasets: it substantially improved recall and F1-score while maintaining similar precision and slightly improving accuracy and ROC-AUC.

## Initial Findings

Accuracy alone was not a reliable measure of software defect-prediction performance on the imbalanced datasets. The DummyClassifier achieved high accuracy while failing to identify any defective modules, showing that accuracy can hide poor minority-class performance.

Random Forest generally performed better than Logistic Regression at identifying defective modules, particularly in terms of recall and F1-score, although the results varied across KC1, CM1, and PC1.

Class weighting generally improved the detection of defective modules by increasing recall and reducing false negatives. However, these improvements sometimes came at the cost of additional false positives, lower precision, or lower accuracy.

Overall, the experiments show that no single model or configuration was consistently best across all three datasets. Software defect-prediction models should therefore be evaluated using multiple metrics and across multiple datasets rather than relying on accuracy or the results from a single dataset.

## Limitations

1. **Limited number of datasets:** Only KC1, CM1, and PC1 were evaluated, so the observed results may not generalize to software projects or datasets with different characteristics.

2. **Limited model comparison:** The study mainly compared Logistic Regression and Random Forest against a majority-class baseline. Other classification approaches may produce different results, so these experiments cannot establish either model as the best general approach for software defect prediction.

3. **Limited hyperparameter tuning:** Most models were evaluated using largely default hyperparameter settings. Systematic hyperparameter optimization could change their performance and the relative differences between models.

4. **Variation across datasets:** Model behavior was not consistent across KC1, CM1, and PC1. This makes it difficult to generalize one model or configuration as the best choice for software defect prediction.

## Repository Structure

```text
Software-Defect-Prediction/
├── notebooks/
│   ├── KC1_initial_eda.ipynb
│   ├── CM1_initial_eda.ipynb
│   └── PC1_initial_eda.ipynb
├── Data/
│   ├── KC1.arff
│   ├── CM1.arff
│   └── PC1.arff
├── .gitignore
├── README.md
└── requirements.txt
```

The three Jupyter notebooks contain the data exploration, preprocessing, model evaluation, cross-validation, and class-weighting experiments for the KC1, CM1, and PC1 datasets.

* `KC1_initial_eda.ipynb` — experiments and results for KC1.
* `CM1_initial_eda.ipynb` — experiments and results for CM1.
* `PC1_initial_eda.ipynb` — experiments and results for PC1.
* `requirements.txt` — Python dependencies required to run the notebooks.
* `.gitignore` — prevents local environments, datasets, cache files, and other unnecessary files from being committed.

## How to Reproduce

1. Clone the repository.

2. Install the required Python dependencies:

```bash
pip install -r requirements.txt
```

3. Create a `Data/` directory in the project root.

4. Obtain the KC1, CM1, and PC1 ARFF datasets from the NASA PROMISE dataset source documented above and place them inside `Data/`:

```text
Data/
├── kc1.arff
├── cm1.arff
└── pc1.arff
```

The dataset files are not included in this repository.

5. Launch Jupyter Notebook or open the repository in an environment that supports Jupyter notebooks.

6. Run the notebooks in `notebooks/`:

```text
KC1_initial_eda.ipynb
CM1_initial_eda.ipynb
PC1_initial_eda.ipynb
```

Each notebook contains the preprocessing, baseline and model evaluation, cross-validation, and class-weighting experiment for its corresponding dataset.

## Next Experiments

Future work will extend the current study in several directions:

* Compare additional classification models to determine whether other approaches improve defect detection beyond Logistic Regression and Random Forest.
* Perform systematic hyperparameter tuning rather than relying mostly on default model settings.
* Compare class weighting with other imbalance-handling techniques such as oversampling or undersampling.
* Investigate classification-threshold adjustment to study the tradeoff between recall and precision.
* Evaluate the models on additional software-defect datasets to test whether the observed results generalize beyond KC1, CM1, and PC1.
* Examine whether observed differences between models are statistically meaningful across repeated evaluations.
