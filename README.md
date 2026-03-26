# deberta-finetuning-for-semantic-classification
DeBERTa-based semantic classification project exploring both full fine-tuning and LoRA for efficient adaptation and performance comparison.


# DeBERTa Fine-Tuning for Semantic Classification

This repository explores how a pretrained **DeBERTa-base** model can be adapted to a **three-class classification task** using the **EduRABSA_ASQE** dataset. The project compares three settings:

1. **Baseline**
2. **Full Fine-Tuning**
3. **LoRA Fine-Tuning**

The main purpose of this work is to measure how much task-specific adaptation improves performance, and to compare standard full fine-tuning with a more parameter-efficient alternative, **LoRA (Low-Rank Adaptation)**.

---

## Project Goal

The goal of this project is to fine-tune a pretrained DeBERTa-base model for **aspect-level sentiment / semantic classification**.  
Although the original dataset is designed for **Aspect-Sentiment Quadruplet Extraction (ASQE)**, in this project it is reformulated into a **3-class classification problem** where the model predicts one of:

- **negative**
- **neutral**
- **positive**

We compare:

- the behavior of the model before meaningful task adaptation (**baseline**)
- the performance after **full fine-tuning**
- the performance after **LoRA fine-tuning**

---

## Model Used

This project uses **DeBERTa-base**, a transformer-based language model that is widely used for NLP classification tasks because of its strong contextual understanding and transfer learning ability.

For the downstream task, the model is used as a **multi-class classifier** with three output labels:

- Negative
- Neutral
- Positive

  ![LoRA Classification Report](https://github.com/abdelrahman234013/deberta-finetuning-for-semantic-classification/blob/main/screenshots/base_model/Screenshot%202026-03-26%20215038.png?raw=true)

  ![LoRA Classification Report]([https://github.com/abdelrahman234013/deberta-finetuning-for-semantic-classification/blob/main/screenshots/base_model/Screenshot%202026-03-26%20215038.png?raw=true](https://github.com/abdelrahman234013/deberta-finetuning-for-semantic-classification/blob/main/screenshots/base_model/Screenshot%202026-03-26%20215046.png?raw=true))

Two adaptation strategies were applied:

### 1. Full Fine-Tuning
In full fine-tuning, the model weights are updated across the network during training so the model can fully adapt to the downstream task.

### 2. LoRA Fine-Tuning
LoRA (**Low-Rank Adaptation**) is a **parameter-efficient fine-tuning** method where only a small number of additional trainable parameters are introduced, while most pretrained weights remain frozen. This makes adaptation lighter and more efficient while still giving strong performance.

---

## Dataset

This project uses **EduRABSA_ASQE**, a version of the EduRABSA dataset published on Hugging Face. EduRABSA is described by its authors as the **first public annotated ABSA dataset for English education reviews**, covering three review subject types: **course reviews, teaching staff reviews, and university reviews**. :contentReference[oaicite:0]{index=0}

On Hugging Face, the `EduRABSA_ASQE` release contains **6.5k total rows** in one default subset, split into about **4k training rows** and **2.5k test rows**. Each dataset row contains the fields `id`, `task_type`, `original_id`, `text`, and `output`. :contentReference[oaicite:1]{index=1}

The important field is `output`, which stores a **list of ASQE annotations**. Each annotation contains:

- **aspect**
- **category**
- **opinion**
- **sentiment** :contentReference[oaicite:2]{index=2}

This means that a single review can contain **multiple labeled opinion targets**, each with its own sentiment. Some reviews can also have an empty output list, meaning no extractable quadruplet was annotated for that row. :contentReference[oaicite:3]{index=3}

### How the Dataset Was Used in This Project

The original dataset is meant for **Aspect-Sentiment Quadruplet Extraction**, not plain classification.  
To make it suitable for DeBERTa-based classification, each annotated quadruplet was converted into a separate classification instance.

So instead of predicting the full quadruplet, the model predicts only the **sentiment label** for a given aspect-aware input.

A typical input format used in this project is:

```text
Aspect: <aspect> [SEP] Opinion: <opinion> [SEP] Review: <review text>
