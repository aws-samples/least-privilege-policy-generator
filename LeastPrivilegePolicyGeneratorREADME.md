## Least Privilege Policy Generator

Least Privilege Policy Generator is a CloudFormation Template that, when built in an AWS account and the logon credentials are added to the IAM User, allows an end user to log in to the IAM User. The IAM User has no permissions except a Managed Policy with "ec2:DescribeInstances" at the start. As the end user attempts to do things when they are logged in to the IAM User their actions will generate API calls that are denied. All API call events will be sent from a CloudTrail to a CloudWatch Log Group. The CloudWatch Log Group will filter for events with denied API calls. When it detects an event with a denied API call, it forwards that event to a Lambda. The Lambda adds the API call to the IAM User's Policy.

The value of this is when generating least privilege IAM policies it is recommended to start with nothing and add only the API calls that are needed. Often the recommended way to do this is to sit down and go over each service and API call to determine what is needed. This is slow and prone to human error because even the most knowledgeable AWS user doesn't know the thousands of API calls across the hundreds of AWS services. With Least Privilege Policy Generator, the end user can log in to the user, follow their written processes, and the policy generates itself.

How to use:
1. Download the CloudFormation template: LeastPrivilegePolicyGenerator.yml
2. In the AWS Console or AWS CLI, using CloudFormation, upload the template to create the resources
3. Add in the appropriate log on credentials to the IAM user
4. Log in to the user
5. Using your written procedures, take the actions specified one at a time.
6. Moving the events from CloudTrail to CloudWatch and finally to the Lambda will take a few minutes. Monitor the Lambda's CLoudWatch Log Group or the IAM Policy to know when the changes have been made.
7. Take the next action
8. Repeat until all actions are taken
9. Review the final IAM Policy to excessive API calls

WARNINGS:
1. While there is nothing stopping you, please do not use this in a Production environment. While this may be a more efficient way to make least privilege IAM policies, it is not the most secure because every action adds API calls to the IAM policy. It is best to use this in a Test/Development environment, review the IAM policy, then transfer the IAM policy to Production.
2. Use written procedures. If you don't have formalized procedures then it will be difficult to validate that the generated policy is least privilege because it only has the necessary API calls.
3. Review the final IAM policy! Even opening the EC2 dashboard makes over a dozen API calls as AWS attempts to populate the dashboard. All of those denied calls are automatically added to the IAM policy. Many of these may be excessive. Review the final IAM policy and remove these excessive API calls.

Thoughts on the Least Privilege Policy Generator:
1. Cloudwatch Log Groups are not fast. Expect to wait a few minutes before each action's API calls are added to the IAM policy. This is still likely faster than manually going through a list of API calls for each service.
2. It would have likely been faster to use Amazon EventBridge. However, many events that would need to be added to a least privilege policy aren't detected by Amazon EventBridge: "All events that are delivered by CloudTrail have AWS API Call via CloudTrail as the value for detail-type. **Events from API actions that start with the keywords List, Get, or Describe aren't processed by EventBridge**, with the exception of events from the following AWS STS actions: GetFederationToken, GetSessionToken" (Source: https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-ct-api-tutorial.html)
3. Another way this may be improved upon is to add a way to send an email for each API call to verify that it should be included. It is possible, but out of the scope of this version of Least Privilege Policy Generator. An issue with this addition is the end user may not know what the API call is, and the large number of API calls that can happen at once.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

