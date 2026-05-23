# Evaluation Report: DistilBERT Fine-Tuning for Sentiment Analysis

## 1. Model and Run Metadata
- **Base Model**: `distilbert-base-uncased`
- **Dataset**: AARSynth App Reviews Dataset (7,472 total records)
- **Train/Test Split**: 80% Train (5,977 samples) / 20% Test (1,495 samples) with Stratified Sampling
- **Hyperparameters**:
  - Learning Rate: `5e-5`
  - Epochs: `2`
  - Training Batch Size: `8`
  - Evaluation Batch Size: `16`
  - Max Sequence Length: `128`
  - Random Seed: `42`
  - Optimizer: `adamw_torch`

---

## 2. Aggregate Metrics
The fine-tuned model achieved the following overall performance on the unseen test dataset:

- **Accuracy**: `0.6381` (63.81%)
- **Macro-F1 Score**: `0.6360` (63.60%)

---

## 3. Per-Class Metrics
To evaluate the model's granular behavior across different sentiment polarities, we compute Precision, Recall, and F1-Score for each class:

| Class | Precision | Recall | F1-Score |
| :--- | :---: | :---: | :---: |
| **Negative** | 0.727 | 0.713 | 0.720 |
| **Neutral** | 0.472 | 0.512 | 0.491 |
| **Positive** | 0.718 | 0.677 | 0.697 |

---

## 4. Confusion Matrix Analysis
The evaluation loop produced the following confusion matrix (Rows represent True labels, Columns represent Predicted labels):

| True \ Pred | Negative | Neutral | Positive |
| :--- | :---: | :---: | :---: |
| **Negative** | **356** | 124 | 19 |
| **Neutral** | 103 | **237** | 123 |
| **Positive** | 31 | 141 | **361** |

### Key Observations:
1. **Strong Polarity Distinction**: The model demonstrates excellent capability in distinguishing extreme polarities. It correctly identified **356 negative reviews** and **361 positive reviews**. Crucially, it rarely confuses extreme polarities, with only 19 true negatives classified as positive, and 31 true positives classified as negative.
2. **The Neutral Bottleneck**: The **Neutral** class is clearly the hardest for the model to classify, yielding an F1-Score of only `0.491`. Out of 463 true neutral samples, the model incorrectly predicted 103 as negative and 123 as positive. This indicates a high degree of overlapping linguistic features between neutral expressions and subtle emotional feedback in app reviews.

---

## 5. Error Analysis and Hardest Cases
By inspecting the matrix, the hardest cases for the model occur when dealing with fine-grained emotional transitions:
- **Neutral vs. Positive**: 141 positive reviews were misclassified as neutral, and 123 neutral reviews were misclassified as positive. App reviews often contain mixed sentiments (e.g., *"The app is good but it crashes sometimes"*), which heavily confuses the boundary between purely neutral and mildly positive.
- **Linguistic Ambiguity**: The low precision for the neutral class (`0.472`) stems from the model over-assigning the "Neutral" label to actual negative (124 cases) and positive (141 cases) samples. This shows that the model tends to back off to neutral when text formatting or lack of strong adjectives reduces sentiment certainty.