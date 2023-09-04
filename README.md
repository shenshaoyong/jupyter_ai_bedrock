# jupyter_ai_bedrock
how to configure Amazon Bedrock and Amazon SageMaker Endpoint in Jupyter AI

# Setting of Jupyter AI + Bedrock(on Mac)
```
#1. Open terminal, install bedrock specific version boto3 SDK,langchain
curl -sS https://d2eo22ngex1n9g.cloudfront.net/Documentation/SDK/bedrock-python-sdk.zip > sdk.zip
sudo yum install unzip -y
unzip sdk.zip -d sdk 
pip install --no-build-isolation --force-reinstall ./sdk/awscli-*-py3-none-any.whl  ./sdk/boto3-*-py3-none-any.whl ./sdk/botocore-*-py3-none-any.whl
pip install --quiet langchain==0.0.249
#pip install 'jupyter-ai>=1.0,<2.0' # If you use JupyterLab 3pip install jupyter-ai # If you use JupyterLab 4

#2. change the default token count to 2048
vi ~/anaconda3/lib/python3.11/site-packages/langchain/llms/bedrock.py
change  this line:  input_body["max_tokens_to_sample"] = 2048

#3. aws configure default profile, make sure the aksk has enough permissions(BedrockFullAccess)
aws configure

#4.run %%ai in *.ipynb file on ec2/local machine [also can run in VSCODE] instead of SageMaker notebook instance / SageMaker Studio
%load_ext jupyter_ai
%%ai bedrock:anthropic.claude-v2
Write something about Amazon
```


# Setting of Jupyter AI+Llama2 in SageMakerEndpoint(on Mac)
```
#1. Open terminal, install bedrock specific version boto3 SDK,langchain
curl -sS https://d2eo22ngex1n9g.cloudfront.net/Documentation/SDK/bedrock-python-sdk.zip > sdk.zip
sudo yum install unzip -y
unzip sdk.zip -d sdk 
pip install --no-build-isolation --force-reinstall ./sdk/awscli-*-py3-none-any.whl  ./sdk/boto3-*-py3-none-any.whl ./sdk/botocore-*-py3-none-any.whl
pip install --quiet langchain==0.0.249
#pip install 'jupyter-ai>=1.0,<2.0' # If you use JupyterLab 3pip install jupyter-ai # If you use JupyterLab 4

#2. change the default token count to 1024
vi ~/anaconda3/lib/python3.11/site-packages/langchain/llms/sagemaker_endpoint.py
Insert below lines after body = self.content_handler.transform_input(prompt, _model_kwargs)
        parameters={"max_new_tokens": 1024, "top_p": 0.9, "temperature": 0.6, "return_full_text": True}
        t = json.loads(body)
        t["parameters"] = parameters
        body = json.dumps(t)
Insert the line CustomAttributes='accept_eula=true’,  between Accept=accepts, and **_endpoint_kwargs,

#3. aws configure default profile,  make sure the aksk has enough permissions(SageMakerFullAccess)
aws configure

#4.run %%ai in *.ipynb file on ec2 instead of SageMaker notebook instance / SageMaker Studio [also can run in VSCODE]
%load_ext jupyter_ai
%%ai sagemaker-endpoint:jumpstart-dft-meta-textgeneration-llama-2-7b --region-name=us-east-1 --request-schema={"inputs":"<prompt>"} --response-path=[0]['generation']
write somthing on Humor
```
