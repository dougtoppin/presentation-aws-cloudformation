## Intro to AWS CloudFormation

NOVALUG

10-Mar-2018

dougtoppin@gmail.com

[github.com/dougtoppin/presentation-aws-cloudformation](github.com/dougtoppin/presentation-aws-cloudformation.git)

---

## Agenda

- What is CloudFormation
- Why use it
- Related tools
- Where did it help me
- Syntax
- Nested stacks
- Examples
- Lessons learned
- Demo
- Links

---
### What is CloudFormation

"AWS CloudFormation provides a common language for you to describe and provision all the infrastructure resources in your cloud environment. CloudFormation allows you to use a simple text file to model and provision, in an automated and secure manner, all the resources needed for your applications across all regions and accounts. This file serves as the single source of truth for your cloud environment. "

---
### Why use it

Staying within the AWS ecosystem has advantages

* IAM
* logging
* cli
* admin console

+++

No other tools to learn, install, configure, support and pay for

+++

* it is comprehensive
* package all of your resources and creation/configuration/deletion orchestration into one or more templates
* outside tools can be installed or configuration performed as a part of the template

+++

If you have a demo or similar application to run in AWS and your instructions have the user manually create resources (security groups for example) and manually launch an EC2 instance you are going to lose some opportunities.

Instead, create a CloudFormation template in a public bucket and have users launch it

+++

Using it with existing templates requires less knowledge of AWS resource details

---
### Related provisioning tools

- ansible
- Terraform
- Chef
- Puppet
- SaltStack
- Red Hat CloudForms

---

### Where did it help me

My datacenter is a yaml file

Create an entire environment with a single command, again and again

+++

Namespace based environment means that multiple environments can coexist without stepping on each other

+++

Ease of environment creation facilitates things like automated regression testing such as a Jenkins pipeline I&T stage that spins up everything and then deletes it

+++

Creating CloudWatch dashboards when creating/deploying a service to the cluster as a part of the service template

Developers should include a monitoring dashboard along with their service

---

### Syntax

- yaml or json (note that yaml can have comments)
- template contains these sections
  - parameters - things passed in or defaulted
  - conditions - things to evaluate and set
  - resources - things to create
  - outputs - things to make visible and use elsewhere
- Designer UI can be helpful

+++

AWS resources are described using a format that can be found at [docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html](docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)

Create an EC2 instance would start with the following and Properties would provide specifics

```
Type: "AWS::EC2::Instance"
```

+++

### CLI

```
aws cloudformation create-stack \
   --stack-name test-01 \
   --template-url https://s3.amazonaws.com/test-01.yaml \
   --capabilities CAPABILITY_NAMED_IAM \
   --parameters \
     ParameterKey=param1,ParameterValue=value1

For local testing use a file reference instead of a url to S3

     --template-body file://./test-1.yaml
```

+++
Asynchronous resource creation means wait until everything has completed

When using a shell script the CloudFormation `wait` function will hold until an action has completed or failed

```
aws cloudformation wait stack-create-complete \
    --stack-name test-01
```

+++

Various `describe-` commands will list details about a stack

```
aws cloudformation describe-stacks --stack-name test-01 \
    --query 'Stacks[].StackStatus' --output text
```

+++

Delete a stack and remove all resources

```
aws cloudformation delete-stack --stack-name test-01
```

+++

Use `wait` to hold until the delete completes, necessary if related resouces cannot be deleted until this stack resources are deleted

```
aws cloudformation wait stack-delete-complete --stack-name test-01
```


+++

What are the resources in this template going to cost me?

```
aws cloudformation estimate-template-cost \
    --template-body file://./single_instance.json \
    --parameters ParameterKey=KeyName,ParameterValue=red

Output will be a URL to the AWS cost calculator
{
  "Url": "http://calculator.s3.amazonaws.com/calc5.html?key=cloudformation/xxx"
}
```

---
### Nested Stacks

Don't make one giant template

Group related/hierarchical resources into templates

Templates can invoke/reference other templates

---

### Examples

Launch an EC2 instance

tbd

+++

Get a value from the SSM Parameter Store

```
"Parameters" : {

"DBSnapshotIdentifier" : {
    "Type" : "AWS::SSM::Parameter::Value<String>",
    "Default" : "/dev/rds/snapshot",
    "Description" : "snapshot name to start from"
  }

}
```

+++

Put something into the Parameter Store

```
"Resources" : {
    "ParamStoreRdsEndpoint" : {
      "Type" : "AWS::SSM::Parameter",
      "Properties" : {
        "Name" : {
          "Fn::Join" : [ "/", [
            "",
            {"Ref": "Env"},
            "snapshot",
            "db.url" ] ] },
        "Type" : "String",
        "Description" : "Endpoint for the RDS instance",
        "Value" : { "Fn::GetAtt": [ "MyDB", "Endpoint.Address" ] }
      }
    }
```
+++

Create an ECS cluster

[github.com/awslabs/ecs-refarch-cloudformation](github.com/awslabs/ecs-refarch-cloudformation)

---

### Lessons learned

Move quickly from manual creation to automation

Don't get in the habit of creating things using the admin console, it is difficult to recreate exactly, document and support things created using the admin console

+++

deleting stacks

Sometimes a stack delete will fail (race condition for example), you might need to try it again or do a manual delete of a resource, do not assume that a stack delete will work every time without confirming

+++

Don't hardcode stuff in your templates

* use the Parameter Store to centralize settings
* pass parameters between templates

+++

Use `wait` if you need to ensure that resource creation/deletion is orchestrated before continuing to the next action

+++

When creating resources with names/keys concatenate the environment name to prevent conflicts if there is any chance that multiple instances of a stack might exist

+++

CloudFormation does not support SecureStrings in the Parameter Store but it is in the AWS queue to get working someday

+++

Start with an existing set of templates rather than create your own from scratch

+++

stack-updates seem to take a while vs delete/create cycles

+++

To prevent race conditions between stacks use Ouputs/export and ImportValue, this will cause a dependency/wait to occur. This also helps in creating a stack that needs something to exist but the "base" stack was never created.

+++

Note that the Parameter Store is rate limited, be careful about having CloudFormation do a lot of put values without some rate governing
---

### Demo

(desktop video goes here)
---

## Links

- [aws.amazon.com/cloudformation/](aws.amazon.com/cloudformation)
- [docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html#cli-aws-cloudformation](docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html#cli-aws-cloudformation)
- [docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html](docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
- [cloudonaut.io/optional-parameter-in-cloudformation/](cloudonaut.io/optional-parameter-in-cloudformation)
