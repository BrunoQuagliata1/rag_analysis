# RAG Analysis Results

This document presents the comprehensive findings from our RAG (Retrieval-Augmented Generation) analysis, comparing different configurations and their impact on performance metrics.

## Test Configurations

The analysis was performed with the following parameters:
- Chunk sizes: 500, 800, 1000, 1500, 2000 tokens
- Text splitting strategies: recursive_text_splitter, regex
- Embedding models: bge-m3, bge-large-en-v1.5
- Overlap sizes: 0, 100
- Different prompt versions (v1, v2, v3, v4)
- Context retrieval: tested both enabled and disabled

## Key Findings

### Best Overall Configuration

The optimal configuration was found to be:
- Context retrieval: Enabled
- Prompt version: v3
- Chunk size: 1500
- Text splitter: recursive_text_splitter
- Embedding model: bge-m3
- Overlap size: 0

This configuration achieved:
- Faithfulness: 0.77 (77%)
- Answer Relevancy: 0.96 (96%)
- Answer Similarity: 0.94 (94%)
- Answer Correctness: 0.62 (62%)
- Context Precision: ~1.0 (100%)
- Context Recall: 1.0 (100%)

### Embedding Model Comparison

#### BGE-M3 vs BGE-Large-EN-v1.5 (500 chunk size, no overlap)

| Metric | BGE-M3 | BGE-Large-EN-v1.5 | Difference |
|--------|--------|-------------------|------------|
| Faithfulness | 0.51 | 0.43 | +0.08 |
| Answer Relevancy | 0.87 | 0.90 | -0.03 |
| Answer Similarity | 0.91 | 0.91 | 0.00 |
| Answer Correctness | 0.43 | 0.45 | -0.02 |

**Important Note**: BGE-Large-EN-v1.5 has a maximum token limit of 500. Any results with chunk sizes larger than 500 tokens for this model should be disregarded as the context was being truncated. BGE-M3 does not have this limitation and can handle larger chunk sizes effectively.

Key observations:
- At 500 token chunks (valid comparison point):
  - BGE-M3 shows better faithfulness (+8%)
  - BGE-Large-EN-v1.5 shows slightly better answer relevancy (+3%)
  - Similar performance in answer similarity
  - Comparable answer correctness
- BGE-M3's ability to handle larger chunks (>500) makes it more versatile
- BGE-M3 maintains performance with larger contexts, enabling better context understanding
- The ability to use larger chunks with BGE-M3 contributes to its superior performance when combined with optimized prompts

### Chunk Size Analysis

Optimal chunk sizes by metric:
- Faithfulness: 1500 tokens (0.71)
- Answer Relevancy: 1500 tokens (0.87)
- Answer Similarity: 1000-1500 tokens (0.92-0.94)
- Answer Correctness: 1500 tokens (0.48)

### Impact of Overlap

Adding 100 token overlap showed mixed results:
- Improved context recall (+2-3%)
- Slightly decreased faithfulness (-5%)
- Improved answer correctness (+6%)
- Increased computational overhead

### Text Splitting Strategy

Recursive text splitter consistently outperformed regex-based splitting:
- Higher faithfulness (+15%)
- Better answer relevancy (+4%)
- Improved context precision (+2%)

## Current Implementation vs Optimal Configuration

### Current Implementation
Current configuration:
- Chunk size: 500 tokens
- Strategy: recursive_text_splitter
- Context retrieval: disabled
- Overlap size: 0

Current metrics:
- Faithfulness: 0.56 (56%)
- Answer Relevancy: 0.74 (74%)
- Answer Similarity: 0.92 (92%)
- Answer Correctness: 0.43 (43%)

### Recommended Changes

Based on our analysis, we recommend the following changes to improve performance:

1. **Enable Context Retrieval**
   - Current: Disabled
   - Recommended: Enabled
   - Expected improvements:
     - Faithfulness: +26% (to ~0.71)
     - Answer Relevancy: +13% (to ~0.87)
     - Answer Correctness: +17% (to ~0.50)

2. **Increase Chunk Size**
   - Current: 500 tokens
   - Recommended: 1500 tokens
   - Expected improvements:
     - Faithfulness: +15%
     - Answer Relevancy: +12%
     - Answer Correctness: +5%

3. **Add Overlap**
   - Current: 0 tokens
   - Recommended: 100 tokens
   - Expected improvements:
     - Context recall: +3%
     - Answer correctness: +6%

### Implementation Priority

1. Enable context retrieval (highest impact, lowest implementation effort)
2. Increase chunk size to 1500 tokens (significant impact, moderate effort)
3. Add 100 token overlap (moderate impact, moderate effort)

Expected Combined Improvements:
- Faithfulness: +41% (to ~0.79)
- Answer Relevancy: +25% (to ~0.93)
- Answer Correctness: +28% (to ~0.55)
- Context Recall: +3% (to ~1.0)

## Recommendations

1. **Use BGE-M3 with Optimized Prompts**: While BGE-Large-EN-v1.5 shows comparable base metrics, BGE-M3 with optimized prompting (v3) provides the best overall performance.

2. **Optimal Chunk Size**: Use 1500 tokens as the default chunk size, as it provides the best balance across all metrics.

3. **Consider Overlap Tradeoff**: 
   - Use 0 overlap for better faithfulness and faster processing
   - Consider 100 token overlap when answer correctness is critical

4. **Always Use Recursive Text Splitter**: It consistently outperforms regex-based splitting across all metrics.

5. **Context Retrieval**: Enable context retrieval as it significantly improves all metrics:
   - Faithfulness: +26%
   - Answer Correctness: +17%
   - Answer Relevancy: +2%

## Metrics Explained

- **Faithfulness**: Measures how well the generated answers align with the provided context
- **Answer Relevancy**: Evaluates if the response addresses the query appropriately
- **Answer Similarity**: Measures consistency between generated responses
- **Answer Correctness**: Assesses factual accuracy of the responses
- **Context Precision/Recall**: Evaluates the quality of retrieved context

## Future Considerations

1. **Prompt Engineering**: Further optimize prompt v3 to improve answer correctness while maintaining high faithfulness

2. **Hybrid Approaches**: Test combining multiple chunk sizes for different types of content

3. **Dynamic Chunking**: Investigate adaptive chunk sizes based on content complexity

4. **Embedding Fine-tuning**: Explore fine-tuning BGE-M3 on domain-specific data

5. **Overlap Strategy**: Test variable overlap sizes based on content boundaries

## Performance Considerations

- Larger chunk sizes (>1500) showed diminishing returns and increased processing time
- Context retrieval adds minimal overhead while significantly improving results
- Overlap processing increases computational cost by ~20-30% 