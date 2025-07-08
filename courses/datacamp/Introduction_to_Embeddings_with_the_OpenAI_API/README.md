# [Introduction to Embeddings with the OpenAI API](https://app.datacamp.com/learn/courses/introduction-to-embeddings-with-the-openai-api)

## What are embeddings?

+ Numerical representation of text.
+ semantic meaning: context and intent behind text.
+ Embedding allows **semantic search engines**. The search query will be passed to a embedding model and it will return the model numbers.
+ Classification
    + classify sentiment
    + cluster observations
    + categorization
+ create an embeddings request
    + OpenAI offer embeddings endpoints
    ```python
    from openai import OpenAI
    client = OpenAI(api_key="<OPENAI_API_KEY>")
    response = client.embeddings.create(
        model="text-embedding-3-small",
        input="Embeddings are a numerical representation of text that can be used to measure the relatedness between two pieces of text."
    )
    
    response_dict = response.model_dump()
    print(response_dict)
    ```
+ The output is very extense and it will contain a lot of elements

## Investigating the vector space

+ Example dataset:
```python
# List of news articles with headlines and their corresponding topics
articles = [
    {"headline": "Economic Growth Continues Amid Global Uncertainty", "topic": "Business"},
    {"headline": "Interest rates fall to historic lows", "topic": "Business"},
    {"headline": "Scientists Make Breakthrough Discovery in Renewable Energy", "topic": "Science"},
    {"headline": "India Successfully Lands Near Moon's South Pole", "topic": "Science"},
    {"headline": "New Particle Discovered at CERN", "topic": "Science"},
    {"headline": "Tech Company Launches Innovative Product to Improve Online Accessibility", "topic": "Tech"},
    {"headline": "Tech Giant Buys 49% Stake In AI Startup", "topic": "Tech"},
    {"headline": "New Social Media Platform Has Everyone Talking!", "topic": "Tech"},
    {"headline": "The Blues get promoted on the final day of the season!", "topic": "Sport"},
    {"headline": "1.5 Billion Tune-in to the World Cup Final", "topic": "Sport"}
]

# Extract only the headline text from each article
headline_text = [article['headline'] for article in articles]

# Generate embeddings for the list of headlines using OpenAI's embedding model
response = client.embeddings.create(
    model="text-embedding-3-small",
    input=headline_text
)
```

+ batching is more efficient that using multiple API calls

```python
# Convert the response object to a dictionary for easier access
response_dict = response.model_dump()

# Add the corresponding embedding to each article
for i, article in enumerate(articles):
    article['embedding'] = response_dict['data'][i]['embedding']

print(articles[:2])
```

+ OpenAI embeedings return 1536 numbers always
+ in order to visualize those embeedings, it is needed to reduce dimensions to 2 or 3. This can be done with several techniques (PCA, t-SNE, UMAP), and in this case it will be done with t-SNE
```python
from sklearn.manifold import TSNE
import numpy as np

embeddings = [article['embedding'] for article in articles]

tsne = TSNE(n_components=2, perplexity=5)
embeddings_2d = tsne.fit_transform(np.array(embeddings))

```

+ `n_components`: the resulting number of dimensions
+ `perplexity`: used by the algorithm, must be less than number of data points (default 30 work most of the time)
+ Dimensionality reduction inevitabilly will result in loss of some information
+ Visualizing
```python
import matplotlib.pyplot as plt

plt.scatter(embeddings_2d[:, 0], embeddings_2d[:, 1])
topics = [article['topic'] for article in articles]

for i, topic in enumerate(topics):
    plt.annotate(topic, (embeddings_2d[i, 0], embeddings_2d[i, 1]))

plt.show()
```

![alt text](<resources/img/Screenshot 2025-06-20 at 21.56.21.png>)

+ Similar articles are grouped together!
+ Model captured the semantic meaning

```python
# Set your API key
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Extract a list of product short descriptions from products
product_descriptions = [product['short_description'] for product in products]

# Create embeddings for each product description
response = client.embeddings.create(
  model="text-embedding-ada-002",
  input=product_descriptions
)
response_dict = response.model_dump()

# Extract the embeddings from response_dict and store in products
for i, product in enumerate(products):
    product['embedding'] = response_dict['data'][i]['embedding']
    
print(products[0].items())
```

## Text similarity

+ semantically similar text are embedded more closely in a vector space.
+ measuring distance allows to measure similarity
+ enables embeddings applications as semantic search, recommendations or classification.
+ there are several metrics to measure similarity

```python
from scipy.spatial import distance

distance.cosine([0, 1], [1, 0])
```
+ cosine distance ranges from 0 to 2 and smaller values = greater similarity

```python
# to create embeding in a more consistant way lets use a function
def create_embeddings(texts):
    response = client.embeddings.create(
        model="text-embedding-3-small",
        input=texts
    )
    response_dict = response.model_dump()

    return [data['embedding'] for data in response_dict['data']]

from scipy.spatial import distance
import numpy as np

search_text = "computer"
search_embedding = create_embeddings(search_text)[0]

distances = []

for article in articles:
    dist = distance.cosine(search_embedding, article["embedding"])
    distances.append(dist)

min_dist_ind = np.argmin(distances)
print(articles[min_dist_ind]['headline'])
```
