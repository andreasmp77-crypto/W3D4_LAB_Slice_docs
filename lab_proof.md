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