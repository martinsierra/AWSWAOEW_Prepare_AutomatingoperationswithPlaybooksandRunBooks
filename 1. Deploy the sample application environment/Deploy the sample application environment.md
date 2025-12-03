# Deploy the sample application environment

In this section, you will prepare a sample application. The application is an API hosted inside a docker container, using [Amazon Elastic Container Service (ECS)](https://aws.amazon.com/ecs/) . The container is accessed via an [Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html). 

The API is a private microservice within your [Amazon Virtual Private Cloud (VPC)](https://aws.amazon.com/vpc/). Communication to the API can only be done privately through routes within the VPC subnet. In our lab example, the business owner has agreed to run the API over HTTP protocol to simplify the implementation.

The API has two actions available which encrypt and decrypt information. This is triggered by doing a REST POST call to the `/encrypt` / `/decrypt` methods as appropriate.

- The **encrypt** action will allow you to pass a secret message along with a 'Name' key as the identifier and it will return a 'Secret Key Id' that you can use later to decrypt your message.
- The **decrypt** action allows you to then decrypt the secret message passing along the 'Name' key and 'Secret Key Id' you obtained before to get your secret message.

Both actions will make a write and read call to the application database hosted in [Amazon Relational Database Service (RDS)](https://aws.amazon.com/rds/), where the encrypted messages are stored.

The following step-by-step instructions will provision the application that you will use with your **runbooks** and **playbooks**.

Explore the contents of the CloudFormation script to learn more about the environment and application.

You will use this sample application as a sandbox to simulate an application performance issue, start your **runbooks** and **playbooks** to autonomously investigate and remediate.

## Actions items in this section:

1. You will use [CloudShell](https://aws.amazon.com/cloudshell/)  to run a script to build the sample application as shown in the diagram below.

    [Section1 App Arch](1. Deploy the sample application environment/section2-base-application.png)

1.0 Build baseline application.
In this first step, you will provision the baseline application stack that consists of a sample application along with its underlying resources. This stack will be deployed via the AWS CloudShell environment. Follow the steps below to proceed.

From the main console, access the AWS CloudShell service.

Once your CloudShell environment is established. You should see a terminal output as per below:

Section1 shell

Download the script by running below commands.
1
2
3
4
wget https://ws-assets-prod-iad-r-iad-ed304a55c2ca1aee.s3.us-east-1.amazonaws.com/6d753373-da0f-46a6-8195-4f001e507961/pack.zip

unzip pack.zip
cd ~/scripts

Copy and paste the command below, replacing <region> and <accountid> with your current region and account ID. Replace <sysops@domain.com> with the email address representing the system operators team, and <owner@domain.com> with the email address representing the business owner.
1
bash ./build_application.sh <region> <accountid> <sysops@domain.com> <owner@domain.com>

The build_application.sh script will build and deploy your sample application, along with the architecture that hosts it. The application architecture will have capabilities to notify systems operators and owners, leveraging Amazon Simple Notification Service . You can use the same email address for sysops@domain.com and owner@domain.com if you need to, but ensure that you have both values specified.

If you have deployed Amazon ECS before in your account, you may encounter InvalidInput error with message "AWSServiceRoleForECS has been taken" while running the build_application.sh script. You can safely ignore this message, as the script will continue despite the error.

The above command runs the build and provisioning of the application stack. The script should take about 20 mins to finish.
Section 2 Cloud9 IDE Welcome Screen

The build_application.sh will deploy the application docker image and push it to Amazon ECR . This is used by Amazon ECS.  Once the build script completes, another CloudFormation stack containing the application resources (ECS, RDS, ALB, and others) will be deployed.
In the CloudFormation console, you should see a new stack being deployed called walab-ops-sample-application. Wait until the stack reaches CREATE_COMPLETE state and proceed to the next step.
Section 2 CreateComplete

1.2. Confirm the application status.
Once the application is successfully deployed, go to your CloudFormation console  and locate the stack named walab-ops-sample-application.

Confirm that the stack is in a 'CREATE_COMPLETE' state.
Record the following output details as it will be required later:
Take note of the DNS value specified under OutputApplicationEndpoint of the Outputs.
The screenshot below shows the output from the CloudFormation stack:

Section2 DNS Output

Check for an email sent to the system operator and owner addresses you've specified in the build_application.sh script. This email should also be visible in the CloudFormation parameter under in the SystemOpsNotificationEmail and SystemOwnerNotificationEmail.

Choose confirm subscription on the email links to subscribe.

Section2 DNS Output

There will be 2 emails sent to your address, please ensure to subscribe to both of them.
1.3. Test the application.
In this section, you will be testing the encrypt API action from the deployed application.

The application will take a JSON payload with Name as the identifier and Text key as the value of the secret message.

The application will encrypt the value under Text key with a designated KMS key and store the encrypted text in the RDS database with Name as the primary key.

Note: For simplicity purposes the sample application will re-use the same KMS keys for each record generated.
In the CloudShell terminal, run the commands below, replacing the <ApplicationEndpoint> with the OutputApplicationEndpoint from previous step. This command will run curl  to send a POST request with the secret message payload {"Name":"Bob","Text":"Run your operations as code"} to the API.
1
ALBEndpoint="<ApplicationEndpoint>"

1
curl --header "Content-Type: application/json" --request POST --data '{"Name":"Bob","Text":"Run your operations as code"}' $ALBEndpoint/encrypt

Once you run the previous command, you should see output as follows:
1
{"Message":"Data encrypted and stored, keep your key save","Key":"EncryptKey"}

Take note of the encrypt key value under Key .

Run the command below, pasting the encrypt key you took note of previously under the Key section to test the decrypt API.

1
curl --header "Content-Type: application/json" --request GET --data '{"Name":"Bob","Key":"EncryptKey"}' $ALBEndpoint/decrypt

Once you run the command you should see the following output:
1
{"Text":"Run your operations as code"}

Congratulations!
You have now completed the first section of the Lab.

You should have a sample application API which we will use for the remainder of the lab.

Choose Next Step to continue to the next section.
