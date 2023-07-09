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


