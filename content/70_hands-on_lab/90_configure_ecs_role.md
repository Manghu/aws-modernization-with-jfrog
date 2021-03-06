---
title: "Configure an IAM Role for ECS"
chapter: false
weight: 90
pre: "<b>9. </b>"
---
We now have our Artifactory credentials in the AWS Secrets Manager. Next, we must create an IAM role that will allow ECS to access our Artifactory secrets and deploy our image. 

{{% notice info %}}
Before you can launch ECS container instances and register them into a cluster, you must create an IAM role for those container instances to use when they are launched. The Amazon ECS container agent makes calls to the Amazon ECS API on your behalf using this role. We add an additional policy to allow ECS to access our secrets.
{{% /notice %}}

1. Go to [IAM Roles](https://us-east-1.console.aws.amazon.com/iam/home?#/roles).
2. Click on **Create role**.
3. Select the **Elastic Container Service** service and **Elastic Container Service Task** use case.
4. Click on **Create Policy**.
5. Click on the **JSON** tab and paste the following.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "kms:Decrypt",
        "secretsmanager:GetSecretValue"
      ],
      "Resource": [
        "<secret_arn>",
        "arn:aws:kms:<region>:<aws_account_id>:key/key_id"     
      ]
    }
  ]
}

```

6. Substitute your copied **Secret ARN** for \<secret_arn\>.
7. Also substitute your \<region\> and \<aws_account_id\>. You can derive this from the **Secret ARN** format.

```
arn:aws:secretsmanager:<region>:<aws_account_id>: secret:secret_name
```

8. Click on **Review policy**.
9. Name the policy ```ecsAccessToSecrets``` and create the policy. This creates a policy that allows ECS to access your Artifactory credentials that are stored in the Secrets Manager.
![Inline Policy](/images/inline-policy.png)
10. Now go back to your role and search for your new policy _ecsAccessToSecrets_ and attach it. You may need to refresh the policy list. 
11. Also attach the **AmazonECSTaskExecutionRolePolicy**. This policy allows the execution of Amazon ECS tasks.
12. Click through the next steps and then create the role with the name ```ecsWorkshop```.
![IAM Role](/images/iam-role.png)

You have now created an IAM role that will allow ECS to deploy images from Artifactory.