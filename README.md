# Rekognition-training-pipeline

This application creates a workflow that creates an Amazon Rekognition project, a training and a test dataset by copying an existing dataset from another rekognition project, trains a custom model and deploys the model if the model's accuracy is > 0.85. We train the Rekognition custom model using the training dataset and evaluate the trained model on a test dataset. The model is finally deployed if the accuracy is > 0.85. The whole workflow is automated using AWS Step Functions.

AWS Step Functions lets you coordinate multiple AWS services into serverless workflows so you can build and update apps quickly. Using Step Functions, you can design and run workflows that stitch together services, such as AWS Lambda, AWS Fargate, and Amazon SageMaker, into feature-rich applications.

Amazon Rekognition is an AI service that can enable your business and development teams to solve computer vision needs without any ML skills. You can quickly add pre-trained models or train a custom computer vision model and perform inference using the models using Rekognition APIs within your application.

The application uses several AWS resources, including Step Functions state machines and Rekogntion APIs. These resources are defined in the `template.yaml` file in this project. You can update the template to add AWS resources through the same deployment process that updates your application code.

This project contains source code and supporting files for the worflow defined as a serverless application that you can deploy with the [SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html). It includes the following files and folders:

- statemachine - Definition for the state machine that orchestrates the rekognition training workflow.
- template.yaml - A template that defines the application's AWS resources.

## Workflow architecture

This workflow will be deployed to Step functions in the selected region. You can go to the step functions console and access the workflow.

![Architecture](/assets/workflow.jpg)

## Prerequsites

Before deploying the workflow, we need to create the existing training and validation datasets. To do that,

- First, create an Amazon Rekognition project. You can follow the steps [here](https://docs.aws.amazon.com/rekognition/latest/customlabels-dg/mp-create-project.html) to create the project.
- Then, create the training and validation datasets. You can follow the steps [here](https://docs.aws.amazon.com/rekognition/latest/customlabels-dg/creating-datasets.html) to create the datasets.
- Finally, install AWS SAM CLI. You can follow steps [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html).

## Deploy the sample application

To build and deploy your application for the first time, run the following in your shell:

```bash
sam build
sam deploy --guided
```

The first command will build the source of your application. The second command will package and deploy your application to AWS, with a series of prompts:

- **Stack Name**: The name of the stack to deploy to CloudFormation. This should be unique to your account and region, and a good starting point would be something matching your project name.
- **AWS Region**: The AWS region you want to deploy your app to.
- **Parameter TrainingDatasetArnToCopyFrom**: The training dataset arn to copy training dataset from. For example: arn:aws:rekognition:<aws-region>:<account_num>:project/<project-name>/dataset/train/<dataset-unique-number>
- **Parameter TestDatasetArnToCopyFrom**: The test dataset arn to copy test dataset from. For example arn:aws:rekognition:<aws-region>:<account_num>:project/<project-name>/dataset/test/<dataset-unique-number>]:
- **Parameter OutputBucketName**: The bucketname where Rekonition should copy model artifacts to
- **Confirm changes before deploy**: If set to yes, any change sets will be shown to you before execution for manual review. If set to no, the AWS SAM CLI will automatically deploy application changes.
- **Allow SAM CLI IAM role creation**: Many AWS SAM templates, including this example, create AWS IAM roles required for the AWS Lambda function(s) included to access AWS services. By default, these are scoped down to minimum required permissions. To deploy an AWS CloudFormation stack which creates or modifies IAM roles, the `CAPABILITY_IAM` value for `capabilities` must be provided. If permission isn't provided through this prompt, to deploy this example you must explicitly pass `--capabilities CAPABILITY_IAM` to the `sam deploy` command.
- **Save arguments to samconfig.toml**: If set to yes, your choices will be saved to a configuration file inside the project, so that in the future you can just re-run `sam deploy` without parameters to deploy changes to your application.

## Test the workflow

To test the workflow and see the Rekogntion model trained, start an execution for the step functions workflow.

1. Go to step functions console and access the deployed workflow.
2. Click on start execution.

The workflow could take anywhere from a few mins to few hrs complete. If the model passed the evaluation criteria, an endpoint for the model should be created in Rekognition.

## Cleanup

To delete the sample application that you created, use the AWS CLI. Assuming you used your project name for the stack name, you can run the following:

```bash
aws sam delete --stack-name <stack-name>
```

To delete the Amazon Rekognition Custom Labels model, navigate to the Amazon Rekognition console and follow [these](https://docs.aws.amazon.com/rekognition/latest/customlabels-dg/tm-delete-model.html#tm-delete-model-console) instructions. Alternatively, you can use the AWS SDK to [delete](https://docs.aws.amazon.com/rekognition/latest/customlabels-dg/tm-delete-model.html#tm-delete-model-sdk) the model.
