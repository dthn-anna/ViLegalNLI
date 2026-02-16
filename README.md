# ViLegalNLI: Vietnamese Legal Natural Language Inference Dataset

## Overview
ViLegalNLI is the first large-scale Vietnamese Natural Language Inference (NLI) dataset specifically constructed for the legal domain. The dataset consists of 42,012 premise–hypothesis pairs derived from officially promulgated statutory documents and annotated with binary inference labels: **Entailment** and **Non-entailment**.

The dataset spans multiple legal domains and reflects realistic legal reasoning scenarios characterized by strict logical structures, conditional clauses, and domain-specific legal terminology. ViLegalNLI is designed as a standardized benchmark for evaluating Vietnamese NLI models in legal text understanding and reasoning.

## Data Construction

The ViLegalNLI dataset was constructed through a semi-automatic pipeline designed to ensure legal validity, annotation reliability, and realistic inference complexity for the legal NLI task.

### 1. Data Collection
To build a reliable legal corpus, we surveyed official Vietnamese legal sources and selected the Luật Việt Nam portal as the primary data source due to its comprehensive coverage, structured organization, regular updates, and high credibility. These characteristics make it well-suited for legal NLP research.

Data were automatically collected using a Selenium-based crawler implemented in Python to retrieve dynamically generated web content. The crawler systematically gathered document links and extracted full textual content, which was stored in plain text (`.txt`) format for subsequent processing.  
The initial corpus consisted of 514 Vietnamese statutory documents (excluding amendment and supplementary texts) issued up to February 2025.

### 2. Data Preprocessing
To ensure legal validity and temporal currency, documents that were no longer in effect were manually filtered out. The final corpus contains 168 active statutory documents representing the Vietnamese legal framework as of February 2025.

All documents were then cleaned and standardized to remove noise and formatting inconsistencies. Specifically, we eliminated:
- Non-semantic symbols and decorative separators (e.g., “—–”, “_____”, “*****”, “=====”)
- Redundant whitespace
- Administrative signatures and confirmation statements

This preprocessing step improved structural consistency and reduced non-informative artifacts, facilitating more reliable downstream processing and information extraction.

### 3. Premise Extraction
Premises were automatically extracted from articles, clauses, and sub-clauses of the preprocessed legal documents. As a sentence-level NLI dataset, each premise represents a semantically complete and contextually grounded legal statement derived from statutory provisions.

When clauses constituted the smallest structural units, premises were derived directly from clause-level content. To preserve legal context, article-level information was incorporated when necessary, particularly for provisions describing prohibited acts, sanctions, or semantically interdependent enumerations.  
For clauses containing multiple points, premise construction integrated point-level content and was optionally supplemented with clause-level context to ensure semantic completeness and contextual coherence.

The extraction process was implemented using rule-based structural patterns and keyword indicators (e.g., “sau đây”, “như sau”, “bao gồm”, “nghiêm cấm”, “vi phạm”, “bị xử phạt”). Metadata such as document attributes and the positions of articles, clauses, and points were also recorded for traceability.  
This procedure resulted in 20,860 legal premise instances.

### 4. Prompt Optimization
Hypothesis generation was supported by large language models within a semi-automatic framework. Gemini-2.5 Flash was used as the primary model for hypothesis generation, while GPT-4o, DeepSeek-R1, and LLaMA-4 Scout were employed for cross-model labeling and validation.

Prompt design was iteratively refined to improve hypothesis quality and label consistency. Each prompt version was tested on small validation samples and evaluated using inter-model agreement. We adopted Fleiss’ Kappa to measure agreement among annotating models, and prompts achieving κ ≥ 0.85 were selected for large-scale generation.  
This iterative refinement process reduced linguistic ambiguity and improved logical clarity in generated hypotheses.

### 5. Hypothesis Generation
Hypotheses were automatically generated using a controlled prompting strategy guided by predefined generation rules for both entailment and non-entailment relations. These rules include:
- Paraphrasing and semantic reformulation
- Logical consequence inference
- Conditional reformulation preserving logic
- Numerical equivalence transformation
- Contradiction introduction
- Unsupported assumption generation

The framework was specifically designed to reflect the normative structure and linguistic characteristics of Vietnamese legal texts, thereby preserving logical consistency and formal legal style in generated hypotheses.

### 6. Data Validation
To reduce single-model bias and ensure label reliability, the dataset underwent a multi-layer validation process combining automatic cross-labeling and manual review. Three independent models (GPT-4o, DeepSeek-R1, and LLaMA-4 Scout) re-annotated the dataset without access to the original labels.

A sample was retained if at least two of the three models agreed with the original label. Instances with full disagreement were discarded, while partially inconsistent cases were manually reviewed through detailed semantic and legal analysis.  
Overall, the high inter-model agreement indicates strong annotation consistency and reliability.

### 7. Artifact Mitigation and Difficulty Control
To enhance dataset difficulty and mitigate annotation artifacts, we analyzed token–label correlations using Pointwise Mutual Information (PMI). Tokens strongly correlated with specific labels were identified as potential lexical shortcuts.

Instances containing such artifacts were revised through controlled paraphrasing, where hypotheses were rewritten to preserve semantic meaning while removing label-indicative cues. This process reduces superficial lexical bias and encourages models to rely on genuine semantic and logical reasoning.

### 8. Data Splitting
After generation, validation, and refinement, the dataset was split into training, validation, and test sets with an 8:1:1 ratio.  
To prevent data leakage, all hypotheses derived from the same premise were assigned to the same subset, ensuring that no premise appears across multiple splits. Additionally, label and legal-domain distributions were balanced across subsets to support fair and reliable evaluation of Vietnamese legal NLI models.
