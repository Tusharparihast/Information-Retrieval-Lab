# Information Retrieval Lab

Lab implementations of core Information Retrieval concepts including text preprocessing, inverted and positional indexing, Boolean retrieval, TF-IDF weighting, vector space modeling, and document ranking using Python.

## Lab 1: Text Preprocessing, Inverted Indexing, and Compression

### 1. Text Preprocessing

Text preprocessing converts unstructured textual data into a normalized and consistent form that can be efficiently indexed and searched.

- **Tokenization**: Splitting text into individual terms.
- **Stopword Removal**: Eliminating common words (e.g., "the", "is","and",etc.) which contribute little semantic value.
- **Stemming**: Reducing words to their root form (e.g., "jumping" $\rightarrow$ "jump") using the **Porter Stemmer**.
- **Lemmatization**: Reducing words to their dictionary base form (e.g., "better" $\rightarrow$ "good") using **WordNet**, taking grammatical context into account.

**Mathematical Insight**:
If $V$ is the original vocabulary size, preprocessing yields a reduced vocabulary $V'$ where $|V'| \ll |V|$, improving index efficiency.
  
### 2. Inverted Index Construction

To minimize storage requirements and improve efficiency, posting lists are compressed.

- **Basic Inverted Index**: Maps each term to a list of documents containing it (Posting List).
  - _Structure_: `term` $\rightarrow$ `[doc_id_1, doc_id_2, ...]`
- **Positional Index**: Enhances the basic index by storing term positions within each document.
  - _Structure_: `term` $\rightarrow$ `{doc_id: [pos_1, pos_2], ...}`
  - _Use Cases_: **Phrase Queries** (e.g., "retrieve data") by verifying if positions are adjacent ($pos_{t2} - pos_{t1} = 1$).


### 3. Index Compression

Techniques to reduce the storage footprint of posting lists.

#### A. Gap Encoding (Delta Encoding)

Rather than storing absolute document identifiers, differences between successive document IDs are recorded:

$$
G_1 = d_1
$$

$$
G_i = d_i - d_{i-1}, \quad i > 1
$$

- _Benefit_: Gaps are smaller integers, requiring fewer bits to encode.

#### B. Variable-Byte (VB) Encoding

- Encodes numbers using a variable number of bytes.
- **Mechanism**: The most significant bit (MSB) indicates termination: ($0$ = continue, $1$ = end). The remaining 7 bits store the data.

#### C. Golomb Encoding

- Efficient for geometrically distributed integers (like gaps).

* Uses a parameter $M$ (optimal $M \approx 0.69 \times \text{mean gap}$).

- **Mechanism**:
  1.  **Quotient** $q = \lfloor x/M \rfloor$: Encoded in Unary.
  2.  **Remainder** $r = x \pmod M$: Encoded in Binary.

---

## Lab 2: Boolean Retrieval and Vector Space Model

### 1. Boolean Retrieval

Documents are retrieved based on exact matching using Boolean logic.

- **AND**: Intersection of posting lists ($A \cap B$).
- **OR**: Union of posting lists ($A \cup B$).
- **NOT**: Set difference (All Docs $\setminus A$).

### 2. TF-IDF Representation

Documents are transformed into numerical vectors that reflect the importance of terms.

- **Term Frequency (TF)**: How often term $t$ appears in document $d$.

$$ TF\_{t,d} = \frac{\text{count}(t, d)}{\text{total terms in } d} $$

- **Inverse Document Frequency (IDF)**: Measures how rare a term is across the corpus (penalizes common words).

$$ IDF*t = \log*{10}\left(\frac{N}{DF_t}\right) $$

Where:
- $N$ = total documents
-  $DF_t$ = number of documents containing $t$.

   **TF-IDF Weight**:

$$ W_{t,d} = TF_{t,d} \times IDF_t $$

### 3. Vector Space Representation

Each document is represented as a vector in a high-dimensional term space using:
- **TF-IDF** weighted vectors

This representation enables similarity-based ranking instead of exact Boolean matching.

### 4. Cosine Similarity & Ranking

Used to rank documents by relevance to a query in the Vector Space Model. It measures the cosine of the angle between the query vector $\vec{q}$ and document vector $\vec{d}$.

$$ \text{Similarity}(\vec{q}, \vec{d}) = cos(θ) = \frac{\vec{q} \cdot \vec{d}}{||\vec{q}|| \times ||\vec{d}||} = \frac{\sum (q_i \times d_i)}{\sqrt{\sum q_i^2} \times \sqrt{\sum d_i^2}} $$

- **Interpretation**:
  - $1.0$: Vectors are identical (perfect match).
  - $0.0$: Vectors are orthogonal (no shared terms).

Documents with higher scores are considered more relevant to the query.
