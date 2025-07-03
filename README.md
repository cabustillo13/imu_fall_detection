# Inertial Measurement Unit Fall Detection

## 0) Research

**Before starting any project, I always research information to better understand the problem and avoid reinventing the wheel**. I enjoy bridging the gap between scientific publications and the project at hand, as this saves implementation time, reveals what has and hasn't worked in research, allows for analysis of strategies used in papers, and enables their adaptation to our project.
<br>All of this translates into better and more agile delivery times.

- [Inertial Measurement Unit Fall Detection Dataset](https://www.frdr-dfdr.ca/repo/dataset/6998d4cd-bd13-4776-ae60-6d80221e0365)
- [Validation of accuracy of SVM-based fall detection system using real-world fall and non-fall datasets](https://pdfs.semanticscholar.org/8220/032093e94c51080dd28b2c9eb7f4b7e5c191.pdf)

## 1) Considerations

### 1) Classification Approach: __Binary__ vs. Multi-class

Based on the objectives of the "Fall Detection Challenge" and insights from the provided research paper, I have opted to frame this problem as a **binary classification task**.

**Justification:**

- Challenge Objective Alignment:
<br>The core requirement of the challenge is to **"develop a model that can detect falls."** This explicitly points towards a binary outcome: Is a fall occurring (positive class), or is it not (negative class)? **The primary goal is the detection of the critical 'Fall' event**.

- Practical Application:
<br>In real-world fall detection systems, the most crucial distinction is between a genuine fall incident (requiring immediate attention or alert) and any non-fall activity. While "Activities of Daily Living (ADLs)" and "Near Falls" represent different types of non-fall events, for the purpose of immediate detection and alerting, they both fall under the umbrella of "non-fall" scenarios.

- Reference to Scientific Literature (Aziz et al., 2017):
<br>The supplementary paper, "Validation of accuracy of SVM-based fall detection system using **real-world fall and non-fall datasets**" by Aziz et al. (2017), reinforces this approach.

**Conclusion on Class Definition:**

Therefore, my classification system defines the classes as follows:
- Positive Class (1): Falls
- Negative Class (0): ADLs (Activities of Daily Living) + Near_Falls


### 2) Model Selection Justification

After preprocessing the IMU time-series signals, I applied a **sliding window approach** and extracted statistical features such as **mean**, **variance**, and **magnitude** across sensor axes. This transformation converts the original sequential data into a **tabular structure**, which is well-suited for classical machine learning classifiers.

Based on this transformation, I selected the following models:

---

#### 1) Random Forest (RF)
- A robust, non-linear ensemble model ideal for **tabular data with mixed signal patterns**.
- Naturally handles variable interactions and is resilient to noise and feature scaling.
- Serves as a strong and interpretable baseline for classification.

---

#### 2) Support Vector Machine (SVM)
- Well-suited for high-dimensional, non-linearly separable data when using the RBF kernel.
- Particularly effective for **binary classification problems with complex decision boundaries**.
- While sensitive to **class imbalance**, it benefits from techniques like class weighting or resampling.

---

#### 3) XGBoost
- A high-performance gradient boosting algorithm **optimized for tabular data**.
- Captures non-linear relationships and variable interactions effectively.
- Especially useful in scenarios with limited data but rich engineered features.
- Offers **flexible tuning** and often delivers state-of-the-art performance in structured datasets.

**Conclusion on Model Selection**

By transforming time-series data into a structured, window-based feature matrix, I'm able to leverage **powerful tree-based models and kernel-based methods** to address the fall detection task efficiently —> without requiring deep learning architectures at this stage.

### 3) Evaluation Metrics Justification

Given the nature of the fall detection problem, where class imbalance is present (**fall events are less frequent than non-falls**), selecting appropriate evaluation metrics is critical to ensure that the model performs well on both classes —> especially the minority class (falls).

#### Primary Objective
The main goal is to **maximize the F1-score**, particularly for the fall class (`label = 1`), since it balances **precision** and **recall**:

- **Precision** answers: *Of all predicted falls, how many were actually falls?*
- **Recall** answers: *Of all actual falls, how many did we detect?*
- **F1-score** is the harmonic mean of precision and recall —> **ideal for imbalanced problems**.

---

#### Why these metrics?

| Metric      | Why it matters here                                         |
|-------------|--------------------------------------------------------------|
| **Recall**  | Missing a fall (false negative) can be dangerous or critical |
| **Precision** | We want to avoid too many false alarms                      |
| **F1-score** | Balances both recall and precision under class imbalance    |
| **ROC Curve (AUC)** | Measures the ability to distinguish between classes across thresholds —> good for comparing models objectively |
| **Accuracy** | Less informative when data is imbalanced                    |

#### Additional Consideration: Model File Size
Beyond evaluating model performance through metrics such as F1-score and ROC AUC, it's also valuable to consider the **storage footprint** of each trained model —> especially in real-world deployments where **memory or storage constraints** may apply.

To that end, trained models were saved using the `.joblib` format for efficient reuse and post-training analysis.  

**Conclusion on Evaluation Metrics**

In an **imbalanced classification problem** such as fall detection, relying solely on accuracy would be misleading. <br>**F1-score and ROC AUC provide a much more reliable picture of the model's real-world performance**. 
<br>My evaluation focuses on maximizing these metrics for the fall class to ensure robust and practical detection.
<br>**Model size can influence model selection decisions by impacting both performance and deployability.**


## 2) Final Results

### 1) Random Forest

**Hypertuning config params:**
```
Best Random Forest Params: {'n_estimators': 100, 'max_depth': 20, 'min_samples_split': 2, 'min_samples_leaf': 1}
Best F1 on Validation: 0.7175
```

**Metrics**
```
Classification Report: Random Forest
              precision    recall  f1-score   support

           0       0.84      0.83      0.84      1222
           1       0.68      0.70      0.69       626

    accuracy                           0.79      1848
   macro avg       0.76      0.76      0.76      1848
weighted avg       0.79      0.79      0.79      1848

ROC AUC: 0.8501
```

**Model File Size** 
<br>`4.06 MB`

### 2) SVM

**Hypertuning config params:**
```
Best SVM Params: {'C': 1, 'gamma': 'scale', 'kernel': 'rbf'}
Best F1 on Validation: 0.6812
```

**Metrics**
```
Classification Report: SVM
              precision    recall  f1-score   support

           0       0.85      0.66      0.74      1222
           1       0.54      0.78      0.64       626

    accuracy                           0.70      1848
   macro avg       0.69      0.72      0.69      1848
weighted avg       0.75      0.70      0.71      1848

ROC AUC: 0.7828
```

**Model File Size**
<br>`826 KB`

### 3) XGBoost

**Hypertuning config params:**
```
Best XGBoost Params: {'n_estimators': 1000, 'max_depth': 4, 'learning_rate': 0.1, 'subsample': 1.0}
Best F1 on Validation: 0.7284
```

**Metrics**
```
Classification Report: XGBoost
              precision    recall  f1-score   support

           0       0.86      0.80      0.83      1222
           1       0.66      0.75      0.71       626

    accuracy                           0.79      1848
   macro avg       0.76      0.78      0.77      1848
weighted avg       0.80      0.79      0.79      1848

ROC AUC: 0.8534
```

**Model File Size**
<br>`1.24 MB`

---
**Conclusion on Selected Model: XGBoost**

While both **XGBoost** and **Random Forest** achieved strong performance, **XGBoost** was selected as the final model due to the following reasons:

- It obtained the **highest F1-score for the fall class** (0.71), which is the **primary metric** in this challenge.
- It matched Random Forest in **ROC AUC (0.85)** but with a **significantly smaller model size** (1.24 MB vs. 4.06 MB).
- It showed more **consistent generalization** during validation and test.

This makes XGBoost the **most balanced choice** in terms of both **performance and deployability**.

## 3) Next Steps
In my professional experience, achieving an **F1-score above 70%** is a solid starting point during the research phase. Of course, there are industry-specific applications where higher performance thresholds are required (e.g., healthcare, fintech). However, for this **initial research sprint**, the current results are **acceptable and actionable**.

With additional time and iterations, future sprints can focus on:

- Fine-tuning models via advanced hyperparameter optimization.
- Engineering new features or leveraging deep learning on raw sensor signals.
- Experimenting with cost-sensitive learning or ensemble techniques.

**The current solution provides a strong foundation that can be incrementally improved in future iterations**.
