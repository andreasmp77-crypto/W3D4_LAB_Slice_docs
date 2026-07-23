# W3D4_LAB_Slice_docs_the_way_Google_wishes_you_would
- Week 3 / Day 4
- Student: Andreas Papachristophorou
- Course: AI Consulting & Integration 2026-07
- Date: 2026-07-23

## Step 2: Implement Fixed-Size Chunking

### Fixed-Size Chunking Results (Podcast vs PDF)

This table summarizes the number and average length of chunks for different fixed-size and overlap settings, comparing podcast transcript and PDF text.

| Chunk size | Overlap | # Podcast chunks | Avg podcast len (chars) | # PDF chunks | Avg PDF len (chars) |
|-----------|---------|------------------|-------------------------|--------------|----------------------|
| 500 | 0 | 33 | 490.1 | 45 | 490.9 |
| 500 | 50 | 36 | 497.8 | 50 | 490.6 |
| 500 | 100 | 41 | 492.0 | 56 | 492.6 |
| 1000 | 0 | 17 | 951.6 | 23 | 960.7 |
| 1000 | 50 | 17 | 998.9 | 24 | 968.3 |
| 2000 | 0 | 9 | 1798.1 | 12 | 1841.5 |


### Analysis questions:

Please also view at [fixed_chunking_boundary_examples.md](reports\fixed_chunking_boundary_examples.md)

1. Does fixed-size chunking break sentences in the middle?

*In both the podcast and PDF, fixed-size chunking frequently breaks sentences and even words in the middle. For example, the podcast chunk ending “Think abou” cuts a word mid-character, and the PDF chunk ending /licenses/by-nc-sa/3 cuts a URL mid-token. This confirms that fixed-size chunking does not respect sentence boundaries and often truncates thoughts mid-sentence.*

2. How does it handle paragraph boundaries?

*Fixed-size chunking sometimes ends near natural line or paragraph breaks (especially in the PDF), but whenever a paragraph or section exceeds the 500-character limit, it slices directly through the paragraph. In practice, many chunks end mid-paragraph, and some even end inside boilerplate license text or URLs, which means paragraph boundaries are only weakly preserved.*

3. Which content type handles fixed-size chunking better?

*Fixed-size chunking behaved slightly better on the PDF than on the podcast transcript. The PDF’s more regular formatting (lines, sections, headings) means some chunks end near reasonable boundaries, even though many still cut sentences or URLs. The podcast transcript, with long conversational sentences and fewer clear paragraph markers, produced more abrupt mid-sentence and mid-word cuts. Overall, fixed-size chunking is more tolerable for structured PDF text and noticeably worse for conversational transcripts.*

## Step 3: Implement Recursive Character Chunking

### Table Summary

| Chunk size (chars) | Overlap (chars) | # Podcast chunks | Avg podcast len (chars) | # PDF chunks | Avg PDF len (chars) |
|--------------------|-----------------|------------------|-------------------------|--------------|----------------------|
| 500 | 0 | 33 | 489.5 | 54 | 407.3 |
| 500 Seper. | 0 | 36 | 449.6 | 54 | 407.3 |
| 1000 | 0 | 17 | 951.2 | 30 | 734.7 |
| 2000 | 0 | 9 | 1797.6 | 17 | 1297.9 |

| Content type | Chunk size (chars) | Overlap (chars) | Separator priority | # Chunks | Avg len (chars) |
|-------------|--------------------|-----------------|--------------------|-----------|-----------------|
| Podcast | 500 | 0 | \n\n > \n > '. ' > ' ' > '' | 36 | 449.6 |
| PDF | 500 | 0 | \n\n\n > \n\n > \n > '. ' > ' ' > '' | 54 | 407.3 |


### Analysis questions:

Please also view at [recursive_chunking_boundary_examples_custom.md](reports\recursive_chunking_boundary_examples_custom.md)

1. Does recursive chunking preserve sentence boundaries better?

