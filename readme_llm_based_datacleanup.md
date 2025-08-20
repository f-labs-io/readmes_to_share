# Document Relevance Cleaning Pipeline

## Overview
We discovered that two different relevance classification approaches disagree on ~25% of documents in the BEIR benchmark. By analyzing these disagreements with AI coding agents, we developed an improved cleaning pipeline to create more accurate relevance labels.

**Important: This entire analysis and development process was done using AI coding agents** - from the initial discovery of dataset issues to prompt refinement to the iterative analysis approach.

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

## The Process (AI-Assisted Development)

### Phase 1: Initial Testing (✅ Complete)
- Ran first prompt (free-form with reasoning)
- Found 25% disagreement with dataset

### Phase 2: Comparative Analysis (✅ Complete)
- Developed second prompt based on first prompt's patterns
- Found another 25% disagreement (different documents!)
- Discovered both prompts were finding real issues

### Phase 3: Pattern Analysis (✅ Complete)
- Analyzed which documents each prompt got "wrong"
- Found they were often actually correct
- Identified systematic patterns in disagreements

### Phase 4: Final Prompt Development (✅ Complete)
- Combined insights from both prompts
- Created cleaning prompt with confidence-based relevance
- Designed to catch both types of errors

### Phase 5: Multi-Model Testing (🔄 In Progress)
- Running final prompt on all 5 models
- Testing on ~20,000 examples
- Will analyze results to identify optimal configuration

### Phase 6: Production Strategy (📋 Planned)
Will determine:
- Optimal model(s) and confidence thresholds
- Best rule combinations
- Deployment for full dataset cleaning

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

## What We're Actually Doing Now(The Real Process)

### Step 1: Run All 5 Models (Current)
Running our final cleaning prompt on ~20k examples with all 5 models.

### Step 2: Iterative Analysis (Next)
Using AI assistance with `show_600_raw_examples.py` and similar scripts to:
- Look at actual examples where models disagree
- Understand patterns in the disagreements
- Update thresholds and rules based on findings
- Decide if more examples are needed
- Continue until models are consistent enough

**The exploration process:**
- Try different thresholds (0.5, 0.6, 0.7, 0.8)
- Test different agreement levels (2/5, 3/5, 4/5 models)
- Combine agreement + confidence (e.g., 3 models agree AND avg confidence > 0.7)
- Keep testing until finding what works

Iterating many times:
1. Examine examples
2. Propose a rule
3. Test if it works
4. Adjust and repeat

### Step 3: Find the Best Configuration
Through this iterative process, we'll discover:
- Maybe 1 model is enough (GPT-5 with confidence > 0.7)
- Maybe need 2 models agreeing
- Maybe need special rules for edge cases
- We don't know yet - we'll find out by testing!

### The Final Configuration Will Include:
- Which model(s) to use
- Confidence thresholds
- Agreement rules
- Any special handling

This is exploratory - we're not assuming the answer, we're finding it through data!

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

Through AI-assisted analysis of disagreements between different prompting approaches and the dataset, we discovered systematic patterns that led to a better understanding of document relevance. Our final cleaning pipeline leverages these insights to create more accurate and consistent relevance labels, with tunable precision/recall via confidence thresholds.

The AI assistance enabled rapid testing of hundreds of examples and iterative refinement based on data patterns - achieving a more robust solution than manual analysis alone.
