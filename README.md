# HybridCodeAuthorship

> **🎉 Accepted to LREC 2026 🎉** > *The paper introducing this dataset has been accepted to LREC 2026. A link to the official publication will be provided here after the conference concludes (post 05/16/2026).*

HybridCodeAuthorship is a benchmark dataset designed for line-level and chunk-level AI-generated code authorship detection. With the rapid adoption of AI code assistants, industry codebases are increasingly becoming a hybrid of AI- and human-authored code. This dataset simulates the authentic utilization of AI code assistants by interleaving human and AI-authored lines of code.

## Dataset Overview
- **Base Data:** Derived from CodeSearchNet, focusing on Python code files.
- **Models Used:** Llama 3.3-70B, Llama-4-Scout, and GPT-OSS-120b.
- **Size:** 10,488 records derived from 4,196 Python code files.
- **Total Lines of Code:** 2,827,938 (17% AI-generated, 69% nontrivial).
- **Quality Checks:** Includes labels indicating whether the code passed unit tests or is AST parsable.

## Repository Structure

```text
c1-hybrid-code-authorship/
├── data/
│   └── HybridCodeAuthorship.csv
├── LICENSE
└── README.md
```

## Schema
The dataset schema (`HybridCodeAuthorship.csv`) includes the following columns:

| Column Name | Data Type | Description |
| :--- | :--- | :--- |
| `ModelId` | String | Identifier for which LLM was used for code generation. |
| `RecordId` | String | A unique identifier for each code sample, serving as the primary key when combined with `ModelId`. |
| `Language` | String | The programming language of the code sample (Python). |
| `GitHubUrl` | String | The URL to the original GitHub repository and file. |
| `HumanCode` | String | The original, unmodified human-authored code. |
| `HumanCodeTier` | String | The validity tier of the human-authored code ("Unit Test Passed", "AST Parsable", "Unparsable"). |
| `AICode` | String | The final code containing interleaved AI-generated content. |
| `AICodeTier` | String | The validity tier of the AI-generated code ("Unit Test Passed", "AST Parsable", "Unparsable"). |
| `AICodeLines` | List of String | `AICode` string as a list with each line its own item. |
| `LineNumber` | List of Int | List of line numbers for `AICode`. Used as index for lists in `AICodeLines`, `Attribution`, and `Triviality` columns. |
| `Attribution` | List of String | List of attribution labels for `AICode` lines ("AI" or "Human"). |
| `Triviality` | List of String | List of triviality labels for `AICode` lines ("Trivial" or "Nontrivial"). |
| `AILineProportion` | Float | The actual proportion of lines attributed to AI in the `AICode`. |

## Quick Start
Below is a quick demo using Python and `pandas` to load and inspect the dataset:

```python
import ast
import glob
import pandas as pd

# Reads all chunks and fuses them into one DataFrame in memory automatically
file_paths = sorted(glob.glob("data/HybridCodeAuthorship_part_*.csv"))
df = pd.concat((pd.read_csv(f) for f in file_paths), ignore_index=True)
print(f"Dataset loaded! Shape: {df.shape}")

# Safely evaluate list columns from strings
list_cols = ['AICodeLines', 'LineNumber', 'Attribution', 'Triviality']
for col in list_cols:
    df[col] = df[col].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)

# View basic statistics
print(f"Total records: {len(df)}")
print(f"Models used: {df['ModelId'].unique()}")

# Inspect the first record's code and attribution line-by-line
first_record = df.iloc[0]
for line_num, code_line, attribution in zip(first_record['LineNumber'], first_record['AICodeLines'], first_record['Attribution']):
    print(f"[{attribution}] Line {line_num}: {code_line.strip()}")
```

## Intended Use & Limitations
This dataset is intended for researchers and practitioners developing and evaluating fine-grained (line-level and chunk-level) AI-generated code detection algorithms. 

**Known Limitations:**
* **Language Scope:** The current version is limited to Python code. 
* **Temporal Scope:** To guarantee the absence of AI-generated code in the baseline human samples, the source files from CodeSearchNet are 6+ years old. Consequently, the dataset may not reflect recently developed or popularized Python libraries.
* **Pipeline Attrition:** Some highly complex or very long code files failed the automated code-interleaving pipeline due to LLM context limits or instruction-following failures, which may slightly impact the macroscopic representativeness of the dataset.

## Contributing & Issues
If you discover formatting errors, broken links, or issues with the dataset, please [open an issue](https://github.com/CapitalOne-Research/c1-hybrid-code-authorship/issues) in this repository. We welcome feedback and community validation!

## Citation
If you use this dataset in your research, please cite our LREC 2026 paper. 
*(Full citation details and the paper link will be updated after May 16, 2026).*

## License
This project is licensed under the Apache 2.0 License.

## Authors
Luke Patterson, Li Wang, Adam Faulkner (Card Intelligence, Capital One).