# Bedrock

1. [Introduction](#1)
2. [Text Generation](#2)
    1. [Text Generation Parameters](#3)
       1. [Max Token Generation Length](#4)
       2. [Temperature](#5)
       3. [Top P](#6)
       4. [Stop or Finish Sequence](#7)
    2. [Text models with Bedrock](#8)
       1. [How to use batch inference vs. On-demand](#9)
3. [Reference](#10) 

<a name="1"></a>
## Introduction 

- Amazon Bedrock is an advanced, fully managed service designed to streamline the integration and application of generative artificial intelligence (AI) in various projects and applications.
- This service stands out by providing access to a selection of top-tier foundation models (FMs) from renowned AI companies, including
     - AI21 Labs,
     - Anthropic,
     - Cohere,
     - Meta,
     - Stability AI, and
     - Amazon itself.
  
 All these models are accessible via Python using Amazon’s **Boto3 API.**
 
- Currently, there are two main model types:
    - Text Generation: Give some input text, generate output text, like explanations, summaries, etc.
    - Image Generation: Given a text description, generate an image, can also use base images
- There are other new and upcoming services, such as Knowledge Databases using RAG - Retrieval Augmented Generation and **Agents**, allowing LLMs to call **Lambda functions**.
- Inside of the AWS Amazon Bedrock console you also get access to a **playground** for quick testing.
- Keep in mind not all configurations are available for all models. They may be slightly different, and you have the playground accessibility to just play around with these models. And most importantly, you do need to request model access. Remember, you go to the Model Access page and then click on Manage Model Access. And then you're going to select which models you want access to.

<a name="2"></a>
## Text Generation

<a name="3"></a>
### Text Generation Parameters

Not all parameters are shared between models (not all models have a frequency penalty) and the JSON configuration and naming schema is different between models (e.g. **topP vs top_p).**

Some fundamental parameters that most models have:

<a name="4"></a>
#### Max Token Generation Length

This parameter sets a limit on the number of tokens the model can generate in response to a prompt. For instance, if the max token generation length is set to 500, the model will not produce a response that exceeds 500 tokens. A shorter max length can lead to more focused and concise answers, ideal for straightforward questions. In contrast, a longer max length allows the model to provide more detailed and comprehensive responses. 

<a name="5"></a>
#### Temperature

The 'Temperature' parameter in LLMs controls the randomness or creativity in the model's responses. A lower temperature (closer to 0) makes the model's responses more deterministic and predictable, often sticking closely to the most likely output based on its training. Conversely, a higher temperature (closer to 1) increases randomness, leading to more varied and sometimes more creative or unexpected outputs. **At high temperatures, there's also a higher risk of generating nonsensical, irrelevant, or off-topic content.** The ideal temperature setting depends on the specific application and goals. For tasks requiring high accuracy and reliability, such as factual reporting or technical explanations, a lower temperature is preferred.  For creative tasks like storytelling or poetry pick a higher temperature.


<a name="6"></a>
#### Top P

Top P sampling is a method used to guide the generation of text by the model. **Unlike traditional sampling methods that consider the entire set of possible next tokens, Top P sampling focuses on a subset.** This subset consists of the most probable next tokens whose **cumulative probability exceeds the threshold P**. For example, if P is set to 0.9, the model selects from the smallest set of tokens that together add up to at least 90% of the total probability of the next token. Usually developers only change temperature or top P, but typically **not both.** However in the case where you want very repeatable outputs, setting a 0 temperature and a very low top P is a good approach to get consistent results (I don't think technically the models will allow you to set it to zero, so you can set it as like 0.001 or something very small. And that's a good approach to try to get consistent results.)


<a name="7"></a>
#### Stop or Finish Sequence

You can also specify a stop sequence for an LLM. Most models accept a list of potential stop sequences. When the model encounters the specified stop sequence in its generated text, it ceases to produce more tokens, effectively ending the response. This can be very useful when you have a specific structure of input or output. For example, when composing an email, the stop sequence could be "Sincerely," which is a common closing remark in emails. Or for code generation, you can have the return keyword be a stop sequence.

Don’t forget to try general prompt engineering best practices (e.g. be clear in your request, be detailed, show examples) in addition to adjusting parameters.

<a name="8"></a>
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

  
#### Custom Models
You may be wondering why we didn't show a video of fine-tuning a custom model!

The main reason is because AWS is extremely focused on reliability with Amazon Bedrock, meaning they want to make sure your fine-tuned model has the same uptime and reliability as any other model. This means to create a custom fine-tuned model, you are required to purchase Provisioned Throughput, basically a payment for a minimum amount of usage in order to reserve the compute your model will use. The minimum amount  of provisioned throughput you can purchase on Amazon Bedrock is approximately $500 per month.

If this seems like the right use case for you and you are aware of the financial costs, here is a guide on how to set up and create a custom model:

To customize a model, you create a model customization job, specifically a Fine-Tuning job (creates a Fine-tuned model model) or Continued Pre-training job (creates a Continued Pre-training model model).

First, you upload a training dataset and, optionally, a validation dataset to Amazon S3 and provide the Amazon S3 bucket path to the model customization job. Follow the instructions at Prepare the datasets to set up your dataset. Each model has default hyperparameter values for model training. If necessary, control the training process by making changes to hyperparameter values. For more information, see Using the console.To customize a model through the API, see Using the API.

After you complete a model customization job, you can purchase Provisioned Throughput (see Provisioned Throughput) for the customized model so that you can use the model for inference using the InvokeModel or InvokeModelWithResponseStream API operations. For example code, see Run inference using Provisioned Throughput. You can also use the text playground (see Text playground).

here is inference parameters for Llama 

    {
        "prompt": string,
        "temperature": float,
        "top_p": float,
        "max_gen_len": int
    }

<a name="9"></a>
### How to use batch inference vs. On-demand

Here is a link with more details on batch inference on Bedrock <a href="https://docs.aws.amazon.com/bedrock/latest/userguide/batch-inference-supported.html">Amazon Bedrock - Batch Inrefence</a>

Some key points batch inference job:

- Data needs to be prepared in a **JSONL** file format 
- I do need to upload the input data to S3 and also outputs need to be saved in S3
- I can run this batch job either in Bedrock console or API
- I need to use create_model_invocation_job and not invoke_model

<a name="10"></a>
References <a href="https://www.udemy.com/course/amazon-bedrock-learn-ai-on-aws-with-python/">Amazon Bedrock - Learn AI on AWS with Python!</a>
