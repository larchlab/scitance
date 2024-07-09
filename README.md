# Zero-shot Scientific Claim Verification Using LLMs and Citation Text

This repo contains the dataset and analysis code for the ACL 2024 workshop paper ["Zero-shot Scientific Claim Verification Using LLMs and Citation Text"](/) written for the 4th Workshop on Scholarly Document Processing at ACL 2024. 

## Data

***TL;DR* The final dataset is found in `data/scitance`**

An example line in SCitance (`jsonl`) looks like the following:

```json
{
    "id": number,                   # An integer claim ID.
    "claim": string,                # The text of the claim.
    "evidence": {                   # The evidence for the claim.
        [doc_id]: [                 # The rationales for a single document, keyed by a string S2ORC ID.
            {
                "label": enum("SUPPORT" | "CONTRADICT")
            }
        ]
    },
    "doc_ids": number[]       # The documents cited by this claim's source citation sentence.
}
```

An example results file (`json`) looks like the following:
```json
[
    [
        string, # A string value of the actual label
        string  # A string value of the LLM output
    ],
    ...
]
```

For further explanation of our dataset format (corpus vs claims), see [SciFact](https://github.com/allenai/scifact/blob/master/doc/data.md). 

NOTE: We do not include the `sentence` key in our claims, as we are not focused on sentence-level predictions. We also use `doc_ids` as opposed to `cited_doc_ids` to stay consistent with [MultiVerS](https://github.com/dwadden/multivers).

## Environment setup

```
conda create -p ./env python=3.10
conda activate ./env
pip install -r requirements.txt
```

You will need an OpenAI API key in order to run the experiments or generate negations. Create a `.env` file at the root and add the line `OPENAI_API_KEY=<your_api_key>`.

## Generating SCitance

First, the [SciFact](https://github.com/allenai/scifact) dataset must be downloaded and placed into `data/scifact.` Instructions and further explanation can be found in the repo.

The code can be found in `code/dataset_generation.ipynb`. Running this file will generate the dataset in `data/scitance`.

Note: The negation generation does not run as written, as the model used `text-davinci-003` has been deprecated. This model name will need to be updated if when generating new negations.


## Running experiments

### LLM prompting on SCitance

The `scitance_testing.ipynb` file contains code to load SCitance and execute claim verification prompts on the dataset. Running the 'Calculate metrics' section will provide the relevant scores.

### LLM Prompting on SciFact test set

Because the SciFact test set does not include ground truth `doc_ids` to evaluate on, we use [BEIR](https://github.com/beir-cellar/beir) for the document retrieval stage. We adapt [code](https://github.com/beir-cellar/beir/blob/main/examples/retrieval/evaluation/dense/evaluate_sbert.py) to perform dense retrieval with SBERT. We feed in the SciFact test set claims and extract the top k relevant documents for each claim. For our testing on SciFact, we use `k = 3`. These claims and documents can then be run through LLM prompting.

Under the 'Baseline on SciFact test set' heading in `scitance_testing.ipynb`, there are two code blocks to load in the training examples. As noted, one is for SciFact ICL and the other is for SCitance ICL. Only run the block corresponding to the type of ICL you want to use. 

After creating the SciFact test set with `doc_ids` retrieved, update the file path in this block accordingly.

## Citation

If using this work, please cite:

```
TBA
```