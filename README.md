# Day-61----Introduction-to-Terraform-and-Your-First-AWS-Infrastructure

# Task

You have been deploying containers, writing CI/CD pipelines, and orchestrating workloads on Kubernetes. But who creates the servers, networks, and clusters underneath? Today you start your Infrastructure as Code journey with Terraform -- the tool that lets you define, provision, and manage cloud infrastructure by writing code.

By the end of today, you will have created real AWS resources using nothing but a .tf file and a terminal.

# Expected Output

- Terraform installed and working on your machine
- AWS CLI configured with valid credentials
- An S3 bucket and EC2 instance created and destroyed via Terraform
- A markdown file: day-61-terraform-intro.md

# Challenge Tasks

# Task 1: Understand Infrastructure as Code

Before touching the terminal, research and write short notes on:

1. What is Infrastructure as Code (IaC)? Why does it matter in DevOps?
- Infrastructure as Code means creating and managing cloud resources using code instead of manual steps.
  
---------Why it matters in DevOps-------

- Makes infrastructure automated and fast
- Ensures same setup every time
- Helps teams collaborate using Git
- Reduces manual errors


  
2. What problems does IaC solve compared to manually creating resources in the AWS console?

     ----- Manual AWS work has many issues:

- Repeating same steps again and again
- Easy to make mistakes (wrong config)
-  No proper tracking of changes
- Hard to recreate same environment

 ------ IaC solves this:

- Automation → run code → infra ready
- Consistency → same result every time
-  Version control → track changes in Git
-  Easy scaling → create multiple resources quickly


3. How is Terraform different from AWS CloudFormation, Ansible, and Pulumi?

------ Terraform--------
- Works with multiple cloud providers
- Easy syntax (HCL)
- Best for infrastructure provisioning

------AWS CloudFormation--------
- Works only with AWS
- Uses JSON/YAML (harder to read)
- Deep AWS integration but not flexible

 ------Ansible------
- Mainly for configuration management
- Example: install software, update servers
- Not focused on creating infrastructure

-------Pulumi--------
- Uses programming languages (Python, JS)
- More flexible but needs coding skills
- Terraform is easier for beginners


4. What does it mean that Terraform is "declarative" and "cloud-agnostic"?

Declarative means:

- You tell Terraform what you want, not how to do it

Cloud-agnostic means:

 - The tool works with multiple cloud providers

# Task 2: Install Terraform and Configure AWS

1. Install Terraform:

# Linux (amd64)

  wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
  echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs)        main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
  sudo apt update && sudo apt install terraform

2. Verify:

   terraform -version

3. Install and configure the AWS CLI:

   aws configure

4. Verify AWS access:

   aws sts get-caller-identity

# Task 3: Your First Terraform Config -- Create an S3 Bucket

Create a project directory and write your first Terraform config:

mkdir terraform-basics && cd terraform-basics

Create a file called main.tf with:

1. A terraform block with required_providers specifying the aws provider
2. A provider "aws" block with your region
3. A resource "aws_s3_bucket" that creates a bucket with a globally unique name

Run the Terraform lifecycle:

terraform init      # Download the AWS provider
terraform plan      # Preview what will be created
terraform apply     # Create the bucket (type 'yes' to confirm)



# Task 4: Add an EC2 Instance

In the same main.tf, add:

1. A resource "aws_instance" using AMI ami-0f5ee92e2d63afc18 (Amazon Linux 2 in ap-south-1 -- use the correct AMI for your region)
2. Set instance type to t2.micro
3. Add a tag: Name = "TerraWeek-Day1"
   
Run:

terraform plan      # You should see 1 resource to add (bucket already exists)
terraform apply

# Task 5: Understand the State File

Terraform tracks everything it creates in a state file. Time to inspect it.

1. Open terraform.tfstate in your editor -- read the JSON structure
2. Run these commands and document what each returns:
   
terraform show                          # Human-readable view of current state
terraform state list                    # List all resources Terraform manages
terraform state show aws_s3_bucket.<name>   # Detailed view of a specific resource
terraform state show aws_instance.<name>

3. Answer these questions in your notes:
- What information does the state file store about each resource?

It stores:

1. Resource type (EC2, S3)
2. Resource name (my_ec2, my_bucket)
3. Unique IDs (instance ID, bucket name)
4. Attributes:
5. Public IP
6. ARN
7. Region
8. Tags
9. Current state of infrastructure

- Why should you never manually edit the state file?

  1. You can break Terraform tracking
  2. Resources may go out of sync
  3. Terraform may create/delete wrongly
  4. JSON errors can corrupt file


- Why should the state file not be committed to Git?
 
Because it may contain:

1. Sensitive data (IDs, sometimes secrets)
2. Infrastructure details

Problems:

1. Security risk (data leak)
2. Team conflicts
3.  Corruption if multiple people edit

# Task 6: Modify, Plan, and Destroy

1. Change the EC2 instance tag from "TerraWeek-Day1" to "TerraWeek-Modified" in your main.tf
2. Run terraform plan and read the output carefully:
   - What do the ~, +, and - symbols mean?
   - Is this an in-place update or a destroy-and-recreate?
3. Apply the change
4. Verify the tag changed in the AWS console
5. Finally, destroy everything:
   
  terraform destroy

