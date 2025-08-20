# Document Relevance Cleaning Pipeline

## Overview
We discovered that two different relevance classification approaches disagree on ~25% of documents in the BEIR benchmark. By analyzing these disagreements, we developed an improved cleaning pipeline to create more accurate relevance labels.

## The Discovery Process

### Initial Finding: Two Models, Different Errors
We tested two different prompting approaches:

1. **First Prompt** (more free-form):
   - Included reasoning and explanations
   - Designed for exploratory classification
   - Disagreed with dataset on ~25% of documents

2. **Second Prompt** (structured cleaning):
   - Based on patterns found from the first prompt's errors
   - More systematic relevance categories
   - ALSO disagreed with dataset on ~25% of documents
   - **BUT disagreed on DIFFERENT documents!**

### Key Insight
When two independent approaches both disagree with the dataset by 25% but on **different documents**, it suggests:
- The dataset itself may have consistency issues
- Each approach catches different types of problems
- A combined approach could be more accurate

## Analysis of Disagreements

### What We Found
By examining where the models disagreed:

1. **Type 1 Disagreements** (Model says relevant, dataset says not):
   - Often involved indirect relevance
   - Example: SARS research → COVID-19 queries (related coronaviruses)
   - Example: Surrealist art concepts → surrealist film queries

2. **Type 2 Disagreements** (Model says not relevant, dataset says yes):
   - Often involved overly broad dataset labels
   - Example: General health study marked relevant for specific disease
   - Example: Tangentially related content marked as relevant

### The Pattern
- First prompt: Better at catching certain relevance types
- Second prompt: Better at identifying different relevance patterns
- Dataset: Inconsistent labeling standards

## Our Solution: Refined Cleaning Pipeline

### The Final Cleaning Prompt
Based on analyzing both prompts' strengths and weaknesses, we created a final cleaning prompt that:

1. **Keeps binary classification** (compatible with existing systems)
2. **Uses confidence to encode relevance strength**:
   ```
   0.9-1.0 = Highly relevant (direct answer)
   0.7-0.9 = Somewhat relevant (same domain, e.g., SARS for COVID)
   0.5-0.7 = Generally relevant (related field)
   ```
3. **Captures nuanced relevance types** identified from both previous prompts

### Multi-Model Validation
Running the final cleaning prompt on 5 models:
- **GPT-5-nano**: Fastest, most cost-efficient
- **GPT-5-mini**: Balanced speed and accuracy
- **GPT-5**: Best for complex reasoning
- **GPT-4.1**: Highly intelligent with large context
- **o3**: OpenAI's reasoning specialist

## The Process

### Phase 1: Initial Testing (✅ Complete)
- Ran first prompt (free-form with reasoning)
- Found 25% disagreement with dataset

### Phase 2: Comparative Analysis (✅ Complete)
- Developed second prompt based on first prompt's patterns
- Found another 25% disagreement (different documents!)
- Realized both prompts were finding real issues

### Phase 3: Pattern Analysis (✅ Complete)
- Studied which documents each prompt got "wrong"
- Discovered they were often actually correct
- Identified systematic patterns in disagreements

### Phase 4: Final Prompt Development (✅ Complete)
- Combined insights from both prompts
- Created cleaning prompt with confidence-based relevance
- Designed to catch both types of errors

### Phase 5: Multi-Model Testing (🔄 In Progress)
- Running final prompt on all 5 models
- Testing on ~20,000 examples
- Will identify if one model is sufficient or need consensus

### Phase 6: Production Strategy (📋 Planned)
Based on results:
- Find optimal model(s) and confidence thresholds
- Deploy for full dataset cleaning

## Key Innovation: Learning from Disagreements

Instead of assuming the dataset is correct, we:
1. Used two independent approaches
2. Found they disagree with dataset on different documents
3. Analyzed the disagreements to understand true relevance
4. Created a final prompt that captures both perspectives

## File Structure

```
cleanup/
├── cleaning_benchmark_analysis.py   # Final cleaning prompt benchmark
├── prompt_disagreements.json        # Analysis of model disagreements
├── show_600_raw_examples.py        # Display disagreement examples
├── cleaning_analysis_*.json        # Results for each model
├── model_pricing.json              # Cost calculations
└── README.md                       # This file
```

## Running the Pipeline

```bash
# Run final cleaning prompt on all models
python3 cleaning_benchmark_analysis.py

# Analyze specific disagreements
python3 show_600_raw_examples.py

# Results saved per model
# cleaning_analysis_gpt-5-nano.json
# cleaning_analysis_gpt-5-mini.json
# etc.
```

## Expected Outcomes

1. **Find best model(s)** for production use
2. **Determine optimal confidence thresholds**
3. **Validate that our approach is more consistent** than original dataset
4. **Create scalable pipeline** for cleaning millions of documents

## The Plan

1. **Testing (Current)**: Run final prompt on 20k examples with 5 models
2. **Analysis**: Determine if one model is sufficient (hopefully GPT-5 or GPT-4.1)
3. **Optimization**: Set confidence thresholds based on use case
4. **Production**: Deploy most cost-effective solution
   - Best case: Single model with threshold
   - Alternative: 2-3 model consensus

## Confidence Threshold Strategy

```python
# After running all models, tune thresholds:
STRICT = 0.8   # High precision (legal, medical)
BALANCED = 0.6 # Default setting
INCLUSIVE = 0.4 # High recall (research)

# Apply based on use case
final_relevant = llm_classification and (confidence >= threshold)
```

## Conclusion

By analyzing disagreements between different prompting approaches and the dataset, we discovered systematic patterns that led to a better understanding of document relevance. Our final cleaning pipeline leverages these insights to create more accurate and consistent relevance labels, with tunable precision/recall via confidence thresholds.
