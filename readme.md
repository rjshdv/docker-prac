# Single Container Deployment using Travis CI, AWS Beanstalk, Nginx, and Docker

This project outlines a deployment pipeline for a simple "Hello World" React.js application, containerized using Docker. By leveraging Travis CI and AWS Beanstalk, it facilitates automated continuous deployment. The setup incorporates Nginx for efficient web serving. Prioritizing best practices and security, the guide ensures a seamless deployment experience on AWS, streamlining updates and maintenance for developers.

## Prerequisites
- A Dockerized application.
- An account on [Travis CI](https://www.travis-ci.com/).
- An AWS account.

## Table of Contents
1. [Creating an EC2 IAM Instance Profile](#creating-an-ec2-iam-instance-profile)
2. [Setting Up Elastic Beanstalk Environment](#setting-up-elastic-beanstalk-environment)
3. [Update Object Ownership of S3 Bucket](#update-object-ownership-of-s3-bucket)
4. [Updating .travis.yml for AWS Deployment](#updating-travisyml-for-aws-deployment)
5. [Creating an IAM User](#creating-an-iam-user)
6. [Travis Variable Setup](#travis-variable-setup)
7. [Deploying Your Application](#deploying-your-application)

## Creating an EC2 IAM Instance Profile
1. Open the AWS Management Console.
2. Navigate to IAM Service by searching for "IAM".
3. Under Access Management, select **Roles**.
4. Click on **Create role**.
5. Select **AWS Service** and then choose **EC2**.
6. Search and attach the following policies: 
    - AWSElasticBeanstalkWebTier
    - AWSElasticBeanstalkWorkerTier
    - AWSElasticBeanstalkMulticontainerDocker
7. Name the role as `aws-elasticbeanstalk-ec2-role`.
8. Finalize by clicking **Create role**.

## Setting Up Elastic Beanstalk Environment
1. From AWS Management Console, find and click on **Elastic Beanstalk**.
2. Click **Create Application** or **Create environment** based on what you see.
3. Provide an **Application name**.
4. For the Platform, select **Docker**. Then, switch from `Docker running on 64bit Amazon Linux 2023` to `Docker running on 64bit Amazon Linux 2`.
5. Ensure `free tier eligible` is selected under the Presets section.
6. Proceed to the Service Access configuration form.
7. Choose **Create and use new service role** and name it `aws-elasticbeanstalk-service-role`. Set the EC2 instance profile to the `aws-elasticbeanstalk-ec2-role`.
8. Skip steps 3-6 and proceed to review.
9. Submit to create your Elastic Beanstalk environment.
10. Once created, click on the Domain link to view your application.

## Update Object Ownership of S3 Bucket
1. In AWS Management Console, find **S3** service.
2. Click on the `elasticbeanstalk` bucket associated with your environment.
3. Navigate to **Permissions** tab.
4. Under Object Ownership, enable ACLs and set Bucket owner preference to `Object Writer`.

## Updating .travis.yml for AWS Deployment
1. Set your region (e.g., `us-east-1`).
2. Set `app` to your Application Name.
3. Set `env` to your Beanstalk Environment name in lowercase.
4. Find your `bucket_name` from the S3 Storage service.
5. Set `bucket_path` to 'docker'.
6. Set `access_key_id` to `$AWS_ACCESS_KEY`.
7. Set `secret_access_key` to `$AWS_SECRET_KEY`.

## Creating an IAM User
1. Search for "IAM Security, Identity & Compliance Service".
2. Click **Manage Users** > **Add User**.
3. Provide a name (e.g., `docker-react-travis-ci`).
4. Attach the `AdministratorAccess-AWSElasticBeanstalk` policy.
5. Create the user and then navigate to **Security Credentials**.
6. Generate an access key for Command Line Interface (CLI) use.

## Travis Variable Setup
1. On your Travis Dashboard, select your project repository.
2. Navigate to **Settings** > **More Options**.
3. Add `AWS_ACCESS_KEY` and `AWS_SECRET_KEY` variables using the IAM access key and secret key respectively.

## Deploying Your Application
1. Make a minor change in your application.
2. Commit and push your changes:
    ```bash
    git add .
    git commit -m “testing deployment"
    git push origin main
    ```
3. Monitor your build on Travis Dashboard.
4. Once successfully built, check your AWS Elastic Beanstalk application. Your updates should reflect there.

---

That's it! Your application should now be successfully deployed using Travis CI, AWS Beanstalk, Nginx, and Docker. Ensure you monitor AWS resources to manage costs effectively.
