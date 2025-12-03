# Automating operations with Playbooks and Runbooks
## Introduction
Manually running your [runbooks (broken link)](https://wa.aws.amazon.com/wat.concept.runbook.en.html)  and [playbooks(broken link)](https://wa.aws.amazon.com/wat.concept.playbook.en.html)  for operational activities has a number of drawbacks:

- Activities are prone to errors & difficult to trace.
- Manual activities do not allow your operational practice to scale in line with your business requirements.

In contrast, implementing automation in these activities has the following benefits:

- Improved reliability by preventing the introduction of errors through manual processes.
- Increased scalability by allowing non linear resource investment to operate your workload.
- Increased traceability on your operation through log collection of the automation activity.
- Improved incident response by reducing idle time and automatically triggering activity based on known events.

At a glance, both **runbooks** and **playbooks** appear to be similar documents that technical users, can use to perform operational activities. However, there an essential difference between them:

- A [playbook (broken link](https://wa.aws.amazon.com/wat.concept.playbook.en.html)  documents contain processes that guides you through activities to investigate an issue. For example, gathering applicable information, identifying potential sources of failure, isolating faults, or determining the root cause of issues. Playbooks can follow multiple paths and yield more than one outcome.

- A [runbook (broken link](https://wa.aws.amazon.com/wat.concept.runbook.en.html) contains procedures necessary to achieve a specific outcome. For example, creating a user, rolling back configuration, or scaling resource to resolve the issue identified.

This hands-on lab will guide you through the steps to automate your operational activities using runbooks and playbooks built with AWS tools.

We will show how you can build automated runbooks and playbooks to investigate and remediate application issues using the following AWS services:

- [Systems Manager Automation](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-automation.html)
- [Simple Notification Service ](https://aws.amazon.com/sns/?whats-new-cards.sort-by=item.additionalFields.postDateTime&whats-new-cards.sort-order=desc)
- [Amazon CloudWatch synthetic monitoring](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Synthetics_Canaries.html)

## Well-Architected Best Practices
This lab helps you to exercise the following Well-Architected best practices in your operation change process:

- [OPS07-BP03 Use runbooks to perform procedures](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/ops_ready_to_support_use_runbooks.html)
- [OPS07-BP04 Use playbooks to investigate issues](https://docs.aws.amazon.com/wellarchitected/latest/operational-excellence-pillar/ops_ready_to_support_use_playbooks.html)

## Goals
- Build and run automated playbooks to support your investigations
- Build and run automated runbooks to remediate specific faults
- Enabling traceability of operations activities in your environment

## Prerequisites

- An [AWS account](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html)  that you are able to use for testing. The account should not be used for production purposes.
- An [IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html)  in your AWS account with full access to [CloudFormation](https://aws.amazon.com/cloudformation/),  [Amazon ECS](https://aws.amazon.com/ecs/), [Amazon RDS](https://aws.amazon.com/rds/),  [Amazon Virtual Private Cloud (VPC)](https://aws.amazon.com/vpc/),  AWS Identity and Access Management (IAM),  [AWS Cloud9](https://aws.amazon.com/cloud9/)

# Level
- 200 - Intermediate

# Duration
- 1 hour

# Costs
Cost Control
You will be billed for any applicable AWS resources used if you complete this lab that are not covered in the AWS Free Tier .

# Author
Stephen Salim

# Contributors
- Brian Carlson
- Jang Whan Han
- Juan Ossa

Last Updated
March 2024