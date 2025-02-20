# Text-To-Speech Lambda Function

## Description

This folder contains the code for the text-to-speech lambda.This Lambda will take as its input some text. It will call a specified third party API for text to speech (either Google Cloud Text-to-Speech or ElevenLabs Text to Speech), choosing which one depending on a parameter passed in. Then it will store the generated audio file in a specified S3 bucket and return the URL of that S3 file.

## Functionality

1. The function is triggered by an HTTP request, which includes the text to which audio has to be generated and the name of the API to use for transcription.
2. Supported APIs are Google Cloud Text-to-Speech (`google`) and ElevenLabs Text to Speech (`elevenlabs`).
3. The Lambda function assumes that the text received in the HTTP request is a valid string. If not, it will return an error.
4. If no API is specified, the Lambda will default to Google Cloud Text-to-Speech.
5. The Lambda will store the generated audio file in the S3 bucket whose name is specified in the environment variable `AWS_BUCKET_NAME`.
6. The Lambda will return the Object URL of the audio file generated by the third party API.

## Deployment Steps

1. Create a new Lambda function in the AWS console.
2. Upload the code in this folder to the Lambda function.
3. Upload the [layer](./layers/texttospeechlayer.zip) in the `layer` folder to the Lambda function and add it as a layer.
4. Make sure the Lambda function has access to the S3 bucket where the audio files are stored by adding the appropriate permissions to the execution role of the Lambda function. Sample Execution Role Policy can be found [here](./texttospeech_executionpolicy.json).
5. Update the required environment variables and Resource Values in the Lambda function as shown in the [SAM template](./texttospeechlambda.yaml).
6. Deploy the Lambda function
7. Test the Lambda function by sending an HTTP request to the API Gateway endpoint of the Lambda function. The request should include the text to which audio has to be generated and the name of the API to use for transcription. The response should include the Object URL of the audio file generated by the third party API.

## Environment Variables

The following environment variables are required for the Lambda function to work:

1. `AWS_BUCKET_NAME`: The name of the S3 bucket where the audio files are stored.
2. `ELEVENLABS_API_KEY`: The API key for the ElevenLabs Text to Speech API.
3. `GOOGLE_CLOUD_TEXT_TO_SPEECH_API_KEY`: The API key for the Google Cloud Text-to-Speech API.

## Resource Values

Following are the values that need to be updated in the Lambda function. Feel free to change the values as per your requirements.

1. `MemorySize` : 128
2. `Timeout` : 123
3. `Runtime` : python3.10

## Test Example (Python)

```python
import boto3
import json

payload = {
    "body": json.dumps(
        {
            "text": "Hello, this is a sample text to speech file. Do you like my voice?",
            "api": "elevenlabs",
        }
    )
}

client = boto3.client("lambda")
response = client.invoke(
    FunctionName="texttospeechlambda",
    InvocationType="RequestResponse",
    Payload=json.dumps(payload),
)
response = json.loads(response["Payload"].read())
print(response)
```
