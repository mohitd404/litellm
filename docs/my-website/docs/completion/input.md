import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Input Params

## Common Params 
LiteLLM accepts and translates the [OpenAI Chat Completion params](https://platform.openai.com/docs/api-reference/chat/create) across all providers. 

### usage
```python
import litellm

# set env variables
os.environ["OPENAI_API_KEY"] = "your-openai-key"

## SET MAX TOKENS - via completion() 
response = litellm.completion(
            model="gpt-3.5-turbo",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

print(response)
```

### translated OpenAI params
This is a list of openai params we translate across providers.

This list is constantly being updated.

| Provider | temperature | max_tokens | top_p | stream | stop | n | presence_penalty | frequency_penalty | functions | function_call |
|---|---|---|---|---|---|---|---|---|---|---|
|Anthropic| ✅ | ✅ | ✅ | ✅ | ✅ |  |  |   |  |   |
|OpenAI| ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
|Replicate | ✅ | ✅ | ✅ | ✅ | ✅ | |  |   |  |   |
|Cohere| ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |   |   |
|Huggingface| ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |  |  |   |    |
|Openrouter| ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
|AI21| ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |  |   |
|VertexAI| ✅ | ✅ |  | ✅ |  |  |  |  |  |   |
|Bedrock| ✅ | ✅ | ✅ | ✅ | ✅ |  |  |   |  |   |
|Sagemaker| ✅ | ✅ |  | ✅ |  |  |  |  |  |   |
|TogetherAI| ✅ | ✅ | ✅ | ✅ | ✅ |  |  |   |  |   |
|AlephAlpha| ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |  |   |  |   |
|Palm| ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |  |  |  |   |
|NLP Cloud| ✅ | ✅ | ✅ | ✅ | ✅ | |  |  |  |   |
|Petals| ✅ | ✅ |  | ✅ | |  |   |  |  |   |
|Ollama| ✅ | ✅ | ✅ | ✅ | ✅ |  |   | ✅ |  |   |n

:::note

By default, LiteLLM raises an exception if the openai param being passed in isn't supported. 

To drop the param instead, set `litellm.drop_params = True`.

**For function calling:**

Add to prompt for non-openai models, set: `litellm.add_function_to_prompt = True`. 
::: 

## Provider-specific Params
Providers might offer params not supported by OpenAI (e.g. top_k). You can pass those in 2 ways: 
- via completion(): We'll pass the non-openai param, straight to the provider as part of the request body.
    - e.g. `completion(model="claude-instant-1", top_k=3)`
- via provider-specific config variable (e.g. `litellm.OpenAIConfig()`). 

<Tabs>
<TabItem value="openai" label="OpenAI">

```python
import litellm, os

# set env variables
os.environ["OPENAI_API_KEY"] = "your-openai-key"

## SET MAX TOKENS - via completion() 
response_1 = litellm.completion(
            model="gpt-3.5-turbo",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.OpenAIConfig(max_tokens=10)

response_2 = litellm.completion(
            model="gpt-3.5-turbo",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>
<TabItem value="openai-text" label="OpenAI Text Completion">

```python
import litellm, os

# set env variables
os.environ["OPENAI_API_KEY"] = "your-openai-key"


## SET MAX TOKENS - via completion() 
response_1 = litellm.completion(
            model="text-davinci-003",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.OpenAITextCompletionConfig(max_tokens=10)
response_2 = litellm.completion(
            model="text-davinci-003",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>
<TabItem value="azure-openai" label="Azure OpenAI">

```python
import litellm, os

# set env variables
os.environ["AZURE_API_BASE"] = "your-azure-api-base"
os.environ["AZURE_API_TYPE"] = "azure" # [OPTIONAL] 
os.environ["AZURE_API_VERSION"] = "2023-07-01-preview" # [OPTIONAL]

## SET MAX TOKENS - via completion() 
response_1 = litellm.completion(
            model="azure/chatgpt-v-2",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.AzureOpenAIConfig(max_tokens=10)
response_2 = litellm.completion(
            model="azure/chatgpt-v-2",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>
<TabItem value="anthropic" label="Anthropic">

```python
import litellm, os 

# set env variables
os.environ["ANTHROPIC_API_KEY"] = "your-anthropic-key"

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="claude-instant-1",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.AnthropicConfig(max_tokens_to_sample=200)
response_2 = litellm.completion(
            model="claude-instant-1",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>

<TabItem value="huggingface" label="Huggingface">

```python
import litellm, os 

# set env variables
os.environ["HUGGINGFACE_API_KEY"] = "your-huggingface-key" #[OPTIONAL]

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="huggingface/mistralai/Mistral-7B-Instruct-v0.1",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            api_base="https://your-huggingface-api-endpoint",
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.HuggingfaceConfig(max_new_tokens=200)
response_2 = litellm.completion(
            model="huggingface/mistralai/Mistral-7B-Instruct-v0.1",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            api_base="https://your-huggingface-api-endpoint"
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>

<TabItem value="together_ai" label="TogetherAI">


```python
import litellm, os 

# set env variables
os.environ["TOGETHERAI_API_KEY"] = "your-togetherai-key" 

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="together_ai/togethercomputer/llama-2-70b-chat",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.TogetherAIConfig(max_tokens_to_sample=200)
response_2 = litellm.completion(
            model="together_ai/togethercomputer/llama-2-70b-chat",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>

<TabItem value="ollama" label="Ollama">

```python
import litellm, os 

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="ollama/llama2",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.OllamConfig(num_predict=200)
response_2 = litellm.completion(
            model="ollama/llama2",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>

<TabItem value="replicate" label="Replicate">

```python
import litellm, os 

# set env variables
os.environ["REPLICATE_API_KEY"] = "your-replicate-key" 

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="replicate/meta/llama-2-70b-chat:02e509c789964a7ea8736978a43525956ef40397be9033abf9fd2badfe68c9e3",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.ReplicateConfig(max_new_tokens=200)
response_2 = litellm.completion(
            model="replicate/meta/llama-2-70b-chat:02e509c789964a7ea8736978a43525956ef40397be9033abf9fd2badfe68c9e3",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>

<TabItem value="petals" label="Petals">


```python
import litellm

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="petals/petals-team/StableBeluga2",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            api_base="https://chat.petals.dev/api/v1/generate",
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.PetalsConfig(max_new_tokens=10)
response_2 = litellm.completion(
            model="petals/petals-team/StableBeluga2",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            api_base="https://chat.petals.dev/api/v1/generate",
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>

<TabItem value="palm" label="Palm">

```python
import litellm, os 

# set env variables
os.environ["PALM_API_KEY"] = "your-palm-key"  

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="palm/chat-bison",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.PalmConfig(maxOutputTokens=10)
response_2 = litellm.completion(
            model="palm/chat-bison",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```
</TabItem>

<TabItem value="ai21" label="AI21">

```python
import litellm, os 

# set env variables
os.environ["AI21_API_KEY"] = "your-ai21-key"  

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="j2-mid",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.AI21Config(maxOutputTokens=10)
response_2 = litellm.completion(
            model="j2-mid",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>

<TabItem value="cohere" label="Cohere">

```python
import litellm, os 

# set env variables
os.environ["COHERE_API_KEY"] = "your-cohere-key"   

## SET MAX TOKENS - via completion()
response_1 = litellm.completion(
            model="command-nightly",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
            max_tokens=10
        )

response_1_text = response_1.choices[0].message.content

## SET MAX TOKENS - via config
litellm.CohereConfig(max_tokens=200)
response_2 = litellm.completion(
            model="command-nightly",
            messages=[{ "content": "Hello, how are you?","role": "user"}],
        )

response_2_text = response_2.choices[0].message.content

## TEST OUTPUT
assert len(response_2_text) > len(response_1_text)
```

</TabItem>

</Tabs>


[**Check out the tutorial!**](../tutorials/provider_specific_params.md)

## Input - Request Body
# Request Body

### Required Fields

- `model`: *string* - ID of the model to use. Refer to the model endpoint compatibility table for details on which models work with the Chat API.
  
- `messages`: *array* - A list of messages comprising the conversation so far.

#### Properties of `messages`
*Note* - Each message in the array contains the following properties:

- `role`: *string* - The role of the message's author. Roles can be: system, user, assistant, or function.

- `content`: *string or null* - The contents of the message. It is required for all messages, but may be null for assistant messages with function calls.

- `name`: *string (optional)* - The name of the author of the message. It is required if the role is "function". The name should match the name of the function represented in the content. It can contain characters (a-z, A-Z, 0-9), and underscores, with a maximum length of 64 characters.

- `function_call`: *object (optional)* - The name and arguments of a function that should be called, as generated by the model.



### Optional Fields

- `functions`: *array* - A list of functions that the model may use to generate JSON inputs. Each function should have the following properties:

    - `name`: *string* - The name of the function to be called. It should contain a-z, A-Z, 0-9, underscores and dashes, with a maximum length of 64 characters.
    
    - `description`: *string (optional)* - A description explaining what the function does. It helps the model to decide when and how to call the function.
    
    - `parameters`: *object* - The parameters that the function accepts, described as a JSON Schema object.
    
    - `function_call`: *string or object (optional)* - Controls how the model responds to function calls.

- `temperature`: *number or null (optional)* - The sampling temperature to be used, between 0 and 2. Higher values like 0.8 produce more random outputs, while lower values like 0.2 make outputs more focused and deterministic. 

- `top_p`: *number or null (optional)* - An alternative to sampling with temperature. It instructs the model to consider the results of the tokens with top_p probability. For example, 0.1 means only the tokens comprising the top 10% probability mass are considered.

- `n`: *integer or null (optional)* - The number of chat completion choices to generate for each input message.

- `stream`: *boolean or null (optional)* - If set to true, it sends partial message deltas. Tokens will be sent as they become available, with the stream terminated by a [DONE] message.

- `stop`: *string/ array/ null (optional)* - Up to 4 sequences where the API will stop generating further tokens.

- `max_tokens`: *integer (optional)* - The maximum number of tokens to generate in the chat completion.

- `presence_penalty`: *number or null (optional)* - It is used to penalize new tokens based on their existence in the text so far.

- `frequency_penalty`: *number or null (optional)* - It is used to penalize new tokens based on their frequency in the text so far.

- `logit_bias`: *map (optional)* - Used to modify the probability of specific tokens appearing in the completion.

- `user`: *string (optional)* - A unique identifier representing your end-user. This can help OpenAI to monitor and detect abuse.