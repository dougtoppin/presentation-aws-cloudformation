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

+++

No other tools to learn, install, configure and support

---
### Related tools

- ansible
- Terraform
- Chef
- Puppet
- SaltStack

---

### Where did it help me

---

### Syntax

---
### Nested Stacks

Don't make one giant template

Group related/hierarchical resources into templates

---

### Examples

[github.com/awslabs/ecs-refarch-cloudformation](github.com/awslabs/ecs-refarch-cloudformation)

---

### Lessons learned

deleting stacks

Sometimes a stack delete will fail, you might need to try it again or do a manual delete of a resource

+++

Don't hardcode stuff in your templates

Use the Parameter Store to centralize settings

+++

Does not support SecureStrings in the Parameter Store

+++

Start with an existing set of templates rather then create your own from scratch

---

### Demo

(desktop video goes here)
---

## Links

- [aws.amazon.com/cloudformation/](aws.amazon.com/cloudformation)
- [docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html](docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
- [cloudonaut.io/optional-parameter-in-cloudformation/](cloudonaut.io/optional-parameter-in-cloudformation)
