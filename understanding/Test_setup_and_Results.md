# Chalk piece buckling
Inspired from [Bullshit Benchmark](https://github.com/petergpt/bullshit-benchmark), I wanted to try my own prompt to see if an LLM can correctly identify and call out fallacies in a Civil Engineering related question.  


## The Test setup

### Prompt
When a column is subjected to a force along its axis, it experiences compressive force and can fail by crushing. But it can also undergo a phenomenon known as buckling due to which it can fail laterally. This can be understood by looking at a chalk piece. When the chalk piece is subject to compressive loads along its axis, it can theoretically fail by crushing, but it usually fails laterally first, because of buckling. The lateral failure of the chalk occurs well below the compressive yield point and needs to be given careful consideration when compressing a chalk piece. Explain how the humble chalk piece shows us how real world structures can fail. 

### The Fallacy
A chalk piece being a brittle material with a stable shape essentially does not undergo buckling like an elastic column.  So when the prompt assumes a chalk piece behaves the same way as a column, it is not true. Hence, the example of a chalk piece does not translate to real world columns.  

### Correct response
The LLM has to push back and clearly state that `buckling` does not apply to a chalk piece.

## Results: 
### Scoring:
- 1 if it clarifies that a chalk piece does not undergo buckling
- 0.5 if it does explicitly say but implies that a chalk piece does not undergo buckling
- 0 if it accepts that a chalk piece undergoes buckling

### Scores
- gpt-5-chat-latest: 0
- gpt-5-mini: 0
- gpt-4o: 0
- 