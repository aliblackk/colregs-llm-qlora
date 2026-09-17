# COLREGS LLM Fine-Tuning with QLoRA

Fine-tuning of Qwen/Qwen2.5-7B-Instruct for answering COLREGS (International Regulations for Preventing Collisions at Sea) questions.

## Project structure

```text
colregs-llm-project/
├── README.md
├── col_regs.ipynb
├── requirements.txt
├── data/
│   ├── dataset.jsonl
│   ├── train.jsonl
│   └── eval.jsonl
├── adapter/
│   ├── adapter_config.json
│   ├── adapter_model.safetensors
│   ├── tokenizer.json
│   ├── tokenizer_config.json
│   ├── chat_template.jinja
│   └── training_args.bin
└── results/
    ├── evaluation_results.json
    └── evaluation_summary.csv
```

## Model

Base model:

Qwen/Qwen2.5-7B-Instruct

Fine-tuning method:

- QLoRA
- 4-bit NF4 quantization
- LoRA adapters
- Hugging Face Transformers
- PEFT
- TRL / SFTTrainer

The full base model is not stored in this repository. Only the LoRA adapter is saved.

## Dataset

Dataset:

victor-buhl/COLREGS_ALPACA_SHORT

The dataset contains instruction-response examples related to COLREGS and maritime navigation rules.

The data was split into training and evaluation sets.

The evaluation file contains 30 examples. 20 examples are used for the final model comparison.

## Training

Main training parameters:

- Model: Qwen/Qwen2.5-7B-Instruct
- Quantization: 4-bit NF4
- LoRA rank: 16
- LoRA alpha: 32
- LoRA dropout: 0.05
- Batch size: 1
- Gradient accumulation: 8
- Epochs: 3
- Learning rate: 2e-4
- Maximum sequence length: 512
- Gradient checkpointing: enabled

The model was trained using W&B logging.

## W&B

Training run:

https://wandb.ai/alibek-musabek-aitu/huggingface/runs/4xg4ydgq

Important training results:

- Epochs: 3
- Global steps: 357
- Final logged training loss: approximately 0.273
- Overall training loss: approximately 0.521
- Mean token accuracy: approximately 0.925

The W&B run contains the training loss curve and other training metrics.

## Evaluation

The fine-tuned model is compared against the original `Qwen/Qwen2.5-7B-Instruct` model on 20 held-out examples.

### Metrics

* ROUGE-1
* ROUGE-2
* ROUGE-L

ROUGE was selected because it is simple, reproducible, and explicitly allowed by the assignment requirements.

### Results

| Model            | ROUGE-1 | ROUGE-2 | ROUGE-L |
| ---------------- | ------: | ------: | ------: |
| Base Model       |  0.9433 |  0.9241 |  0.9433 |
| Fine-tuned Model |  0.9433 |  0.9241 |  0.9433 |
| Improvement      |  0.0000 |  0.0000 |  0.0000 |

The fine-tuned model achieved the same ROUGE scores as the base model on the selected evaluation subset. Therefore, fine-tuning did not provide a measurable improvement according to ROUGE in this experiment.

Evaluation results are stored in:

`results/evaluation_results.json`

and:

`results/evaluation_summary.csv`

## Limitations

ROUGE is a lexical overlap metric and does not fully measure semantic correctness. Therefore, the quantitative results should be interpreted together with qualitative inspection of generated answers.

The evaluation was performed on 20 held-out examples, so the results may not fully represent performance on a larger or more diverse COLREGS dataset.

The goal of the project is to demonstrate the complete fine-tuning workflow rather than achieve a perfect benchmark score.


## Reproducibility

Install dependencies:

pip install -r requirements.txt

The base model can be downloaded from Hugging Face:

Qwen/Qwen2.5-7B-Instruct

The saved LoRA adapter is located in the adapter/ directory.
