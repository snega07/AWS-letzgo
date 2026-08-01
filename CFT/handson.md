**Validate:**

aws cloudformation validate-template --template-body file://CFT/EC2.yaml            

**Deploy**

aws cloudformation deploy --template-file file://CFT/EC2.yaml --stack-name my-stack

**Describe event , delete Stack:**

aws cloudformation describe-stack-events --stack-name my-stack  
aws cloudformation wait stack-delete-complete --stack-name my-stack 

**Detect Drift**

aws cloudformation detect-stack-drift --stack-name my-stack -> return drift tection ID

aws cloudformation describe-stack-drift-detection-status \
  --stack-drift-detection-id a1b2c3d4-5678-90ab-cdef-1234567890ab

Resource level drift detection:

aws cloudformation describe-stack-resource-drifts --stack-name my-stack


