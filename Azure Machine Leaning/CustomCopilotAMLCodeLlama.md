# A Step-by-Step Guide to Build a Custom Copilot Powered by Code Llama Model #

Custom Copilot is an application that employs state-of-the-art AI and large language models to assist users with complex cognitive tasks. It allows organizations to construct bespoke intelligent, conversational app experiences for their users, tailored to specific domains and unique pain points. Essentially, it’s like having your very own AI copilot to help navigate the complexities of tasks and interactions!

Lets build CodeBuddy a custom copilot powered by Code Llama, a leading AI platform for code generation and analysis. the custom Copilot integrates Azure AI Content Safety, a feature that detects and prevents harmful or inappropriate content. the copilot helps you with code completion and Infilling.

## High-level Architecture

![image](https://github.com/mahes-a/2023/assets/120069348/c91513ff-ad45-43a2-805e-bfcd17eb24d7)

## Custom Copilot Teams channel Screen Grab

![image](https://github.com/mahes-a/2023/assets/120069348/368fa3ce-57ce-44c4-9f28-3e5c3bcffd84)

## Prerequisites

- Azure subscription with Azure Machine Learning resource
- Deploying Llama 2 models require GPU compute of V100 / A100 SKUs. You can view and request AzureML compute quota [here](https://ml.azure.com/quota).
- Copilot Studio , Can be accessed from [here](https://copilotstudio.microsoft.com/).
- Teams for work or school , You can download from [here](https://go.microsoft.com/fwlink/?linkid=2187327&Lmsrc=groupChatMarketingPageWeb&Cmpid=directDownloadWin64&clcid=0x409&culture=en-us&country=us)

## Things to Note 

- The Model catalog in AML with Generative AI Models(like Code Llama) is in Preview

- Please note that this tutorial is intended for explorative and illustrative purposes only. It is meant to inspire ideas and should not be taken as prescriptive advice. Any implementation of the techniques described in this tutorial as part of your application should be thoroughly validated and evaluated to ensure accuracy, validity, compatibility with your specific use case and technical environment.


- If you aren't going use the real time Llama  deployment any more, Please delete the endpoint by following the article [here](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-deploy-online-endpoints?view=azureml-api-2&tabs=azure-cli#delete-the-endpoint-and-the-deployment)
