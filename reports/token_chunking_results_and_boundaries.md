# Token-Based Chunking Results (Podcast vs PDF)

This report summarizes token-based chunking statistics and shows example chunk boundaries for the podcast transcript and the PDF text.

## Summary table

| Chunk size (tokens) | Overlap (tokens) | # Podcast chunks | Avg podcast len (chars) | # PDF chunks | Avg PDF len (chars) |
|---------------------|------------------|------------------|-------------------------|--------------|----------------------|
| 500 | 0 | 7 | 2314.9 | 10 | 2210.1 |
| 1000 | 0 | 4 | 4051.0 | 5 | 4420.2 |
| 2000 | 0 | 2 | 8102.0 | 3 | 7367.0 |

## Boundary examples

## Podcast token 500 (last 100 characters of first 5 chunks)

### Chunk 1

```text
rustworthy AI as having three components. Right. And they have to exist through the entire lifecycle
```

### Chunk 2

```text
ng into a worker. But also mental integrity. And of course, protection from malicious use. Principle
```

### Chunk 3

```text
akes a suggestion, but a human has to click approve for every single action. But let me play devil's
```

### Chunk 4

```text
 list. Your grocery list. The AI can infer those details with a frighteningly high accuracy. So even
```

### Chunk 5

```text
e first line of defense. They have to be. If an engineer sees that a system is behaving unethically,
```


## PDF token 500 (last 100 characters of first 5 chunks)

### Chunk 1

```text
e engineering, 
and the internet of things. This has been 
done with the unvaluable support of our 

```

### Chunk 2

```text
ial principles such as 
transparency, accountability, and the rule 
of law online. 
It also includes
```

### Chunk 3

```text
he ‘Recommendation on the Ethics of 
Artificial Intelligence’ (hereafter “the Recommendation”). This
```

### Chunk 4

```text
e, ranging 
from research, design, and 
development to deployment 
and use, including maintenance, 

```

### Chunk 5

```text
overnance.

10
7 HUMAN OVERSIGHT AND 
DETERMINATION 
Member States should ensure 
that AI systems do
```

