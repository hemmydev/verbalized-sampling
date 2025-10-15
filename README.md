

<div align="center">
<h1>Verbalized Sampling: How to Mitigate Mode Collapse and Unlock LLM Diversity</h1>

[![PyPI](https://img.shields.io/pypi/v/verbalized-sampling?style=for-the-badge&logo=pypi&logoColor=white)](https://pypi.org/project/verbalized-sampling/) [![Python](https://img.shields.io/pypi/pyversions/verbalized-sampling?style=for-the-badge&logo=python&logoColor=white)](https://pypi.org/project/verbalized-sampling/) [![Homepage](https://img.shields.io/badge/Homepage-4d8cd8?style=for-the-badge&logo=google-chrome&logoColor=white)](https://www.verbalized-sampling.com/) [![arXiv](https://img.shields.io/badge/arXiv-2510.01171-red?style=for-the-badge)](https://arxiv.org/abs/2510.01171)  [![Blog](https://img.shields.io/badge/Blog-4d8cd8?style=for-the-badge&logo=notion&logoColor=white)](https://simonucl.notion.site/verbalized-sampling)
</div>

---

<p align="center">
  <a href="#quickstart">Quickstart</a> | <a href="#colab-notebooks">Colab</a> | <a href="#installation">Installation</a> | <a href="#usage">Example Usage</a> |  <a href="#reproducing-paper-results">Reproduce Experiments</a> | <a href=https://simonucl.notion.site/verbalized-sampling>Blog</a> | <a href="#citation">Citation</a>
</p>

## Introduction

**Verbalized Sampling (VS)** is a simple prompting strategy that can **improve LLM diversity by 2x**. It works by asking for a list of responses with their corresponding probability. It is **training-free**, **model-agnostic**, **orthogonal to temperature**, **easy to use**, and work on different tasks: **creative writing**, **social simulation**, **synthetic data generation, open-ended QA, random number generation**, and so on. 

<!-- * **Training-Free**: Works with any LLM without fine-tuning—simply apply VS prompts to unlock diversity.
* **Model-Agnostic**: Compatible with GPT, Claude, Gemini, and open models like Llama and Qwen.
* **Measurable Impact**: Achieves 2-3x diversity improvement in creative writing while maintaining quality.
* **Versatile Applications**: Supports creative writing, synthetic data generation, open-ended QA.
* **Complete Framework**: Includes task implementations, evaluation metrics, and reproducible experiments from our paper.
* **Easy to Use**: Simple CLI and Python API for running experiments and comparing methods. -->

<!-- <p align="center">
  <img src="./assets/teaser.png" width=90% alt="Verbalized Sampling" />
</p> -->

## Quickstart

To try Verbalized Sampling, just copy and paste this prompt into any chatbot (ChatGPT, Claude, Gemini, etc.):

```
Generate 10 responses to the user query, each within a separate <response> tag. Each response should be 50-100 words.
Each <response> must include a <text> and a numeric <probability>. Randomly sample the responses from the full distribution.

<user_query>Write a short story about a bear.</user_query>
```

If you want more stories, just respond and ask `Write another 10 stories about a bear.` in the same conversation. For even better results, paste this into a system prompt instead:

```
You are a helpful assistant. For each query, please generate a set of five possible responses, each within a separate <response> tag. Responses should each include a <text> and a numeric <probability>. Please sample at random from the tails of the distribution, such that the probability of each response is less than 0.10.
```

For all of the above in a single function call, the ability to automatically sample from the verbalized responses, and LangChain integration, run `pip install verbalized-sampling` and use it as follows:

```
# Set OPENAI_API_KEY or OPENROUTER_API_KEY in bash

from verbalized_sampling import verbalize, select, DiscreteDist, Item

dist = verbalize(
    "Write an opening line for a mystery novel",
    k=5,
    tau=0.12,
    temperature=0.9,
    seed=42,
)

# Quick view of items & normalized masses
print(dist.to_markdown())
print()

# Deterministic top item
best = dist.argmax()
print(f"Best (argmax): {best.text}")
print()

# Seeded weighted sample
choice = dist.sample(seed=7)
print(f"Sampled (seed=7): {choice.text}")
```

<!-- #### Example 1: Try this system prompt

Copy and paste this system prompt into your favorite LLM playground (ChatGPT, Claude, Gemini, etc.):

**System Prompt**
```
You are a helpful assistant. For each query, please generate a set of five possible responses, each within a separate <response> tag. Responses should each include a <text> and a numeric <probability>. Please sample at random from the tails of the distribution, such that the probability of each response is less than 0.10.
```
**Example User Propmt**
```
Write a short story about a bear.
``` -->


<!-- #### Example 3: Query via API

Use this curl command to try VS-Standard with the OpenAI API. Replace `gpt-4.1` with your model of choice:

```bash
export OPENAI_API_KEY="your_openai_key"
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-4.1",
    "messages": [
      {
        "role": "system",
        "content": "Generate 10 responses to the input prompt, each within a separate <response> tag. Each response should be 50-100 words. Each <response> must include a <text> and a numeric <probability>. Randomly sample the responses from the full distribution. Return ONLY the responses, with no additional explanations or text."
      },
      {
        "role": "user",
        "content": "Write a short story about a bear."
      }
    ],
    "temperature": 1.0
  }'
``` -->

## Colab Notebooks

Explore verbalized sampling with our interactive Jupyter notebooks:

| Notebook                           | Description                                                                                                                                  | Code                                             | Run it Yourself!                                                                                                                                                                      |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Direct vs. Verbalized Sampling** | Head-to-head comparison demonstrating VS effectiveness: 2-3x diversity improvement in creative tasks while maintaining quality               | [View on GitHub](notebooks/vs_base.ipynb)        | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1UDk4W5w6gF0dQ9Tpu0sPQethEht51GXL#offline=true&sandboxMode=true) |
| **Image Generation with VS**       | Visual comparison of Direct Prompting vs. Verbalized Sampling for text-to-image generation, showcasing creative diversity in artistic styles | [View on GitHub](notebooks/vs_with_image.ipynb)  | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1J18VJRnrCjIb6sTivY-znb8C3JsLQCIz#offline=true&sandboxMode=true) |
| **Complete Framework Tutorial**    | Step-by-step guide to using verbalized sampling: API basics, transforms, selection methods, recipes, and advanced features                   | [View on GitHub](notebooks/framework_demo.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1eC0nIUVC1kyANxxzhNib44qmPphdWy9o#offline=true&sandboxMode=true) |

> 💡 **Tip**: Start with **Direct vs. Verbalized Sampling** to see the effectiveness, then explore **Image Generation** for visual results, or dive into the **Complete Tutorial** to learn the full API!



## Installation

```bash
# Lightweight install (API-based models only)
pip install verbalized-sampling

# With GPU support for local models (vLLM, torch, transformers)
pip install verbalized-sampling[gpu]

# Development install
pip install verbalized-sampling[dev]

# Complete install
pip install verbalized-sampling[gpu,dev]
```

### API Keys Setup
```bash
export OPENAI_API_KEY="your_openai_key"
export OPENROUTER_API_KEY="your_openrouter_key"
```

## Usage

### Command Line Interface

```bash
# List available tasks and methods
verbalize list-tasks
verbalize list-methods

# Run an experiment
verbalize run \
    --task joke \
    --model "gpt-4.1" \
    --methods "vs_standard direct vs_cot vs_multi" \
    --num-responses 50

# Run quick test (TODO add this support to the CLI)
verbalize run \
    --task joke \
    --prompt "Write a joke about the weather." \
    --model "gpt-4.1" \
    --methods "direct vs_standard sequence vs_multi" \
    --num-responses 50 \
    --metrics "diversity length ngram joke_quality"

verbalize dialogue \
  --persuader-model "gpt-4.1" \
  --persuadee-model "gpt-4.1" \
  --method direct \
  --num-conversations 5 \
  --num-samplings 4 \
  --max-turns 10 \
  --word-limit 160 \
  --temperature 0.7 \
  --top-p 0.9 \
  --max-tokens 500 \
  --response-selection probability \
  --evaluate \
  --output-file results/dialogue/persuasion_vs_standard.jsonl

```

### Python API

```python
from verbalized_sampling.pipeline import run_quick_comparison
from verbalized_sampling.tasks import Task
from verbalized_sampling.prompts import Method

# Run a quick comparison
results = run_quick_comparison(
    task=Task.JOKE,
    methods=[Method.DIRECT, Method.VS_STANDARD],
    model_name="anthropic/claude-sonnet-4",
    metrics=["diversity", "length", "ngram"],
    num_responses=50,
)

print(f"VS Diversity: {results['VS_STANDARD']['diversity']:.2f}")
print(f"Direct Diversity: {results['DIRECT']['diversity']:.2f}")
```

### Example

```python
from verbalized_sampling.tasks import get_task, Task
from verbalized_sampling.prompts import Method

# Create a task
task = get_task(Task.STORY, num_prompts=10, random_seed=42)

# Generate diverse responses
vs_prompt = task.get_prompt(Method.VS_STANDARD, num_samples=5, prompt_index=0)
responses = model.generate(vs_prompt)
parsed = task.parse_response(Method.VS_STANDARD, responses)
# Returns: [{"response": "...", "probability": 0.15}, ...]

# Chain-of-thought reasoning
cot_prompt = task.get_prompt(Method.VS_COT, num_samples=3)
cot_responses = model.generate(cot_prompt)
parsed_cot = task.parse_response(Method.VS_COT, cot_responses)
# Returns: [{"reasoning": "...", "response": "...", "probability": 0.22}, ...]
```



## Updates
* 🎉 10/01/2025: We release our paper, code and package. Check the release page for more details.

## Reproducing Paper Results

For detailed instructions on reproducing all experiments from our paper, including exact commands, parameter settings, and expected outputs, see:

**📊 [EXPERIMENTS.md](scripts/EXPERIMENTS.md) - Complete Experiment Replication Guide**

This guide provides 1-to-1 mapping between paper sections (§5-8) and experiment scripts.

## Key Results

Our experiments demonstrate consistent improvements across tasks and models:

- **Creative Writing**: 2-3x diversity improvement while maintaining quality
- **Bias Mitigation**: Uniform sampling (KL divergence: 0.027 vs 0.926 for direct)
- **Emergent Scaling**: Larger models show greater benefits from VS
- **Safety**: Preserved refusal rates for harmful content
- **Tunable Diversity**: Control output diversity via probability thresholds

## Repository Structure

```
verbalized_sampling/           # Main package
├── tasks/                     # Task implementations
│   ├── creativity/           # Creative writing tasks
│   ├── synthetic_data/       # Data generation tasks
│   ├── bias/                # Bias mitigation tasks
│   └── safety/              # Safety evaluation
├── prompts/                  # VS method implementations
├── llms/                     # Model interfaces
├── evals/                    # Evaluation metrics
└── cli.py                    # Command line interface

scripts/tasks/                 # Experimental scripts
├── run_poem.py               # Poetry experiments
├── run_story.py              # Story generation
├── run_jokes.py              # Joke writing
├── run_positive_*.py         # Synthetic data generation
├── run_rng.py                # Random number generation
├── run_state_name.py         # Geographic bias
└── run_safety.py             # Safety evaluation
```

## Development

```bash
# Install development dependencies
pip install -e ".[dev]"

# Code formatting and linting
black .
isort .
ruff check .
mypy .

# Run tests
pytest
```

## Citation

If you use Verbalized Sampling in your research, please cite our paper:

```bibtex
@misc{zhang2025verbalizedsamplingmitigatemode,
  title={Verbalized Sampling: How to Mitigate Mode Collapse and Unlock LLM Diversity},
  author={Jiayi Zhang and Simon Yu and Derek Chong and Anthony Sicilia and Michael R. Tomz and Christopher D. Manning and Weiyan Shi},
  year={2025},
  eprint={2510.01171},
  archivePrefix={arXiv},
  primaryClass={cs.CL},
  url={https://arxiv.org/abs/2510.01171}
}
```

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.
