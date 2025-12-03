# Build & Run an Investigative Playbook

The efficiency of issue resolution within an Operations team is directly linked to their tenure and experience. Where an Operator has prior knowledge of a particular issue, they will have a headstart in being able to reach resolution in terms of understanding logs and metrics which were used in previous situations. Whilst this constitutes value to an Operations group, it also represents a single point of failure and a scalability challenge.

This is where [playbooks(broken link)](https://wa.aws.amazon.com/wat.concept.playbook.en.html)  become important. Playbooks are a documented set of predefined steps, which are run to identify an issue. The result of each step can be used to either call more steps to run, or alternatively to trigger manual intervention.

Automating playbook activities wherever possible, is critical to reducing the time to respond to an incident.

The AWS Cloud offers multiple services you can use to build an automated playbook, one which is AWS Systems Manager.

AWS Systems Manager offers an automation document capability (known within Systems Manager as [runbooks](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-documents.html), which allows for the creation of a series of executable steps to orchestrate your investigation and remediation. AWS Systems Manager Automation Documents allow a user to run custom scripts, call AWS service APIs, or even run remote commands on cloud or on-premise compute instances.

In this section, you will focus on creating an automated **playbook** in assisting your investigation, as a Systems Operator.

### Actions items in this section:
1. You will build a playbook to gather information about the workload and query the relevant metrics and logs.
2. You will run the automation document to investigate your issue.

## 3.0 Prepare Automation Document IAM Role
The Systems Manager Automation Document you are building will require assumed permissions to run the investigation and remediation steps. You will need to create the IAM role that will assume the permissions to perform the playbook activities. To simplify the deployment process, a CloudFormation template has been provided that you can deploy via the console or AWS CLI. Please choose one of the two following deployment steps:

<details>

<summary>Click here for CloudFormation Console deployment</summary>
1. Download the template [here(page not found)](https://catalog.workshops.aws/well-architected-operational-excellence/en-US/operations/200-automating-operations-with-playbooks-and-runbooks/code/templates/automation-role-yml).
2. [CloudFormation create stack documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) on how to deploy the CloudFormation template.
3. Use `waopslab-automation-role` as the **Stack Name**, as this is referenced by other stacks later in the lab.
</details>
Click here for CloudFormation CLI deployment [TODO: Fix this ]
**Note:** To deploy from the command line, ensure that you have installed and configured AWS CLI with the appropriate credentials.

1. From the **Cloud Shell** terminal change to the appropriate folder as shown:

```
cd ~/templates
```

2. Then run the command listed below:

```
aws cloudformation create-stack --stack-name waopslab-automation-role \
                                --capabilities CAPABILITY_NAMED_IAM \
                                --template-body file://automation_role.yml 
```
Locate the **StackStatus** and confirm it is set to **CREATE_COMPLETE**.


1. Once you have deployed the CloudFormation stack above, go to the IAM Console.

2. On the side menu, choose Roles and locate the IAM role named AutomationRole.

3. Take note of the ARN of the role, as we will need it later in the lab.

![Section3]()

## 3.1 Building the "Gather-Resources" Playbook.

In preparation for the investigation, you need to know all services and resources associated to the issue. When the email notification is sent, information in the email does not contain any resources information. To gather this necessary information, we will build a **playbook** to acquire all related resources using our CloudWatch alarm ARN as a reference.

Codifying your playbook with AWS Systems Manager allows for maximum code reusability. This will reduce overhead in re-writing codes that has identical objectives.

![Section4]()

Note: Follow these step to build and run playbook. Select a guide to deploy using either the AWS console, the AWS CLI or via a CloudFormation template deployment.
Click here for CloudFormation Console deployment step
Click here for CloudFormation CLI deployment step
Click here for Console step-by-step
Once the automation document is created, you can now give it a test.

You can then find the newly created document under the Owned by me tab of the Document section in Systems Manager Console.
Section3 

Choose the playbook called Playbook-Gather-Resources and choose Execute Automation to run your playbook.

Paste in the CloudWatch Alarm ARN ( You can find this ARN in the email notification in section 2.1 Observing the alarm being triggered ) and choose Execute to test the playbook.

Section3 

Once the playbook run is completed successfully, choose the Step Id to see the final message and output of the step. You should be able to see this output listing all the resources of the application
Section3 

Copy the Resources list output from the section as highlighted in the screenshot below. This list consist of the all the resources defined in the CloudFormation stack related to our application. These information includes the Elastic Load Balancer, ECS and RDS resource id that we can now use to further our investigation of the underlying issue.
Section3 

You can Paste the output into a temporary location like notepad for now. You will need this value for our next step.
3.2 Building the "Investigate-Application-Resources" Playbook.
In the previous step, you have created a playbook that finds all related AWS resources in the application. In this step you will create a playbook that will interrogate resources, capture recent metrics and logs, to look for insights and better understand the root cause of the issue.

In practice, there can be various possibilities of actions that the playbook can take to investigate, depending on the scenario presented by the issue. The purpose of this Lab is to showcase how you can use playbook to aid investigation, rather than advise on a specific action path.

Therefore, in this lab we will assume an example scenario. The playbook will look at metrics and logs of the ELB, ECS and RDS services in the resource list. The playbook will then highlight the metrics and logs that is considered outside of normal operational threshold.

Section3 

Please follow the below instructions to build this playbook:

Note: We will deploy this playbook via CloudFormation template to simplify deployment. Please follow the steps below to deploy the CloudFormation template via CLI / or Console.
Click here for CloudFormation Console deployment step
Click here for CloudFormation CLI deployment step
When the document is created, you can go ahead and run a quick test.

You can find the newly created document under the Owned by me tab of the Document resource in the Systems Manager console.

choose the playbook called Playbook-Investigate-Application-Resources and choose Execute Automation to run our playbook.

Paste in the resources list you took note from the output of the previous playbook ( refer to section 3.1 Building the "Gather-Resources" Playbook ) under Resources and choose Execute

Section3 

Under Executed Steps you should be able to see each of the step the playbook. If you view the content of the document you will be able to see the code and find out what each step does.
Section3 

For simplicity, we have created a list of output and description for each step. Expand the list below to view.

Output list
Wait until all steps are completed successfully.
3.3 Building the "Investigate-Application-From-Alarm" Playbook.
So far we have 2 separate playbooks. The first playbook gathers the list of resources associated with the application. The second playbook queries the relevant resources and investigates the appropriate logs and metrics.

In this step we will automate our playbooks further by creating a parent playbook that orchestrates the 2 Investigative playbooks. We will add another step to send notification to our Developers and System Owners.

Section4 

Follow the instructions below to build the parent Playbook.

Note: Select a step-by-step guide below to build the parent playbook using either the AWS console a CloudFormation template.
Click here for CloudFormation Console deployment step
Click here for CloudFormation CLI deployment step
Click here for Console step-by-step guide
Section4 

Our playbook will run investigative tasks and send the result to an SNS topic where our Systems administrator / engineer will subscribe to. To do this we will need to create an SNS topic that our playbook will send notification to. Please follow the instructions specified in this link  and create a Standard SNS topic and name it PlaybookNotificationSNSTopic

Once you've created the topic, go ahead and subscribe your an email using this instruction here  :::

3.4 Executing investigation Playbook
You can now run the playbook to discover the result of the investigation.

Go to the Output section of the deployed CloudFormation stack walab-ops-sample-application and take note of below output values.

Go to the Systems Manager Automation document we just created in the previous step, Playbook-Investigate-Application-From-Alarm.

And then run the playbook passing the ARN as the AlarmARN input value, along with the SNSTopicArn.

You can get the AlarmARN from the email that you received from CloudWatch Alarm as described in step 3.1 Building the "Gather-Resources" Playbook. in this lab.
To get the value for SNSTopicArn, go to the CloudFormation console output of walab-ops-sample-application stack and copy, paste the value of OutputSystemEventTopicArn
Section3

When the playbook completed, an email will be send to you, which contains a summary of the investigation completed by the playbook as shown.
Section3 

Copy and paste the message section and use a json linter tool such as jsonlint.com  to give better structure for visibility. The result from the playbook investigation might vary slightly, but the overall findings should be similar to the below screenshot.
Section3 

From the report being generated you should see a large number of ELB504Count error and a high TargetResponseTime from the Load balancer. This explains the delay we are seeing from our canary alarm.
If you then look at the ECS summary, you will notice that there is only 1 ECS TaskRunningCount, with a relatively high CPUUtilization average. The script calculates the average of maximum value on the ECS service in the last 6 minutes window. If you do not see CPUUtilization value in the json, you can confirm this by going to the ECS service console and choose the Metrics tab.

Section3 

Therefore, it is likely that the immediate cause of the latency is resource constrained at the application API level running in ECS. Ideally, if we can increase the number of tasks in the ECS service, the application should be able to release some of the CPU Utilization constraints.

With all of these information provided by our playbook findings, we should be able to determine what is the next course of action to attempt remediation to the issue.

This concludes Section 3 of this lab, choose the link below to move on to the next section to build the remediation runbook.

Previous
