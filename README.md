# AWS-Calculator
A simple End-to-End calculator web application
In this project I will be buidling an End-to-End web application using the following AWS Services:

1. AWS Amplify - Used to deploy and host website
2. AWS Lambda - Code/function that will run serverlessly due to a trigger
3. API Gateway - Used to make an API call that will trigger Lambda Function
4. DynamoDB - Used to store results
5. AWS IAM - USeed to set permissions

What is needed prior to starting this project - 
 - A text editor (Notepad, VSCode)
 - A configured AWS account and access to the Console
 - Basic knowledge of AWS

Project Overview:

Step 1 = Create/host a webpage <br/>
Step 2 = A way to do some math <br/>
Step 3 = Invoke math funtionality <br/>
Step 4 = Create somewhere to store and return the calculation result <br/>
Step 5 = A way to handle permissions <br/>

***
STEP 1 Create a Webpage with Amplify
***
Create a new "index.html" file  
![Screenshot (4)](https://github.com/user-attachments/assets/deeb1b9e-c68b-4f2d-86df-1f55d6beb532)

Add the following code:
![Screenshot (5)](https://github.com/user-attachments/assets/d7f73991-98c0-44bc-af50-2370f1a8b3e2)

Zip Up the file
![Screenshot (7)](https://github.com/user-attachments/assets/bbd30bae-f622-4001-8eee-26bb8c244d66)

Open AWS Console -> navigate to Amplify -> click "deploy an app" -> "Deploy without Git" <br/>
Give the app a name. Mine is "EJs-Calculator" <br/>
Branch name. I used "dev" <br/>
Drag and Drop your index.zip file to the upload <br/>
click "Save and deploy"
![Screenshot (10)](https://github.com/user-attachments/assets/b3e17924-904b-483e-85ce-0b6fcfbc2d64)
You can visit the domain if you want to make sure everything works. It should look like this.
![Screenshot (11)](https://github.com/user-attachments/assets/5aa1ab72-53ed-428d-a817-f0cbdd89bbe3)
<br/>

***
STEP 2 Create a way to do some math with a Lambda function
***
Open a new tab in the console by right clicking the AWS logo in the top left corner and selecting "open link in new tab". Navigate to Lambda <br/>
Select "Create a Function" <br/>
click "Author from scratch" -> create a function name, mine is "mymathfunction" -> select Python as your language. -> click "Create function" <br/>
Type the following for your "Code source" <br/>

![Screenshot (12)](https://github.com/user-attachments/assets/e152a83f-5b8c-429b-bf34-2b319eb60bd3)

![Screenshot (13)](https://github.com/user-attachments/assets/e820eac6-36cf-4b39-88b6-69e0f8aa018d)

Next click "Deploy". <br/>
Once it is deployed, select "create new test event" <br/>
Create an Event Name -> select "private" -> <br/>
for the event JSON you need 3 things:
1. Operation (add, subtract, multiply, divide, power)
2. num1
3. num2 <br/>
click "Save" 
![Screenshot (15)](https://github.com/user-attachments/assets/907cae62-19c7-4167-8a5a-14b830dac9e1)

Run the text to make sure it is working properly. You should see Statuscode 200 and the proper answer.
![Screenshot (17)](https://github.com/user-attachments/assets/01382cdf-3fad-456a-b0b2-e4a6d645fcb9)
<br/>

***
STEP 3 Invoke the Math Functionality with API Gateway
***
Open a new tab in the console by right clicking the AWS logo in the top left corner and selecting "open link in new tab". Navigate to API Gateway <br/>
Create a New API using REST API -> Select "New API" -> Create an API Name, mine is MathAPI" -> click "Create API"<br/>
You should be in the "Resources" section. Within this section click "Create method" <br/>
Method Type will be post (because the user will be sending in data) <br/>
Integration type will be "Lambda function" <br/>
Select the lambda function and begin typing. Your function should auto appear. Mine is "mymathfunction". <br/>
Click Create Method
![Screenshot (18)](https://github.com/user-attachments/assets/2bed68dc-076b-4219-a1c2-bb84c1a65fc3)

Once created, click "enable CORS" -> select post -> save <br/>
![Screenshot (20)](https://github.com/user-attachments/assets/0be4a008-1ce4-4053-b549-010e61272704)

Next, Select "Deploy API" -> "new stage" -> create stage name, I used "dev" -> Deploy <br/>
Once created, make sure to copy the invoke URL. Save the URL by typing it on notepad. We will need it later. <br/>
Test this by going back to "Resources" -> click "POST" -> Scroll over to "Test" -> You can use the same Test body as before <br/>
![image](https://github.com/user-attachments/assets/b7488094-6d23-47e2-a473-494f93b2c750)
You should receive statuscode 200 again and the correct answer. <br/>
Congrats you can now trigger your math function in Lambda with an API call! <br/>
<br/>

***
STEP 4 Create a Database to store the math result.
***
Open a new tab in the console by right clicking the AWS logo in the top left corner and selecting "open link in new tab". Navigate to DynamoDB <br/>
-> Create table -> Choose table name, Mine is "MyMathDatabase" -> For partition key, I used "id" -> Create Table <br/>
Once created click into the database to retrieve your ARN. Save the ARN. You will need it later! <br/>
![Screenshot (22)](https://github.com/user-attachments/assets/ec9d91da-5669-4de2-8085-3c26a13cb387)
Now we need to set the proper permissions. To do so, navigate back to the Lambda tab <br/>
In Lambda click your function and then navigate to "configuration" -> click "Permissions" -> click "Role name" to open it in a new tab <br/>
![Screenshot (23)](https://github.com/user-attachments/assets/b9adcb30-1de0-45ad-be4d-233e37741801)
You are now in the IAM tab <br/>
click "Add permissions" -> create inline policy -> click the JSON tab and replace the code with the following while making sure to add your own ARN from earlier:
![Screenshot (25)](https://github.com/user-attachments/assets/ba272933-09c8-408e-8247-eeceed56284e)
This is just giving our Lambda function permission to do all these different actions on our Database. <br/>
Give it a Name, Mine is "MyMathDynamoPolicy" -> create policy <br/>
Navigate back to the Lambda Tab <br/>
Go to your function and select the "code" tab. <br/>
Add the following changes to your code:<br/>
![Screenshot (26)](https://github.com/user-attachments/assets/177bb426-92e1-4764-ba10-ebfbef632236)
![Screenshot (27)](https://github.com/user-attachments/assets/96038aff-d2f6-461a-b84c-3feda820af30)

The changes include: adding python software development kit "boto3", import a date and date formatting package so that when we insert a result into our db table we also insert the current time. Writing the result to the DynamoDB table and saving response<br/>
Make sure to replace "table =" to include the database table name you selected <br/>
Deploy the change -> Test the new code to make sure it is working properly. <br/>
Once you verify the code works properly, navigate back to the DynamoDB tab and click "explore items" <br/>
You should see a new item in your database! The result and the date.
![Screenshot (28)](https://github.com/user-attachments/assets/d794f341-9c9b-41d5-ad50-dcad1e9d33b2)
<br/>

***
STEP 5 Update the HTML page to call API Gateway
***
![Screenshot (29)](https://github.com/user-attachments/assets/c5f213e3-882e-4314-9bc6-727eff888731)
![Screenshot (30)](https://github.com/user-attachments/assets/4d9a42b4-3c01-4089-8904-c56a45b5efe1)
![Screenshot (31)](https://github.com/user-attachments/assets/a2d51491-9ec2-4cb0-94cb-aa263a78682a)
Be sure to edit your callAPI variable "var callAPI" to use the API Gateway you previously saved by going to the line 60 "fetch" and adding your URL. <br/>
Delete your original index.html file and save this in it's place and zip up the file again. <br/>
Navigate back to Amplify <br/>
Click "Deploy Updates" -> Drag and Drop your updated zip file. -> click "Save and Deploy" <br/>
Click on the URL to see the finished product! <br/>
Try out Some Calculations! <br/>
![Screenshot (32)](https://github.com/user-attachments/assets/bef11d97-24be-4f4b-bbc2-c2512b4fc9d0)
![Screenshot (33)](https://github.com/user-attachments/assets/18609d94-79c7-421c-b1e0-be7cc0204a2a)
![Screenshot (34)](https://github.com/user-attachments/assets/3fbc2e74-a366-4557-b1f4-ccf379f9f46e)
![Screenshot (35)](https://github.com/user-attachments/assets/38707a72-cebf-49b6-8e8f-a57ad5624880)
![Screenshot (36)](https://github.com/user-attachments/assets/9f0abb8f-6998-46ff-99e6-ba3ea96b6a39)

Great!! <br/>
Navigate back to DynamoDB to make sure they are stored in your Database!
![Screenshot (36)](https://github.com/user-attachments/assets/2ca30081-b576-4f29-bf77-8d5ae960e3ef)

Last thing, Do not forget to delete and close out of all services so that you do not find yourself with a bill at the end of the month!









