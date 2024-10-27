# Protein Secondary Structure Prediction using Neural Networks

Protein secondary structure prediction is a vital task in computational biology, as the structural configuration of proteins directly impacts their function.

## Previous runs

- [Tensorflow LSTM](https://colab.research.google.com/drive/1bhPuqLQtwF6GSRCO1RnPC3pL1JFizU9S?usp=sharing)
- ...

## Project overview

### Dataset explaination

The main dataset lists peptide sequences and their corresponding secondary structures. It is a transformation of [rcsb](https://cdn.rcsb.org/etl/kabschSander/ss.txt.gz) downloaded at 2018-06-06 from RSCB PDB into a tabular structure. If you download the file at a later time, the number of sequences in it will probably increase.

| **Column**        | **Description**                                                                         |
| ----------------- | --------------------------------------------------------------------------------------- |
| **pdb_id**        | The ID used to locate the peptide's entry on [RCSB PDB](https://www.rcsb.org/).         |
| **chain_code**    | Code indicating a specific chain when a protein consists of multiple peptides (chains). |
| **seq**           | Amino acid sequence of the peptide.                                                     |
| **sst8**          | Eight-state (Q8) secondary structure classification.                                    |
| **sst3**          | Three-state (Q3) secondary structure classification.                                    |
| **len**           | Length of the peptide sequence.                                                         |
| **has_nonstd_aa** | Indicates whether the peptide contains nonstandard amino acids (B, O, U, X, or Z).      |

## LSTM

LSTM attempt found at [/LSTM](/LSTM/LSTM_tf.ipynb)

### Preprocessing

1. **Kmers:** The amino acid sequences (seq) are split into k-mers of length 3 using the seq2kmers function. This divides each sequence into overlapping substrings, helping the model capture local sequence patterns.
2. **Input Tokenization:** The k-mers are then tokenized for embedding.
3. **Target tokenization:** The target secondary structure labels are one-hot encoded to prepare them for classification.

### Model structure

| **Component**          | **Description**                                                                                                                                                           | **Input Dimensions**        | **Output Dimensions**          |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- | ------------------------------ |
| **Embedding Layer**    | Maps each tokenized k-mer to a 128-dimensional vector. `n_words` is the vocabulary size from tokenization.                                                                | `(MAX_AMINO_ACID_LEN,)`     | `(MAX_AMINO_ACID_LEN, 128)`    |
| **LSTM Layers**        | Two LSTM layers, each with 128 units, process sequence data and capture dependencies. Both layers return sequences, enabling per-residue secondary structure predictions. | `(MAX_AMINO_ACID_LEN, 128)` | `(MAX_AMINO_ACID_LEN, 128)`    |
| **Dropout Layers**     | Dropout of 0.2 after each LSTM layer to reduce overfitting.                                                                                                               | `(MAX_AMINO_ACID_LEN, 128)` | `(MAX_AMINO_ACID_LEN, 128)`    |
| **Dense Output Layer** | A softmax layer outputs probabilities across `n_ssts` classes (three-state or eight-state classification).                                                                | `(MAX_AMINO_ACID_LEN, 128)` | `(MAX_AMINO_ACID_LEN, n_ssts)` |
