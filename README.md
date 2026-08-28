# Reproducibility Audit of FedLLM for Traffic Flow Prediction

This repository documents a partial reproducibility study of:

> Kaur, S., Sehra, S. S., and Ebrahimi, D. *FedLLM: A Privacy-Preserving Federated Large Language Model for Explainable Traffic Flow Prediction* (2026).

The project reconstructs the paper’s corridor-level analysis and prompt-generation pipeline using raw LargeST traffic-flow data. It also records ambiguities and inconsistencies that affect exact reproduction.

## Objectives

- Reproduce the District 12 Corridor Similarity Score (CSS) ranking.
- Recreate corridor-level descriptive analyses.
- Reconstruct the zero-shot LLM prompt format.
- Evaluate simple forecasting baselines.
- Document methodological details that are missing, ambiguous, or inconsistent in the paper.

## What Was Reproduced

- CSS ranking for District 12 corridors.
- Per-sensor mean flow, standard deviation, zero-rate, and sensor counts.
- The corridor-level exploratory analysis in Figures 3 and 5 was partially reproduced. 
    - Average 24-hour profiles, average weekly profiles, and flow distributions were reproduced
    - the block-wise freeway adjacency structure did not exactly match the paper.
- Corridor flow distributions.
- The structure of the zero-shot prompt shown in the paper.

## Main Reproducibility Findings

1. **The “last 30 minutes” net-flow-change feature appears to use 45 minutes.** The example value of `+9.0` is consistent with three 15-minute intervals, and the model output itself refers to “last 45 min.”
2. **The congestion-ratio formula is not explicitly defined.** A threshold-based reconstruction was tested, but cannot be confirmed from the paper alone.
3. **The I-605 sensor count differs from the paper.** The paper reports 32 sensors, while the District 12 filtering procedure identifies 3.

## Repository Structure

```text
.
├── data/
├── figures/
├── llm_yhat_result.json
├── main.ipynb
├── README.md
├── report.md
└── requirements.txt
```

## Data

This project uses the LargeST traffic-flow dataset. The raw dataset is not included in this repository.
Download or obtain LargeST according to its original data-access terms, then place the required files in:
```
data/
```

## Installation

```bash
git clone https://github.com/Her304/orchid.git
cd orchid
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Running the Analysis

Open and run 
```test
main.ipynb
```

The notebook performs data loading, District 12 filtering, CSS calculation, figure reproduction, prompt construction, and baseline evaluation.

## Baselines

The project evaluates:
- Historical Average baseline
- Zero-shot LLM baseline
Metrics:
- MAE
- RMSE
- MAPE
- R²

## API Key for Zero-Shot LLM Evaluation

The zero-shot LLM evaluation requires an API key from the selected model provider.

1. Create a local `.env` file in the project root:

   ```text
   OPENAI_API_KEY=your_api_key_here
   ```

2. Add `.env` and raw data to `.gitignore`:

```text
.env
data/
__pycache__/
.venv/
```

Never commit `.env` or paste an API key into the notebook, repository, report, or README.

## Limitations

This project does not reproduce the paper’s full federated QLoRA fine-tuning procedure. Exact reproduction would require the original training configuration, client partitioning, model settings, hardware environment, and complete definitions of all prompt features.

## Citation

If you use this repository, please cite the original FedLLM paper and the LargeST dataset.

```test
@inproceedings{liu2023largest,
  title={LargeST: A Benchmark Dataset for Large-Scale Traffic Forecasting},
  author={Liu, Xu and Xia, Yutong and Liang, Yuxuan and Hu, Junfeng and Wang, Yiwei and Bai, Lei and Huang, Chao and Liu, Zhenguang and Hooi, Bryan and Zimmermann, Roger},
  booktitle={Advances in Neural Information Processing Systems},
  year={2023}
}

@article{kaur2026fedllm,
  title={FedLLM: A Privacy-Preserving Federated Large Language Model for Explainable Traffic Flow Prediction},
  author={Kaur, Seerat and Sehra, Sukhjit Singh and Ebrahimi, Dariush},
  journal={arXiv preprint arXiv:2604.16612},
  year={2026}
}

or in APA format:
Kaur, S., Sehra, S. S., & Ebrahimi, D. (2026, April 17). FEDLLM: A Privacy-Preserving Federated Large Language Model for Explainable Traffic Flow Prediction. arXiv.org. https://arxiv.org/abs/2604.16612


```