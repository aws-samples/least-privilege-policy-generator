## Least Privilege Policy Generator

Least Privilege Policy Generator is a CloudFormation Template that, when built in an AWS account and the logon credentials are added to the IAM User, allows an end user to log in to the IAM User. The IAM User has no permissions except a Managed Policy with "ec2:DescribeInstances" at the start. As the end user attempts to do things when they are logged in to the IAM User their actions will generate API calls that are denied. All API call events will be sent from a CloudTrail to a CloudWatch Log Group. The CloudWatch Log Group will filter for events with denied API calls. When it detects an event with a denied API call, it forwards that event to a Lambda. The Lambda adds the API call to the IAM User's Policy.

The value of this is when generating least privilege IAM policies it is recommended to start with nothing and add only the API calls that are needed. Often the recommended way to do this is to sit down and go over each service and API call to determine what is needed. This is slow and prone to human error because even the most knowledgeable AWS user doesn't know the thousands of API calls across the hundreds of AWS services. With Least Privilege Policy Generator, the end user can log in to the user, follow their written procedures, and the policy generates itself.

How to use:
1. Download the CloudFormation template: LeastPrivilegePolicyGenerator.yml
2. In the AWS Console or AWS CLI, using CloudFormation, upload the template to create the resources.
3. In the parameters step, paste the ARN of the role that will be used when logged in to AWS.
4. Log in to AWS with the role that was selected.
5. Asssume the LPPG role.
6. Using your written procedures, take the actions specified one at a time.
7. Moving the events from CloudTrail to CloudWatch and finally to the Lambda will take a few minutes. Monitor the Lambda's CLoudWatch Log Group or the IAM Policy to know when the changes have been made.
8. Take the next action.
9. Repeat until all actions are taken.
10. Review the final IAM Policy to remove excessive API calls.
11. The policy is the final product. Copy and save it before you delete the CloudFormation Template.

WARNINGS:
1. While there is nothing stopping you, please do not use this in a Production environment. While this may be a more efficient way to make least privilege IAM policies, it is not the most secure because every action adds API calls to the IAM policy. It is best to use this in a Test/Development environment, review the IAM policy, then transfer the IAM policy to Production.
2. Review the final IAM policy! Even opening the EC2 dashboard makes over a dozen API calls as AWS attempts to populate the dashboard. All of those denied calls are automatically added to the IAM policy. Many of these may be excessive. Review the final IAM policy and remove these excessive API calls.
3. The deletion of the CloudFormation Template may not delete all created resources. It may be required to delete the CLoudFormation multiple times or manually deleting the created resources.

Thoughts on the Least Privilege Policy Generator:
1. Cloudwatch Log Groups are not fast. Expect to wait a few minutes before each action's API calls are added to the IAM policy. This is still likely faster than manually going through a list of API calls for each service.
2. It would have likely been faster to use Amazon EventBridge. However, many events that would need to be added to a least privilege policy aren't processed by Amazon EventBridge: "All events that are delivered by CloudTrail have AWS API Call via CloudTrail as the value for detail-type. **Events from API actions that start with the keywords List, Get, or Describe aren't processed by EventBridge**, with the exception of events from the following AWS STS actions: GetFederationToken, GetSessionToken" (Source: https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-service-event.html)


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

