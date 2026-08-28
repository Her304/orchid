# Reproducibility report of FedLLM for traffic flow prediction

This project is to evaluate the reproducibility of the paper “FedLLM: A Privacy-Preserving Federated Large Language Model for Explainable Traffic Flow Prediction”. The goal of this project is to replicate the data preparation, descriptive figures, and prompt-generation pipeline using the raw LargeST dataset.

The project used the same LargeST traffic-flow data with approximately 8,600 sensors and 105,000 time steps. Focused on District 12 traffic and the corridor-level traffic analysis used in the paper

The following components were reconstructed:

1. District-level sensor filtering  
2. Corridor-level mean flow, standard deviation, zero-rate, and sensor count  
3. Corridor Similarity Score calculation  
4. Prompt fields used for Zero-shot LLM

However, the full scale of federated QLoRA fine-tuning was outside the scope of this reproduction because of the compute requirements.

## Results that are successfully replicated

From the paper, I can replicate the following data from the LargeST dataset:

1. The CSS ranking for District 12 corridors that are sorted in descending order  
2. The corridor-level exploratory analysis in Figures 3 and 5 is partially reproduced  
   1. Average 24-hour profile, average weekly profile and flow distribution are successfully reproduced  
   2. The block-wise adjacency by freeway is reproduced but does not match the paper  
3. The prompt-generator format could be recreated field-for-field from the example prompt shown in the paper  
4.  The results, including MAE, RMSE, MAPE, and R^2, of the approach above are:

| Time | Metric | Zero-shot LLM baseline | Historical Average baseline | Domain-Adapted Qwen | FedLLM |
| :---: | :---: | :---: | :---: | :---: | :---: |
| **15 min** | RMSE | **133.765** | **71.233** | 19.03 | 16.30 |
|  | MAE | **93.607** | **51.604** | 12.00 | 10.56 |
|  | R² | **0.496** | **0.857** | 0.986 | 0.989 |
|  | MAPE | **41.10%** | **22.80%** | 18.25% | 16.44% |
| **30 min** | RMSE | **141.695** | **71.235** | 26.63 | 21.33 |
|  | MAE | **98.685** | **51.607** | 15.86 | 13.80 |
|  | R² | **0.434** | **0.857** | 0.977 | 0.985 |
|  | MAPE | **40.12%** | **22.80%** | 22.84% | 19.27% |
| **45 min** | RMSE | **216.860** | **71.236** | 32.74 | 25.42 |
|  | MAE | **158.960** | **51.609** | 19.27 | 16.61 |
|  | R² | **−0.323** | **0.857** | 0.967 | 0.983 |
|  | MAPE | **84.39%** | **22.80%** | 25.63% | 24.27% |
| **60 min** | RMSE | **91.692** | **71.238** | 37.88 | 30.18 |
|  | MAE | **63.032** | **51.609** | 22.74 | 19.70 |
|  | R² | **0.763** | **0.857** | 0.956 | 0.983 |
|  | MAPE | **25.82%** | **22.80%** | 31.24% | 27.37% |

## Reproducibility findings

1. For the net change over the last 30 minutes in Fig 6, I have tried to calculate it with the flows of 03:45, which is 45, and the flow of 04:15, which is 47\. I can only conclude that the net change over 30 minutes is 2 vehicles; however, when I use the 03:30 flows instead, which is 38, I can conclude the net change over the last 30 minutes is 9 vehicles. And the output produced by the FedLLM also supports the finding, according to the Model Output, they also conclude the net change over the last 45 minutes is 9 vehicles.  
2. The explicit operational definition or formula of congestion ratio is not provided in the paper. To get the congestion value of 0.272 in Figure 6, I used the Maximum-relative Threshold-based congestion ratio, defined as the proportion of observations above a threshold relative to the maximum observed flow. Reverse-engineering the reported value suggests a threshold of approximately 0.714.   
3. The amount of sensor 1605 does not match in Table 2\. I found that the number of sensor 1605-S/N is 3 in District 12\. To verify my point, I calculated the total number of sensors in Table 2, which is 1011, whereas the paper stated that there are 953 sensors in District 12\. The difference is 58 sensors. Because Table 2 counts northbound and southbound directions separately, 29 duplicated sensor entries would account for this difference: 1011 \- 2 \* 29 \= 953\.

However, the findings do not invalidate the paper’s overall contribution. The corridor-ranking results, exploratory analysis, and much of the prompt structure can be reproduced from raw data.

## Limitations and Future

The current project did not completely reproduce the federated fine-tuning procedure. The full reproduction would require specific training configuration, hardware environment, etc. The purpose of this project is trying to document which of the published components can be independently reproduced and which may need further clarification.

Even though I have not obtained the same configuration as in the paper and cannot do the federated fine-tuning procedure, I have tried to do the traffic prediction with the Historical Average baseline and Zero-shot LLM baseline approach.

This project allows me to consolidate the foundation of data cleaning with Pandas and NumPy, and represent the result with Matplotlib. In the foreseeable future, perhaps I can train a small model for traffic data with Long Short-Term Memory techniques, extend my understanding of machine learning, not just an ideology but a practical experience.

## Conclusion

This project successfully reproduced the paper’s corridor-level CSS analysis, major descriptive figures, and the structure of its forecasting prompt. Meanwhile, it identified a few reproducibility gaps, and they may affect the feature engineering and prompt generation.

The central conclusion is that the paper is partially reproducible, its high-level analysis is recoverable from raw data, but exact replication of the forecasting inputs requires additional methodological detail. These findings provide a concrete basis for future replication work and for contacting the authors with focused clarification questions. 