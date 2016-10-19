Grails AWS SDK SNS Plugin
=========================

[![Build Status](https://travis-ci.org/agorapulse/grails-aws-sdk-sns.svg?branch=master)](https://travis-ci.org/agorapulse/grails-aws-sdk-sns)
[![Download](https://api.bintray.com/packages/agorapulse/plugins/aws-sdk-sns/images/download.svg)](https://bintray.com/agorapulse/plugins/aws-sdk-sns/_latestVersion)

The [AWS SDK Plugins for Grails3](https://medium.com/@benorama/aws-sdk-plugins-for-grails-3-cc7f910fdc0d#.5gdwdxei3) are a suite of plugins that adds support for the [Amazon Web Services](http://aws.amazon.com/) infrastructure services.

The aim is to to get you started quickly by providing friendly lightweight utility [Grails](http://grails.org) service wrappers, around the official [AWS SDK for Java](http://aws.amazon.com/sdkforjava/) (which is great but very “java-esque”).
See [this article](https://medium.com/@benorama/aws-sdk-plugins-for-grails-3-cc7f910fdc0d#.5gdwdxei3) for more info.

The following services are currently supported:

* [AWS SDK DynamoDB Grails Plugin](http://github.com/agorapulse/grails-aws-sdk-dynamodb)
* [AWS SDK Kinesis Grails Plugin](http://github.com/agorapulse/grails-aws-sdk-kinesis)
* [AWS SDK S3 Grails Plugin](http://github.com/agorapulse/grails-aws-sdk-s3)
* [AWS SDK SES Grails Plugin](http://github.com/agorapulse/grails-aws-sdk-ses)
* [AWS SDK SNS Grails Plugin](http://github.com/agorapulse/grails-aws-sdk-sns)
* [AWS SDK SQS Grails Plugin](http://github.com/agorapulse/grails-aws-sdk-sqs)

# Introduction

This plugin adds support for [Amazon Simple Notification Service (Amazon SNS)](https://aws.amazon.com/sns/), a fully managed and highly scalable messaging.

Right now, the plugin is only used to push mobile notifications to Apple iOS and Android mobile devices.


# Installation

Add plugin dependency to your `build.gradle`:

```groovy
dependencies {
  ...
  compile 'org.grails.plugins:aws-sdk-sns:2.0.3'
  ...
}
```

# Config

Create an AWS account [Amazon Web Services](http://aws.amazon.com/), in order to get your own credentials accessKey and secretKey.

Required permissions:

* "sns:CreatePlatformEndpoint",
* "sns:DeleteEndpoint",
* "sns:GetEndpointAttributes",
* "sns:Publish",
* "sns:SetEndpointAttributes"

## AWS SDK for Java version

You can override the default AWS SDK for Java version by setting it in your _gradle.properties_:

```
awsJavaSdkVersion=1.10.66
```

## Credentials

Add your AWS credentials parameters to your _grails-app/conf/application.yml_:

```yml
grails:
    plugin:
        awssdk:
            accessKey: {ACCESS_KEY}
            secretKey: {SECRET_KEY}
```

If you do not provide credentials, a credentials provider chain will be used that searches for credentials in this order:

* Environment Variables - `AWS_ACCESS_KEY_ID` and `AWS_SECRET_KEY`
* Java System Properties - `aws.accessKeyId and `aws.secretKey`
* Instance profile credentials delivered through the Amazon EC2 metadata service (IAM role)

## Region

The default region used is **us-east-1**. You might override it in your config:

```yml
grails:
    plugin:
        awssdk:
            region: eu-west-1
```

If you're using multiple AWS SDK Grails plugins, you can define specific settings for each services.

```yml
grails:
    plugin:
        awssdk:
            accessKey: {ACCESS_KEY} # Global default setting
            secretKey: {SECRET_KEY} # Global default setting
            region: us-east-1       # Global default setting
            sns:
                accessKey: {ACCESS_KEY} # (optional)
                secretKey: {SECRET_KEY} # (optional)
                region: eu-west-1       # (optional)
                android:
                    applicationArn: {mobileApplicationArn}
                ios:
                    applicationArn: {mobileApplicationArn}
            
```

# Usage

The plugin provides the following Grails artefacts:

* **AmazonSNSService**

## Registering a mobile device

Once you get a device token, you can register the mobile and get the mobile endpoint arn.

```groovy
endpointArn = amazonSNSService.registerDevice(
    'ios', // or 'android'
    deviceToken,
    [data: 'some custom user data'].toString()
)
```

## Validating a mobile device

Once in while, you might validate a mobile endpoint arn: if required (e.g.: token or platform changes), it will update the endpoint by deleting the old one and create a new one.

```groovy
endpointArn = amazonSNSService.validateDevice(
    'ios', // or 'android'
    endpointArn,
    deviceToken,
    [data: 'some custom user data'].toString()
)
```

## Pushing a notification to a mobile device

Once, you have the mobile endpoint arn, you can easily push notification to it:

```groovy
// For android device
amazonSNSService.sendAndroidAppNotification(
    endpointArn,
    [
        badge: 9,
        data: '{"foo": "some bar"}',
        message: 'Some message'
        title: 'Some title'
    ],
    'Welcome' // Collapse key
)

// For ios device
amazonSNSService.sendIosAppNotification(
    endpointArn,
    [
        alert: 'Some message',
        data: '{"foo": "some bar"}',
        badge: 9,
        sound: 'default'
    ]
)
```

## Unregister a mobile device

To delete a mobile endpoint.

```groovy
endpointArn = amazonSNSService.unregisterDevice(
    endpointArn
)
```

# Bugs

To report any bug, please use the project [Issues](http://github.com/agorapulse/grails-aws-sdk-sns/issues) section on GitHub.

Feedback and pull requests are welcome!

