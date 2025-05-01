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
- **Top P**: Top P sampling is a method used to guide the generation of text by the model. **Unlike traditional sampling methods that consider the entire set of possible next tokens, Top P sampling focuses on a subset.** This subset consists of the most probable next tokens whose **cumulative probability exceeds the threshold P**. For example, if P is set to 0.9, the model selects from the smallest set of tokens that together add up to at least 90% of the total probability of the next token. Usually developers only change temperature or top P, but typically **not both.** However in the case where you want very repeatable outputs, setting a 0 temperature and a very low top P is a good approach to get consistent results (I don't think technically the models will allow you to set it to zero, so you can set it as like 0.001 or something very small. And that's a good approach to try to get consistent results.)
- **Stop or Finish Sequence**: You can also specify a stop sequence for an LLM. Most models accept a list of potential stop sequences. When the model encounters the specified stop sequence in its generated text, it ceases to produce more tokens, effectively ending the response. This can be very useful when you have a specific structure of input or output. For example, when composing an email, the stop sequence could be "Sincerely," which is a common closing remark in emails. Or for code generation, you can have the return keyword be a stop sequence.

Don’t forget to try general prompt engineering best practices (e.g. be clear in your request, be detailed, show examples) in addition to adjusting parameters.

### Text models with Bedrock

- In order to **invoke the model** I need to access the **bedrock runtime service.** So something to keep in mind is slightly different than other boto services is the service name. **Bedrock** is basically just information about Amazon bedrock. Like what models are available, what models do I have access to, what services are available, etc. when I actually want to run one of these models, the service name is **bedrock-runtime.**
- Now, something to note is when you actually invoke these models, it expects a JSON request body. And depending on the model the actual configuration of that JSON may be slightly different, which looks like the following:

          {
              "inputText": string,
              "testGenerationConfig":{
                  "temperature": float,
                  "topP": float,
                  "maxTokenCount": int,
                  "stopSequences": [string]
            }
          }

I can get the inference parameters for all the models here at: https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters-deepseek.html 

**key takeaways**
- to remember that both the arguments that you're going to pass in are going to be different for each model.
- And inside of this response body, it's going to be different for each model.
- Generation was not the key we used for the Titan model, but it is for the Llama2 model.
- And you do have to refer to the models' inference parameters in the documentation/link above.

  
