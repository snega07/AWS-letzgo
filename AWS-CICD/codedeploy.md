create application in Codedeploy -> attach service role to communicate with codedeploy from ec2 and wise versa add this role to both code deploy and ec2 whewre we want to deploy -> create deployment group(add role to communicate with ec2) and the instance tag which EC2 instance it must deploy to.

deployment config done in -> appspec.yaml

Install agent in ec2 -> codedeploy

Add Code deploy to code pipeline > post install, pre install