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

