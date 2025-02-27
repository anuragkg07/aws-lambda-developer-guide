# Deploy Python Lambda functions with container images<a name="python-image"></a>

**Note**  
End of support for the Python 2\.7 runtime starts on July 15, 2021\. For more information, see [Runtime support policy](runtime-support-policy.md)\.

You can deploy your Lambda function code as a [container image](images-create.md)\. AWS provides the following resources to help you build a container image for your Python function:
+ AWS base images for Lambda

  These base images are preloaded with a language runtime and other components that are required to run the image on Lambda\. AWS provides a Dockerfile for each of the base images to help with building your container image\.
+ Open\-source runtime interface clients

  If you use a community or private enterprise base image, add a runtime interface client to the base image to make it compatible with Lambda\.

The workflow for a function defined as a container image includes these steps:

1. Build your container image using the resources listed in this topic\.

1. Upload the image to your Amazon ECR container registry\. See steps 7\-9 in [Create image](images-create.md#images-create-from-base)\.

1. [Create](configuration-images.md) the Lambda function and deploy the image\.

**Topics**
+ [AWS base images for Python](#python-image-base)
+ [Python runtime interface clients](#python-image-clients)
+ [Deploying Python with an AWS base image](#python-image-create)
+ [Create a Python image from an alternative base image](#python-image-create-alt)

## AWS base images for Python<a name="python-image-base"></a>

AWS provides the following base images for Python:


| Tags | Runtime | Operating system | Dockerfile | 
| --- | --- | --- | --- | 
| 3, 3\.8 | Python 3\.8 | Amazon Linux 2 | [Dockerfile for Python 3\.8 on GitHub](https://github.com/aws/aws-lambda-base-images/blob/python3.8/Dockerfile.python3.8) | 
| 3\.7 | Python 3\.7 | Amazon Linux 2018\.03 | [Dockerfile for Python 3\.7 on GitHub](https://github.com/aws/aws-lambda-base-images/blob/python3.7/Dockerfile.python3.7) | 
| 3\.6 | Python 3\.6 | Amazon Linux 2018\.03 | [Dockerfile for Python 3\.6 on GitHub](https://github.com/aws/aws-lambda-base-images/blob/python3.6/Dockerfile.python3.6) | 
| 2, 2\.7 | Python 2\.7 | Amazon Linux 2018\.03 | [Dockerfile for Python 2\.7 on GitHub](https://github.com/aws/aws-lambda-base-images/blob/python2.7/Dockerfile.python2.7) | 

Docker Hub repository: amazon/aws\-lambda\-python

Amazon ECR repository: gallery\.ecr\.aws/lambda/python

## Python runtime interface clients<a name="python-image-clients"></a>

Install the runtime interface client for Python using the pip package manager:

```
pip install awslambdaric
```

For package details, see [Lambda RIC](https://pypi.org/project/awslambdaric) on the Python Package Index \(PyPI\) website\.

You can also download the [Python runtime interface client](https://github.com/aws/aws-lambda-python-runtime-interface-client/) from GitHub\.

## Deploying Python with an AWS base image<a name="python-image-create"></a>

When you build a container image for Python using an AWS base image, you only need to copy the python app to the container and install any dependencies\. 

**To build and deploy a Python function with the `python:3.8` base image\.**

1. On your local machine, create a project directory for your new function\.

1. In your project directory, add a file named `app.py` containing your function code\. The following example shows a simple Python handler\. 

   ```
   import sys
   def handler(event, context):
       return 'Hello from AWS Lambda using Python' + sys.version + '!'
   ```

1. In your project directory, add a file named `requirements.txt`\. List each required library as a separate line in this file\. Leave the file empty if there are no dependencies\.

1. Use a text editor to create a Dockerfile in your project directory\. The following example shows the Dockerfile for the handler that you created in the previous step\. Install any dependencies under the $\{LAMBDA\_TASK\_ROOT\} directory alongside the function handler to ensure that the Lambda runtime can locate them when the function is invoked\.

   ```
   FROM public.ecr.aws/lambda/python:3.8
   
   # Copy function code
   COPY app.py ${LAMBDA_TASK_ROOT}
   
   # Install the function's dependencies using file requirements.txt
   # from your project folder.
   
   COPY requirements.txt  .
   RUN  pip3 install -r requirements.txt --target "${LAMBDA_TASK_ROOT}"
   
   # Set the CMD to your handler (could also be done as a parameter override outside of the Dockerfile)
   CMD [ "app.handler" ]
   ```

1. To create the container image, follow steps 4 through 7 in [Create an image from an AWS base image for Lambda](images-create.md#images-create-from-base)\.

## Create a Python image from an alternative base image<a name="python-image-create-alt"></a>

For an example of how to create a Python image from an Alpine base image, see [Container image support for Lambda](http://aws.amazon.com/blogs/aws/new-for-aws-lambda-container-image-support/) on the AWS Blog\.