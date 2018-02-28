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

It is comprehensive


---
### Related tools

- ansible
- Terraform
- Chef
- Puppet
- SaltStack

---

### Where did it help me

My datacenter is a yaml file

Create an entire environment with a single command, again and again

+++

Namespace based environment means that multiple environments can coexist without stepping on each other

+++

Ease of environment creation facilitates things like automated regression testing such as a Jenkins pipeline I&T stage that spins up everything and then deletes it

+++

Creating CloudWatch dashboards when creating/deploying a service to the cluster

---

### Syntax

- yaml or json
- template contains
  - parameters - things passed in or defaulted
  - conditions - things to evaluate and set
  - resources - things to create
  - outputs - things to make visible and use elsewhere
- Designer UI can be helpful

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

CloudFormation does not support SecureStrings in the Parameter Store but it is in the AWS queue

+++

Start with an existing set of templates rather than create your own from scratch

+++

stack-updates seem to take a while vs delete/create cycles

---

### Demo

(desktop video goes here)
---

## Links

- [aws.amazon.com/cloudformation/](aws.amazon.com/cloudformation)
- [docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html](docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
- [cloudonaut.io/optional-parameter-in-cloudformation/](cloudonaut.io/optional-parameter-in-cloudformation)
