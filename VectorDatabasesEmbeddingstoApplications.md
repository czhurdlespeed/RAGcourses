# Vector Databases: from Embeddings to Applications

Calvin Wetzel

## How to Obtain Vector Representations of Data?

- Used heat maps to visualize the embeddings of high dimensional data
  - Check out the barcode code

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.heatmap(embedding[0].reshape(-1,384),cmap="Greys",center=0,square=False)
plt.gcf().set_size_inches(10,1)
plt.axis('off')
plt.show()

sns.heatmap(embedding[1].reshape(-1,384),cmap="Greys",center=0,square=False)
plt.gcf().set_size_inches(10,1)
plt.axis('off')
plt.show()

sns.heatmap(embedding[2].reshape(-1,384),cmap="Greys",center=0,square=False)
plt.gcf().set_size_inches(10,1)
plt.axis('off')
plt.show()
```

- Created 2D embeddings of MNIST data using VAE

```python
# Plot of the digit classes in the latent space
x_te_latent = encoder_f.predict(x_te_flat, batch_size=batch_size,verbose=0)
plt.figure(figsize=(8, 6))
plt.scatter(x_te_latent[:, 0], x_te_latent[:, 1], c=y_te, alpha=0.75)
plt.title('MNIST 2D Embeddings')
plt.colorbar()
plt.show()
```

- Use Dot product, Euclidean distance (L2), Manhattan distance (L1), and Cosine distance to compare embeddings

## Searching for Similar Vectors

- Can use K-Nearest Neighbors (KNN); however, O(kn) as data doubles runtime doubles
- Brute force to slow for large datasets and many queries

## Approximate Nearest Neighbors

- Navigable Small World (NSW)
- Hierarchical Navigable Small World (HNSW)
  - Low likelihood in higher levels
  - Query time increases logarithmically

## Vector Databases

- [Weaviate](https://weaviate.io/)
- CRUD operations: Create, Read, Update, Delete

## Sparse, Dense, and Hybrid Search

- **Dense Search** (Semantic search)
  - Uses vector embedding representation of data to perform search
  - Allows one to capture and return semantically similar objects
  - **Downsides:**
    - _Out of domain data_ will result in poor accuracy
    - _Product with a Serial Number_ searching for seemingly random data
- **Sparse Search**
  - _Bag of Words_ count how many times a word occurs in the query and the data vector and then return objects with the highest matching word frequencies; each word is a dimension of the vector which makes it sparse but also as long as the vocabulary
    - BM25
- **Hybrid Search**
  - Performs both vector/dense and keyword/sparse search and then combines the results
  - Combination based on a scoring system

## Applications - Multilingual Search

- Because embedding produces vectors that convey _meaning_, vectors for the same phrase in different languages produces similar results
- Retrieval Augmented Generation (RAG)
  - Using a Vector Database as an External Knowledge Base
    - Enable LLM to leverage a vector database as an external knowledge base
  - Retrieve Relevant infor and provide to LLM
    - Improve an LLM by enabling it to retrieve relevant source material from the vector database and read it as part of the prompt prior to generating an answer to the prompt
  - Synergizes with a Vector Database
    - Vector databases can be queried for concepts using natural language
  - Better than having to retrain or fine-tune the LLM (less computational resources)
  - Advantages
    - Reduce Hallucination
    - Enable LLM to cite sources
    - Solve knowledge-intensive tasks
  - Workflow
    - Query a vector database
    - Obtain relevant information
    - Stuff the objects/information into the prompt
    - Send the modified prompt to the LLM to generate an answer

```python
prompt = "Write me a facebook ad about {title} using information inside {text}"
result = (
  client.query
  .get("Wikipedia", ["title","text"])
  .with_generate(single_prompt=prompt)
  .with_near_text({
    "concepts": ["Vacation spots in california"]
  })
  .with_limit(3)
).do()

json_print(result)
```
