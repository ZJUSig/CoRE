# CoRE

CoRE is a dataset repository for code reasoning research, with a focus on intermediate-state understanding, fine-grained reasoning, and input-output prediction over code.

The repository currently includes two core data files:

- `core_io.json`: input-output style code samples
- `core_qa.json`: fine-grained question answering samples over execution steps

It also includes an accompanying paper draft:

- `paper.pdf`

## Repository Contents

The repository currently contains four files:

```text
.
├── README.md
├── core_io.json
├── core_qa.json
└── paper.pdf
```

Dataset statistics:

- `core_io.json`: `1978` samples
- `core_qa.json`: `243` samples
- `core_io.json` covers `60` source tasks
- `core_io.json` includes outputs from `7` models

## Data Files

### 1. `core_io.json`

This file contains standard input-output code samples. Each record typically includes:

- a task instance ID
- an input
- a target output
- a code snippet
- the model associated with the sample

Example:

```json
{
  "id": "36_0",
  "input": "78",
  "output": "2",
  "code": "def fizz_buzz(n: int):\n    count = 0\n    for num in range(1, n):\n        if num % 11 == 0 or num % 13 == 0:\n            count += str(num).count('7')\n    return count",
  "model_used": "o1"
}
```

Field descriptions:

- `id`: unique sample identifier, usually composed of a task ID and sample index
- `input`: program input, currently stored as a string
- `output`: expected program output
- `code`: code snippet to execute or reason about
- `model_used`: model associated with the code/sample

Typical use cases:

- program input-output prediction
- code execution evaluation
- code reasoning benchmarks
- cross-model comparison on generated programs

### 2. `core_qa.json`

This file contains fine-grained QA samples built around execution steps. It is designed to evaluate whether a model truly understands local program state, control flow, and variable updates at specific moments during execution.

Example:

```json
{
  "target_step_index": 171,
  "target_line_number": 6,
  "complexity_type": "State Accumulation + Conditional Evaluation",
  "reasoning": "This step involves a compound condition...",
  "question": "At the end of the iteration where `num = 77`, what is the exact value of `count` after the line `count += str(num).count('7')` is executed?",
  "ground_truth": "2",
  "answer_type": "integer",
  "id": "Fine/36_11",
  "model_used": "qwen3-235b-a22b",
  "source_code": "def fizz_buzz(n: int):\n    count = 0\n    for num in range(n):\n        if num % 11 == 0 or num % 13 == 0:\n            count += str(num).count('7')\n    return count",
  "input": "10000",
  "output": "639",
  "meta_data": {
    "model_name": "qwen3",
    "func_name": "fizz_buzz",
    "cyc_comlexity": 4
  }
}
```

Field descriptions:

- `target_step_index`: execution step index targeted by the question
- `target_line_number`: target line number in the code
- `complexity_type`: reasoning complexity label for the sample
- `reasoning`: explanation of why the step is challenging or noteworthy
- `question`: question posed to the model/evaluator
- `ground_truth`: reference answer
- `answer_type`: answer type, such as `integer`, `boolean`, or `string`
- `id`: sample identifier
- `model_used`: model associated with the sample
- `source_code`: original source code
- `input`: program input
- `output`: program output
- `meta_data`: additional metadata such as function name, source model, and cyclomatic complexity

Typical use cases:

- execution trace understanding
- variable state tracking
- intermediate-step reasoning evaluation
- code-centric QA benchmark construction

## Key Characteristics

CoRE has several notable properties:

- It covers both final-output prediction and intermediate-step QA.
- It focuses on challenging reasoning patterns such as loop boundaries, branching, state accumulation, and nested logic.
- It preserves source code, inputs, outputs, and metadata for reproducibility and analysis.
- It includes samples associated with multiple models, making it useful for cross-model comparison.

## Quick Start

### Load the data in Python

```python
import json

with open("core_io.json", "r", encoding="utf-8") as f:
    core_io = json.load(f)

with open("core_qa.json", "r", encoding="utf-8") as f:
    core_qa = json.load(f)

print("core_io size:", len(core_io))
print("core_qa size:", len(core_qa))

print(core_io[0])
print(core_qa[0]["question"])
```

### Filter by fields

o1_samples = [
    item for item in core_io
    if item.get("model_used") == "o1"
]
```

## Potential Research Uses

- code reasoning
- program execution understanding
- step-level QA over code traces
- intermediate state prediction
- cross-model reasoning comparison

## Notes

When using the dataset, a few practical points may help:

- `input` and `output` are mostly stored as strings and may need task-specific parsing
- code style and function signatures are not fully uniform across samples, so preprocessing may be useful
- `core_qa.json` is especially suitable as a fine-grained evaluation set, not just as supervised training data

## Acknowledgments

The reasoning code used in this project is based on the implementation from [TnTWoW/code_reasoning](https://github.com/TnTWoW/code_reasoning), the official repository for *Unveiling the Magic of Code Reasoning through Hypothesis Decomposition and Amendment*. We sincerely thank the authors for open-sourcing their code and making this work possible.
