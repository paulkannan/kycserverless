# KYC Serverless Architecture
The pattern creates a S3, Lambda function, Step Functions workflow with Lambda, Textract ,SQS,SNS, Log group and the IAM resources required to run the application. The pattern is an event based serverless architecture.
![Screenshot 2023-07-09 at 11 19 45 AM](https://github.com/paulkannan/kycserverless/assets/46925641/efb22a04-05e1-43d0-805f-91e6ff34d51a)

Workflow:

  - User uploads a valid KYC pdf (aadhaar) to S3
  - Put Event in S3 triggers Lambda (s3eventnotification) which triggers StateMachine (ExtractAadharPhoneStep) with S3 name and file as input
  - StateMachine triggers a Lambda(ProcessPDF) which will start a Textract Job, pass the pdf, extract the data and check for Aadhaar Number using regex. If Aadhaar Number is found, it is passed as input 
    to next state (ExtractData), if not an error is passed to next state 
  - On receipt of input from ProcessPDF, if it is valid, ExtractData will pass Aadhaar Number to Aadhaar API Gateway for validation and get back response. If response is 200, then it will pass KYC 
    validation success to SQS (ValidatedQueue) else will pass an error message to SQS
  - SNS (NotifyUser) is subscribed to SQS to send notifications to end users
  - The Step Function workflow showcasing the different States.




![stepfunctions_graph](https://github.com/paulkannan/kycserverless/assets/46925641/406206ae-d1d4-4fd0-a722-341cb0ec89ff)

To Deploy the code using Terraform:

1. Clone the Repository: Start by cloning the GitHub repository to your local machine. Use the git clone command to clone the repository: **git clone <repository-url>**
2. Navigate to the Repository: Change into the cloned repository directory:  **cd <repository-directory>**
3. Review the Terraform Code: Explore the contents of the repository and locate the Terraform code files (e.g., .tf files) that define the infrastructure.
4. Install Terraform: Ensure that Terraform is installed on your local machine. You can download Terraform from the official website (https://www.terraform.io/downloads.html) and follow the installation instructions for your operating system.
5. Initialize the Terraform Configuration: Run **terraform init** in the repository directory to initialize the Terraform configuration. This command downloads the necessary provider plugins and sets up the working directory.Review Variables: Check if there are any variables defined in the Terraform code or provided through variable files (*.tfvars files). Modify the values of the variables as required.
6. Plan the Infrastructure Changes: Run **terraform plan** to preview the infrastructure changes that Terraform will make. This step provides an overview of the resources that will be created, modified, or destroyed.
7. Apply the Infrastructure Changes: If you are satisfied with the planned changes, apply the infrastructure modifications by running **terraform apply**. Terraform will prompt for confirmation before making any changes.
8. Review and Validate the Infrastructure: Once the Terraform apply command completes, review the created infrastructure to ensure it matches your expectations. Verify that the resources have been provisioned correctly.
9. Cleanup and Destroy the Infrastructure (Optional): If you want to clean up the resources created by Terraform, run **terraform destroy**. This command will prompt for confirmation before destroying the resources.

**_Note: The specific steps may vary depending on the structure of the repository and the provided documentation. Always refer to the repository's documentation or README file for any specific instructions or requirements._**
