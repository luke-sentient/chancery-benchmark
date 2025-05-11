# CHANCERY: Evaluating corporate governance reasoning capabilities in language models

Law has long been a domain that has been popular in natural language processing (NLP) applications. While multiple legal datasets exist, none have thus far focused specifically on reasoning tasks, despite the fact that reasoning (ratiocination and the ability to make connections to precedent) is a core part of the practice of the law in the real world. We focus on a specific aspect of the legal landscape by introducing a corporate governance reasoning benchmark **(the CHANCERY benchmark)** to test a model's ability to reason about whether executive/board/shareholder's proposed actions are consistent with corporate governance charters. This benchmark introduces a first-of-its-kind corporate governance reasoning test for language models – modeled after real world corporate governance law. The benchmark consists of a corporate charter (a set of governing covenants) and a proposal for executive action. The model’s task is one of binary classification: reason about whether the action is consistent with the rules contained within the charter. We create the benchmark following established principles of corporate governance - 24 concrete corporate governance principles established in *Gompers et al. (2003)* and 79 real life corporate charters selected to represent diverse industries from a total dataset of 10k real life corporate charters. Evaluations on state-of-the-art reasoning models confirm the difficulty of the benchmark, with models such as Claude 3.7 Sonnet and DeepSeek-R1 achieving 64.5\% and 58.6\%  accuracy respectively. We further conduct an analysis of the types of questions which current reasoning models struggle on, revealing insights into the legal reasoning capabilities of state-of-the-art models.


# CHANCERY Evaluator

eval.py evaluates LLM performance on the CHANCERY benchmark by querying a custom LLM through LiteLLM to measure accuracy.

## Features

- 🤖 Works with multiple LLM providers (OpenAI, Anthropic, Fireworks AI, etc.)
- 📊 Detailed performance analytics and results export
- ⚡ Rate limiting handling with exponential backoff
- 🔄 Progress tracking with estimated completion time
- 📝 Comprehensive CSV output of results

## Requirements

- Python 3.8+
- Dependencies:
  - pandas
  - jsonlines
  - tqdm
  - litellm

## Usage

### Basic Usage

```python
# Set your API keys
setup_api_keys(
    fireworks_key="your_fireworks_key",  # Only needed for Fireworks models
    openai_key="your_openai_key",        # Only needed for OpenAI models
    anthropic_key="your_anthropic_key"   # Only needed for Anthropic models
)

# Run the evaluation
results_df, accuracy = process_charters_with_litellm(
    jsonl_path="CHANCERY benchmark.jsonl",
    csv_path="charters.csv",
    model="anthropic/claude-3-opus-20240229",  # Choose your model
    max_items=502  # Number of examples to process
)

# Save results
results_df.to_csv("evaluation_results.csv", index=False)
print(f"Final accuracy: {accuracy:.2f}%")
```

### Full Example Script

```python
import os
from charter_evaluator import process_charters_with_litellm

# Set API keys directly in environment
os.environ["ANTHROPIC_API_KEY"] = "your_api_key_here"

# Run evaluation with custom parameters
results_df, accuracy = process_charters_with_litellm(
    jsonl_path="path/to/questions.jsonl",
    csv_path="path/to/charters.csv",
    model="anthropic/claude-3-sonnet-20240229",
    max_items=100,           # Process 100 questions
    max_tokens=10,           # Limit response length
    temperature=0.0,         # More deterministic responses
    max_retries=3            # Retry 3 times on rate limits
)

# Save and analyze results
results_df.to_csv("model_evaluation.csv", index=False)
print(f"Evaluation complete with {accuracy:.2f}% accuracy")
```

## Supported Models

You can use any model supported by [LiteLLM](https://github.com/BerriAI/litellm#supported-providers)

## Input Data Format

### JSONL File Format
Each line in the JSONL file should contain:
```json
{
  "question": "Southwest Airlines is planning to prevent shareholders from calling special meetings by amending its bylaws. Given the powers outlined in the charter, is there anything stopping the board from making this change on its own?",
"charter_id": "92380A20120517",
  "answer": "Yes",
}
```

### CSV File Format
The CSV file should contain at minimum:
```
charter_id,text,coname,...
92380A20120517,"Full charter text here...",Southwest Airlines,...
```

## Output Format

The tool generates a CSV file with these columns:

- `charter_id`: ID of the charter document
- `question`: Question asked about the charter
- `expected_answer`: Ground truth Yes/No answer
- `model_response`: Raw model response text
- `prediction`: Processed Yes/No prediction
- `is_correct`: Whether prediction matches expected answer
- `token_usage`: Token usage statistics
- `retries`: Number of retries due to rate limiting

## Advanced Configuration

### Environment Variables

```bash
# Set these in your environment or .env file
export FIREWORKS_API_KEY="your_fireworks_key"
export OPENAI_API_KEY="your_openai_key"
export ANTHROPIC_API_KEY="your_anthropic_key"
```

### Rate Limiting

The tool implements exponential backoff retry logic to handle API rate limits:
- Starts with a 2-second wait
- Doubles wait time with each retry
- Configurable maximum retry attempts

## Compute Resources

All evaluations were completed within a budget of $200. Evaluations take between 1-5 hours to complete depending on the complexity of the model/agent. 

## License

[MIT License](LICENSE)

