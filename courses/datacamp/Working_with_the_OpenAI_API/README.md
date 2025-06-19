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
input_tokens = response.usage.prompt_tokens
output_tokens = "...defined by us before..."
```

## Text generation

+ response of the model is not deterministic (inherently random)
+ randomes is not always desirable. 
    + This can be tweek with the parameter `temperature`
    + ranges from `0` (highly deterministic) to `2` (very random)

```python
response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[{"role":"user", "content":"Life is like a box of chocolates."}],
    temperature=2
)

print(response.choices[0].message.content)
```

+ Other common uses are marketing or product description:
``` python
"""Generate a powerful tagline for a new electric vehicle
that highlights innovation and sustainability."""

"""Write a compelling product description for the UltraFit Smartwatch.
Highlight its key features: 10-day battery life, 24/7 heart rate and sleep tracking, built-in GPS, water resistance up to 50 meters, and lightweight design.
Use a persuasive and engaging tone to appeal to fitness enthusiasts and busy professionals.
"""
```

## Shot prompting
 + Providing examples to get more accurate responses
    + zero-shot: no example, just instructions.
    + one-shot: one example guides the response.
    + few-shot: multiple examples provide more context.
+ when giving more examples, the consistancy and formatting tends to improve.
    ```python
    prompt = """Classify sentiment as 1-5 (bad-good) in the following statements:
    1. The service was very slow -> 1
    2. The steak was awfully good! -> 5
    3. It was ok, no massive complaints. -> 3
    4. Meal was decent, but I've had better. ->
    5. My food was delayed, but drinks were good. ->
    """
    ```
    ```
    1. The service was very slow -> 1
    2. The steak was awfully good! -> 5
    3. It was ok, no massive complaints. -> 3
    4. Meal was decent, but I've had better. -> 4
    5. My food was delayed, but drinks were good. -> 3
    ```

## Chat roles and system messages

+ single-turn task:
    + text generation
    + text transformation
    + classification
    ![alt text](<Resources/img/Screenshot 2025-06-19 at 11.44.49.png>)
+ Multi-turn conversations:
    + build on previous prompts and responses
    ![alt text](<Resources/img/Screenshot 2025-06-19 at 11.45.24.png>)
+ Roles:
    + system: controls assistant's behavior.
    + user: instruct the assistant
    + assitant: response to oser instruction
        + can also be written by the developer to provide examples.
    ```python
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", 
            "content": "You are a Python programming tutor who speaks concisely."},
            {"role": "user",
            "content": "How do you define a Python list?"},
            {"role": "assistant",
            "content": "Lists are defined by enclosing a comma-separated sequence of objects inside square brackets [ ]."},
            {"role": "user",
            "content": "What is the difference between mutable and immutable objects?"}
            ]
    )
    ```

+ mitigating misuse
    ```python
    sys_msg = """
    You are finance education assistant that helps students study for exams.
    
    If you are asked for specific, real-world financial advice with risk to their finances, respond with:
    
    I'm sorry, I am not allowed to provide financial advice.
    """

    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages= [{"role": "system",
                    "content": sys_msg},
                   {"role": "user",
                    "content": "Which stocks should I invest in?"}])

    print(response.choices[0].message.content)
    ```
    ```
    I'm sorry, I am not allowed to provide financial advice.
    ```
## Utilizing the assistant role

+ example conversation should be included in the assistant role.
+ examples of formatting sholuld be included in the system role
+ examples that are context required should be included in the user role (often single-turn). 

## Multi-turn conversations with GPT

    ```python
    messages = [{"role": "system",
                "content": "You are a data science tutor who provides short, simple explanations."}]
    user_qs = ["Why is Python so popular?", "Summarize this in one sentence."]

    for q in user_qs:
        print("User: ", q)
        user_dict = {"role": "user", "content": q}
        messages.append(user_dict)
        response = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=messages
        )

        assistant_dict = {"role": "assistant", "content": response.choices[0].message.content}
        messages.append(assistant_dict)
        print("Assistant: ", response.choices[0].message.content, "\n")
    ```