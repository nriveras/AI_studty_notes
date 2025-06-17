[Working with the OpenAI API](https://app.datacamp.com/learn/courses/working-with-the-openai-api)

# Introduction to the OpenAI API
## What is the OpenAI API

+ OpenAI are the developers of ChatGPT
+ API allows to access the modules of OpenAI.
+ API is an interface between the service and the client.

## Making requests to the OpenAI API

+ API have different access points depending on the functionalities we require.
+ API servers use to require authentification and use to have associated charges.
    + Create an OpenAI acount.
    + Go to API keys page.
    + Create a new secret key.

```python
import openai

openai.api_key = 'your-api-key-here'
response = openai.Completion.create(
  engine="text-davinci-003",
  prompt="What is the OpenAI API?",
  max_tokens=100
)
print(response.choices[0].text.strip())
```

other way:
```python
from openai import OpenAI

client = OpenAI(api_key="Enter your key here")

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role":"user", "content":"What is the openAI API?"}]
)

print(response)
```

```python
# Create the OpenAI client
client = OpenAI(api_key="<OPENAI_API_TOKEN>")

# Create a request to the Chat Completions endpoint
response = client.chat.completions.create(
  model="gpt-4o-mini",
  messages=[
    {"role": "user", 
     "content": "Write a polite reply accepting an AI Engineer job offer."}]
)

print(response.choices[0].message.content)
```

## Summarizing and editing text

+ `promt` object as a multi-line string with tripple quotes(""" """)
+ cost is based in tokens and model used.
+ we can limit the number of tokens in a response with the argument `max_completion_tokens` as:

```python
response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role":"user",
    "content": "Write a haiku about AI."}],
    max_completion_tokens=5
)
```
+ this is important because it determines the price of the ussage. 
    + input and output have different cost.
```python
input_tokens = response.usage.promt_tokens
output_tokens = "...defined by us before..."
```