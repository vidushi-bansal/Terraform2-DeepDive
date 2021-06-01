# Module1: Updating Existing Terraform resources  
This module is all about importing resources because brownfield is really the norm, when it comes to Terraform. We might walk into an environment that already has resources deployed.  
We are using aws provider in our configuration.   
Set the access key and secret key for aws as:    
```
aws configure  

AWS Access Key ID:  
AWS Secret Access Key:  
Default region name:  
Default output format:  
```

```
terraform plan
terraform apply
```  
  
![First](https://github.com/vidushi-bansal/Terraform2-DeepDive/blob/main/Module1:Existing-Resources/First.png)  
  
Now that we have our basic environment set up, we want to add a public and private subnet to the us-east-1-c availability zone.  
  
![Second](https://github.com/vidushi-bansal/Terraform2-DeepDive/blob/main/Module1:Existing-Resources/Second.png)  
  
Someone did this by running the scripts (here we are going to run **script.sh** to make those changes), but we need this to be managed by the terraform. Instead of deleting what is already spinned up, we will try to add those resources in our terraform state such that they can be managed by the terraform. The way that we are going to do that is by using the import command in terraform. There is no automatic import so we have to update our configuration within terraform, and then manually import the resources we want terraform to manage.    
Step1. Update the configuration to include resources we want to import.  
Step2. Match up the identifiers from our provider with the identifiers within our configuration.  
Step3. Add new resources (the resources we are bringing under management) to our state data.  
  
### Import Command  
```  
terraform import [options] ADDR ID  
```  
  
ADDR - configuration resource identifier. Its the way that terraform is indentifying that resource inside its own configuration.    
Example: module.vpc.aws_subnet.public[2]    
  
ID - provider specific resource identifier  
Example: subnet-ad536afg9  
  
Once we have the ADDR and ID, we can run the import command in terraform as:    
```  
terraform import -var-file="terraform.tfvars" \
   module.vpc.aws_subnet.public[2] subnet-ad536afg9    
```  
So, in order to add these subnet,s route tables and route table associates into our configuration, we have to make some changes to the tfvars file. Uncomment the values from the tfvars file for private subnets, public subnets and subnet count. Now we have the third private and public subnet and our subnet count is set to three. Comment out the values that we had setup before. Our values are now updated to have three subnets.  
Run terraform plan and check the latest change that are specified by terraform.  
module.vpc.aws_route_table.private[2] will be created  
module.vpc.aws_route_table_association.private[2] will be created  
module.vpc.aws_route_table_association.public[2] will be created  
module.vpc.aws_subnet.private[2] will be created  
module.vpc.aws_subnet.public[2] will be created  
  
These are the addresses used by the terraform for creating the resources. We can use that in the import command instead of applying this plan.  
Run the commands mentioned in the **import-commands.txt**. After all the resources get imported, run the terraform plan again. You will notice that there are no resources that are needed to be added. However, there are 3 resources that are needed to be changed. Those changes are related to the tags, because subnets were created without proper tagging, so terraform is now going to apply those tags in the consistent way.  