*Recursive chunking with custom separators preserved sentence boundaries significantly better than fixed-size chunking. In the podcast, chunk endings fell after full sentences or complete clauses (for example, ending on “We aren't just talking philosophy here”), and there were no mid-word truncations, unlike the fixed-size chunks that cut words like “about” and “done” in half.*

2. How does it handle the podcast's conversational structure?

*For the podcast transcript, recursive chunking with custom separators produced chunks that aligned better with conversational units. Chunks often ended after a complete thought or sentence, such as “We didn't have the generative AI explosion we have today” or “We aren't just talking philosophy here”, which keeps each chunk closer to a self-contained piece of dialogue. The conversational flow is less disrupted compared to fixed-size chunking, even though some breaks still occur mid-utterance.*

3. Does it respect PDF section headers?

*For the PDF, recursive chunking with custom separators respected structural elements like section headers and table-of-contents lines more than fixed-size chunking. One chunk captured a coherent header block (“Key facts / UNESCO’s Recommendation on the Ethics of Artificial Intelligence / Supported by:”), and another ended at a numbered section list (“4 Implementing the Recommendation / 5 Join us”), indicating that recursive splitting aligned chunks with section boundaries and headings rather than cutting through them arbitrarily. However, longer descriptive paragraphs were still split, although mostly at line breaks or spaces instead of mid-word.*

## Step 4: Implement Token-Based Chunking

## Summary table

| Chunk size (tokens) | Overlap (tokens) | # Podcast chunks | Avg podcast len (chars) | # PDF chunks | Avg PDF len (chars) |
|---------------------|------------------|------------------|-------------------------|--------------|----------------------|
| 500 | 0 | 7 | 2312.3 | 10 | 2210.1 |
| 1000 | 0 | 4 | 4046.5 | 5 | 4420.2 |
| 2000 | 0 | 2 | 8093.0 | 3 | 7367.0 |

## Step 6: Visualize and Compare Results

### Trade-offs between chunking strategies (PDF)

Using the PDF document, I compared three chunking strategies:

- **Fixed-500** (character-based, 500 chars, 0 overlap)
- **Recursive-1000** (character-based, 1000 chars, 0 overlap)
- **Token-500** (token-based, 500 tokens, 0 overlap)

**Fixed-500 (character-based)**

- **Pros:**
  - Simple to configure and reason about (fixed `chunk_size` and `chunk_overlap`).
  - Chunks have very consistent sizes around 500 characters (as shown in the histogram and table: average chunk length close to the target).
- **Cons:**
  - Frequently cuts sentences and even words in the middle, especially in longer paragraphs.
  - Paragraph and section boundaries are only weakly preserved; many chunks end inside license text or continuous prose.
- **Best suited for:**
  - Highly uniform text where strict size control matters more than semantic boundaries.

**Recursive-1000 (character-based)**

- **Pros:**
  - Preserves structure better by splitting on paragraph and line separators before falling back to characters.
  - Chunk boundaries often align with section headers and complete sentences, especially in the PDF (e.g. chunks capturing “Key facts / UNESCO’s Recommendation…” and table-of-contents lines).
  - Fewer, larger chunks than Fixed-500 but still within a manageable size for LLMs.
- **Cons:**
  - More complex to configure (separator priority matters) and less predictable than pure fixed-size splitting.
  - Chunk sizes vary more; not all chunks are close to 1000 characters because the splitter respects structural boundaries.
- **Best suited for:**
  - Structured documents (reports, PDFs) where preserving headings and paragraphs is important.

***Token-500 (token-based)***

- **Pros:**
  - Directly aligns chunk size with the model’s token limit, which is more accurate for LLM context windows.
  - Avoids oversized chunks in terms of tokens, even when character length varies.
  - Character-length histograms show that chunks can be longer or shorter than 500 chars, but token counts stay within the intended limit.
- **Cons:**
  - Requires a tokenizer (e.g. `cl100k_base`) and adds an extra dependency.
  - Character-level behavior is less intuitive: chunks may look long or short in characters even though tokens are consistent.
- **Best suited for:**
  - LLM integration scenarios where staying within context window limits is critical and the tokenizer is known.

For this PDF, **Recursive-1000** provided the best balance between preserving context (headings, paragraphs, sentences) and keeping chunk sizes reasonable for downstream LLM use. Fixed-500 is easier but harms semantic coherence, while Token-500 is ideal when strict token budgets matter more than character-level consistency.

## Step 7: Analyze Chunk Quality

- *For the PDF, recursive character chunking clearly preserves sentence boundaries best: about 36.7% of recursive chunks end with a sentence-ending character, while both fixed-500 and token-500 have 0% clean endings, meaning they almost always cut off mid-sentence. This matches what you saw in the boundary examples, where recursive chunks often stopped at section breaks or full sentences, while fixed and token strategies sliced through running paragraphs.*

- *For the podcast, token-based chunking preserves sentence boundaries better than the other two: about 28.6% of token-500 chunks have clean endings, compared to 11.8% for recursive-1000 and 6.1% for fixed-500. This suggests that, for conversational text, aligning chunk size with token limits plus the way the tokenizer segments text leads to more chunks ending at natural sentence boundaries than raw character-based rules.*

## Step 8: Make Recommendations

### Chunking Strategy Recommendations

#### For PDF Documents

**Recommended Strategy:** Recursive character chunking (Recursive‑1000 with custom separators)

**Reasoning:**
- Recursive‑1000 is the only strategy that achieves a meaningful rate of clean sentence endings for the PDF (about 36.7% vs 0% for both Fixed‑500 and Token‑500), so many chunks end at natural sentence boundaries instead of mid‑sentence or mid‑word. 
- Boundary examples and histograms show that recursive chunks often align with section headers and paragraph blocks (for example, chunks containing “Key facts / UNESCO’s Recommendation on the Ethics of Artificial Intelligence”), preserving the document’s structure while keeping chunk sizes under roughly 1000 characters. 
- **Optimal chunk size and overlap:** `chunk_size ≈ 1000` characters, `chunk_overlap = 0`, with separators that prioritize section and paragraph breaks, such as `["\n\n\n", "\n\n", "\n", ". ", " ", ""]`, to keep headers attached to their content while avoiding extremely uneven chunk sizes. 

#### For Podcast Transcripts

**Recommended Strategy:** Token‑based chunking (Token‑500)

**Reasoning:**
- For the podcast, token‑based chunking has the highest proportion of clean sentence endings (about 28.6%) compared to Fixed‑500 (6.1%) and Recursive‑1000 (11.8%), which means more chunks stop at complete sentences instead of cutting conversational turns mid‑thought. [file:124]
- Token‑based histograms and boundary examples show that chunks remain within tight token budgets while still capturing full ideas more often than character‑only strategies, which either cut too aggressively (fixed) or produce highly variable chunk sizes (recursive). 
- **Optimal chunk size and overlap:** `chunk_size ≈ 500` tokens, `chunk_overlap = 0` or a small overlap (for example, 50 tokens) when extra continuity is needed, using a tokenizer compatible with the target LLM (such as `cl100k_base`) so that chunk sizes map directly to context‑window limits.

#### Trade-offs Summary

| Strategy     | Pros                                                                 | Cons                                                                                 | Best For                              |
|--------------|----------------------------------------------------------------------|--------------------------------------------------------------------------------------|----------------------------------------|
| Fixed-Size   | Simple, predictable; very uniform chunk lengths near the target size | Frequently breaks sentences and words; weakly respects paragraphs and structure      | Very uniform content; quick baselines  |
| Recursive    | Preserves document structure; better sentence and section alignment  | More complex to configure; chunk sizes vary; separator priority must be tuned        | Structured docs (reports, PDFs)        |
| Token-Based  | Aligned with LLM token limits; good balance for conversational text  | Requires a tokenizer; character lengths vary; still may cut sentences for token caps | LLM integration and chat transcripts   | 