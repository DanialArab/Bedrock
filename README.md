# Bedrock

Ref: https://www.udemy.com/course/amazon-bedrock-learn-ai-on-aws-with-python/

## Intro

- Amazon Bedrock is an advanced, fully managed service designed to streamline the integration and application of generative artificial intelligence (AI) in various projects and applications.
- This service stands out by providing access to a selection of top-tier foundation models (FMs) from renowned AI companies, including AI21 Labs, Anthropic, Cohere, Meta, Stability AI, and Amazon itself. All these models are accessible via Python using Amazon’s Boto3 API.
- Currently, there are two main model types:
    - Text Generation: Give some input text, generate output text, like explanations, summaries, etc.
    - Image Generation: Given a text description, generate an image, can also use base images
- There are other new and upcoming services, such as Knowledge Databases using RAG - Retrieval Augmented Generation and **Agents**, allowing LLMs to call **Lambda functions**.
- Inside of the AWS Amazon Bedrock console you also get access to a **playground** for quick testing.
- Keep in mind not all configurations are available for all models. They may be slightly different, and you have the playground accessibility to just play around with these models. And most importantly, you do need to request model access. Remember, you go to the Model Access page and then click on Manage Model Access. And then you're going to select which models you want access to.

## Text Generation

### Parameters

Not all parameters are shared between models (not all models have a frequency penalty) and the JSON configuration and naming schema is different between models (e.g. topP vs top_p).

Some fundamental parameters that most models have:

- **Max Token Generation Length**: This parameter sets a limit on the number of tokens the model can generate in response to a prompt. For instance, if the max token generation length is set to 500, the model will not produce a response that exceeds 500 tokens. A shorter max length can lead to more focused and concise answers, ideal for straightforward questions. In contrast, a longer max length allows the model to provide more detailed and comprehensive responses. 
- **Temperature**: The 'Temperature' parameter in LLMs controls the randomness or creativity in the model's responses. A lower temperature (closer to 0) makes the model's responses more deterministic and predictable, often sticking closely to the most likely output based on its training. Conversely, a higher temperature (closer to 1) increases randomness, leading to more varied and sometimes more creative or unexpected outputs. **At high temperatures, there's also a higher risk of generating nonsensical, irrelevant, or off-topic content.** The ideal temperature setting depends on the specific application and goals. For tasks requiring high accuracy and reliability, such as factual reporting or technical explanations, a lower temperature is preferred.  For creative tasks like storytelling or poetry pick a higher temperature.
- **Top P**:
- **Stop or Finish Sequence**: 
