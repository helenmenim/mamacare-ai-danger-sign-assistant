# MamaCare AI: Bilingual Maternal Danger-Sign Education and Referral Assistant

## Overview

**MamaCare AI** is a bilingual maternal danger-sign education and referral assistant designed for pregnant women in Nigeria. The system supports **Simple English** and **Nigerian Pidgin** and is grounded in **World Health Organization (WHO)** and **Nigerian maternal-health guidelines**.

The project implements a **category-aware Retrieval-Augmented Generation (RAG) pipeline integrated with GPT-5.5**. The assistant retrieves evidence from approved maternal-health documents, returns the supporting **source document and page number**, and applies a **rule-based safety escalation engine** before any generative response is produced.

MamaCare AI is intended for **education and referral support only**. It does not diagnose medical conditions, prescribe medicines, or replace qualified healthcare professionals.

---

## Repository Structure

* `01_knowledge_base_eda.ipynb` – document loading, preprocessing, chunk creation, metadata tagging, and exploratory data analysis.
* `02_retrieval_pipeline.ipynb` – category-aware TF-IDF retrieval, bilingual danger-sign classification, rule-based safety engine, GPT-5.5 grounded response generation, and evaluation.
* `data/raw/` – WHO and Nigerian maternal-health source documents.
* `data/processed/knowledge_base_chunks.csv` – processed knowledge-base chunks.
* `data/evaluation/bilingual_evaluation_dataset.csv` – labelled bilingual evaluation dataset.
* `data/results/evaluation_results.csv` – rule-based baseline evaluation outputs.
* `data/results/llm_evaluation_results.csv` – GPT-5.5 RAG evaluation outputs.

---

## System Architecture

The implemented system follows a **hybrid safety architecture** in which deterministic clinical rules are separated from the generative language model.

### Retrieval Pipeline

1. User enters a symptom in **Simple English** or **Nigerian Pidgin**.
2. Language is detected.
3. A **rule-based safety engine** classifies the symptom into a maternal danger-sign category.
4. A **category-aware TF-IDF retriever** searches the indexed maternal-health knowledge base.
5. The highest-ranked evidence chunk is retrieved together with its **source document and page number**.
6. **GPT-5.5** receives the user query and retrieved evidence and generates a grounded bilingual response.
7. The final response includes the **WHO citation** and the **rule-based safety escalation decision**.

This design ensures that emergency decisions are made **before LLM generation** and that supported responses remain grounded in approved maternal-health documents.

---

## Retrieval Demonstration

**User query**

> My head is hurting badly and my vision is blurry.

**Detected language**

> English

**Detected category**

> Severe headache / possible pre-eclampsia

**Retrieved evidence**

> `WHO_Essential_Practice_2023.pdf`, page 46

**Generated response**

> Severe headache with blurred vision in pregnancy can be a serious danger sign of pre-eclampsia. Please go to the nearest hospital immediately for assessment.

**Safety escalation**

> **Level 3 – Urgent / Emergency referral**

---

## Priority Danger Signs Implemented

* **Vaginal bleeding**
* **Severe headache / possible pre-eclampsia**
* **Reduced fetal movement**

---

## Bilingual Evaluation Dataset

The repository includes a labelled bilingual evaluation dataset located at:

`data/evaluation/bilingual_evaluation_dataset.csv`

The dataset contains **direct statements, indirect expressions, misspellings, negation, and ambiguous wording** in both **Simple English** and **Nigerian Pidgin**. Each test case includes the expected danger-sign category, escalation level, and supporting WHO or Nigerian maternal-health source.

---

## Baseline Rule-Based Evaluation

The rule-based retrieval-and-safety system was evaluated using **14 bilingual maternal-health test cases**.

### Results

| Metric                        |      Result |
| ----------------------------- | ----------: |
| Danger-sign category accuracy | **100.00%** |
| Safety escalation accuracy    | **100.00%** |
| Danger-sign recall            | **100.00%** |
| False-negative rate           |   **0.00%** |
| False-positive rate           |   **0.00%** |
| English accuracy              | **100.00%** |
| Nigerian Pidgin accuracy      | **100.00%** |

The baseline system correctly identified all implemented danger-sign cases, retrieved supporting WHO evidence with document and page citations, and applied the correct safety escalation decision for every test case.

---

## LLM-Augmented RAG Evaluation

The GPT-5.5 integrated retrieval system was evaluated using the same labelled bilingual dataset.

### Results

| Metric                      |      Result |
| --------------------------- | ----------: |
| **LLM category accuracy**   | **100.00%** |
| **LLM escalation accuracy** | **100.00%** |
| **Citation availability**   |  **85.71%** |

Citation availability is intentionally lower than 100% because ambiguous or unsupported symptoms correctly trigger the **safe-fallback mechanism**, which deliberately avoids unsupported citations.

---

## Baseline vs LLM Comparison

| System                | Category Accuracy | Escalation Accuracy | Citation Availability |
| --------------------- | ----------------: | ------------------: | --------------------: |
| Rule-based baseline   |           100.00% |             100.00% |               100.00% |
| **LLM-augmented RAG** |       **100.00%** |         **100.00%** |            **85.71%** |

The implemented LLM system preserved perfect danger-sign classification and safety performance while generating more natural bilingual explanations grounded in WHO evidence.

---

## Safe Fallback Behavior

MamaCare AI is designed to **fail safely**. Unsupported symptoms such as **“I feel tired today”** trigger the **Unknown / unsupported** category and return a conservative educational response recommending professional assessment rather than a diagnosis.

Example:

> “I’m sorry you’re feeling this way. The information I have cannot confirm what is causing this symptom. If you are pregnant and the tiredness is severe, getting worse, or happens together with bleeding, severe headache, blurred vision, fever, difficulty breathing, or your baby is not moving, please go to the nearest hospital immediately. Otherwise, contact your antenatal care provider for assessment.”

For unsupported queries the system intentionally returns **no source citation**, preventing unsupported medical claims.

---

## Knowledge Sources

* World Health Organization. *Pregnancy, Childbirth, Postpartum and Newborn Care: A Guide for Essential Practice* (4th ed., 2023).
* World Health Organization. *WHO Recommendations on Antenatal Care for a Positive Pregnancy Experience* (2016).
* Federal Ministry of Health and Social Welfare, Nigeria. *National Guidelines and Tools on Maternal, Perinatal and Child Deaths Surveillance and Response (MPCDSR)* (2022).

---

## Current Project Status

The current prototype successfully demonstrates:

* Bilingual symptom understanding (English and Nigerian Pidgin)
* Category-aware TF-IDF retrieval
* Retrieval of WHO evidence with **source document and page citation**
* GPT-5.5 grounded bilingual response generation
* Rule-based emergency safety escalation
* Safe handling of ambiguous or unsupported queries
* **100% danger-sign category accuracy**
* **100% safety-escalation accuracy**

---

## Author

**Helen Menim**
