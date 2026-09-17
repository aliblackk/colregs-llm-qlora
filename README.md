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

I compared the original `Qwen/Qwen2.5-7B-Instruct` model with the fine-tuned model on 20 held-out examples.

### Metrics

I used:

* ROUGE-1
* ROUGE-2
* ROUGE-L

I chose ROUGE because it is easy to calculate and it is one of the metrics allowed in the assignment.

### Results

| Model            | ROUGE-1 | ROUGE-2 | ROUGE-L |
| ---------------- | ------: | ------: | ------: |
| Base Model       |  0.9433 |  0.9241 |  0.9433 |
| Fine-tuned Model |  0.9433 |  0.9241 |  0.9433 |
| Improvement      |  0.0000 |  0.0000 |  0.0000 |

The results are the same for both models. So, based on ROUGE, I did not see any improvement after fine-tuning.

### Why are the results the same?

I think the main reason is that `Qwen/Qwen2.5-7B-Instruct` is already a strong instruction-following model. The COLREGS dataset mostly contains multiple-choice questions with short answers, for example `Choice D: in distress`. The base model can already handle this type of task quite well.

Another reason is the small evaluation set. I used only 20 examples, so the difference between the models may simply be too small to see in this experiment.

Also, ROUGE only looks at the overlap between the generated answer and the reference answer. It does not really measure whether the model understands the COLREGS rule better.

So I would not say that the fine-tuning failed. It just did not give a measurable improvement with this particular evaluation setup.

### Limitations

The main limitation is the small evaluation set of 20 examples. A larger test set would give more reliable results.

Another limitation is the ROUGE metric itself. Since the answers are short, even a small change in the generated text can affect the score. A semantic metric such as BERTScore or an LLM-based evaluation could give a different picture.

The evaluation results are saved in:

`results/evaluation_results.json`

and:

`results/evaluation_summary.csv`



## Reproducibility

Install dependencies:

pip install -r requirements.txt

The base model can be downloaded from Hugging Face:

Qwen/Qwen2.5-7B-Instruct

The saved LoRA adapter is located in the adapter/ directory.
