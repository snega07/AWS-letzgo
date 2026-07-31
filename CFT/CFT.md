### Cloud Formation template

User submits template which is declarative and versioned by nature.
CFT support either yaml or json templating.
It only create resource in AWS. It is native to AWS.

**Declarative:**

What u see is what u have.

**Versioned**

Has versions like Git which ie easy to rollback.

Yaml or Json:

-> Yaml is easy to understand and we can use comments.

-> Drift detection (If something modified in AWS comsole). CFT will detect drift and we can fix it.

-> we can create stack using this cloud formation template using aws cli or in jenkins pipeline we can use cli commands and do deployment.
-> Stack helps to execute our template and create resources.

create teamplte
use existing template
use template designer -> drag and drop

We can upload the template from local or S3 bucket
By default stores all the CFT in S3
delete the S3 and detect drift
We can select with which IAM user role we need to execute the stack

structure:

version
description
metadata
parameter
rules
mappings
conditions
outputs

Resources

Plugin:

yaml -> RedHat
aws toolkit

create EC2 using CFT