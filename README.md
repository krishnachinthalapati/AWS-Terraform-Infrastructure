# ☁️ AWS Multi-Environment Infrastructure Automation — Terraform

![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=for-the-badge&logo=yaml&logoColor=white)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)

> A hands-on Infrastructure as Code project where I automated the provisioning of complete, production-grade AWS environments using Terraform — replacing manual cloud setup with version-controlled, reproducible infrastructure that spins up in under 15 minutes.

---

## 👋 Why I Built This

Early on, I noticed that manually setting up cloud infrastructure — clicking through the AWS console, configuring security groups, creating VPCs — was slow, error-prone, and impossible to reproduce consistently.

Two engineers setting up the same environment manually would always end up with slightly different configs. That's how bugs appear only in production and not in staging.

I wanted to learn the right way to do it: **Infrastructure as Code.**

So I challenged myself to:
- Provision a full AWS stack from scratch using only Terraform — no console clicking
- Make it work across dev, staging, and production with minimal duplication
- Integrate it into a Jenkins pipeline so infra changes go through the same review process as code
- Add security scanning so misconfigurations get caught before they reach production
- Handle state properly so multiple people can work on the same infrastructure safely

This project completely changed how I think about cloud infrastructure.

---

## 📌 Project Summary

| Item | Detail |
|------|--------|
| **Project Type** | Personal learning project |
| **Cloud Provider** | Amazon Web Services (AWS) |
| **IaC Tool** | Terraform |
| **CI/CD** | Jenkins |
| **Security Scanning** | tfsec / Checkov |
| **State Backend** | AWS S3 + DynamoDB (remote locking) |
| **Environments** | dev / staging / production |
| **Status** | ✅ Active |

---

## 🏗️ What Gets Provisioned

```
┌─────────────────────────────────────────────────────────────────────┐
│                        AWS ACCOUNT                                  │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │                    VPC (10.0.0.0/16)                         │  │
│  │                                                              │  │
│  │  ┌─────────────────────────┐  ┌─────────────────────────┐   │  │
│  │  │    PUBLIC SUBNETS       │  │    PRIVATE SUBNETS      │   │  │
│  │  │    (10.0.1.0/24)        │  │    (10.0.10.0/24)       │   │  │
│  │  │                         │  │                         │   │  │
│  │  │  ┌──────────────────┐   │  │  ┌──────────────────┐   │   │  │
│  │  │  │  Application     │   │  │  │   EC2 Instances  │   │   │  │
│  │  │  │  Load Balancer   │   │  │  │   Auto Scaling   │   │   │  │
│  │  │  │  (ALB)           │   │  │  │   Group          │   │   │  │
│  │  │  └────────┬─────────┘   │  │  └────────┬─────────┘   │   │  │
│  │  └───────────┼─────────────┘  └───────────┼─────────────┘   │  │
│  │              │                             │                 │  │
│  │              └──────────────┬──────────────┘                 │  │
│  │                             │                                │  │
│  │                             ▼                                │  │
│  │              ┌──────────────────────────┐                    │  │
│  │              │   DATABASE SUBNET        │                    │  │
│  │              │   (10.0.20.0/24)         │                    │  │
│  │              │                          │                    │  │
│  │              │  ┌────────────────────┐  │                    │  │
│  │              │  │  RDS PostgreSQL     │  │                    │  │
│  │              │  │  (Multi-AZ in prod) │  │                    │  │
│  │              │  └────────────────────┘  │                    │  │
│  │              └──────────────────────────┘                    │  │
│  │                                                              │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │  │
│  │  │   S3 Buckets │  │  IAM Roles   │  │  Security    │       │  │
│  │  │   + Policies │  │  + Policies  │  │  Groups      │       │  │
│  │  └──────────────┘  └──────────────┘  └──────────────┘       │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │                  OBSERVABILITY                               │  │
│  │   CloudWatch Alarms  ←→  SNS Topics  ←→  Email / Slack      │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │                  REMOTE STATE                                │  │
│  │   S3 Bucket (terraform.tfstate)  +  DynamoDB (state lock)   │  │
│  └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🔧 Tech Stack

| Category | Tool | Purpose |
|----------|------|---------|
| **Infrastructure as Code** | Terraform | Provision all AWS resources |
| **Cloud Provider** | AWS | VPC, EC2, RDS, S3, IAM, ALB, ASG |
| **CI/CD** | Jenkins | Automate terraform plan + apply |
| **Security Scanning** | tfsec + Checkov | Catch misconfigs before apply |
| **State Management** | S3 + DynamoDB | Remote state with locking |
| **Alerting** | CloudWatch + SNS | Infrastructure health alerts |
| **Version Control** | Git | All infra changes tracked and reviewed |

---

## 📂 Project Structure

```
aws-terraform-infrastructure/
│
├── modules/
│   ├── vpc/
│   │   ├── main.tf          # VPC, subnets, route tables, IGW, NAT
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── compute/
│   │   ├── main.tf          # EC2, Auto Scaling Group, Launch Template
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── database/
│   │   ├── main.tf          # RDS PostgreSQL, subnet group, parameter group
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── storage/
│   │   ├── main.tf          # S3 buckets, lifecycle policies, versioning
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── iam/
│   │   ├── main.tf          # IAM roles, policies, instance profiles
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── loadbalancer/
│   │   ├── main.tf          # ALB, target groups, listeners, health checks
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── monitoring/
│       ├── main.tf          # CloudWatch alarms, SNS topics, dashboards
│       ├── variables.tf
│       └── outputs.tf
│
├── environments/
│   ├── dev/
│   │   ├── main.tf          # Calls all modules with dev config
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   └── terraform.tfvars # Dev-specific values
│   ├── staging/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   │   └── terraform.tfvars
│   └── production/
│       ├── main.tf
│       ├── variables.tf
│       ├── outputs.tf
│       └── terraform.tfvars
│
├── backend/
│   ├── main.tf              # S3 + DynamoDB for remote state bootstrap
│   └── terraform.tfvars
│
├── jenkins/
│   └── Jenkinsfile          # Pipeline: plan on PR, apply on merge
│
└── README.md
```

---

## ⚙️ Terraform Modules — Key Code

### VPC Module

```hcl
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name        = "${var.project}-${var.environment}-vpc"
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}

resource "aws_subnet" "public" {
  count             = length(var.public_subnet_cidrs)
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.public_subnet_cidrs[count.index]
  availability_zone = var.availability_zones[count.index]

  map_public_ip_on_launch = true

  tags = {
    Name = "${var.project}-${var.environment}-public-${count.index + 1}"
  }
}
```

### Environment Variables per tier

```hcl
# dev/terraform.tfvars
environment      = "dev"
instance_type    = "t3.micro"
min_size         = 1
max_size         = 2
db_instance_class = "db.t3.micro"
multi_az         = false
backup_retention = 1

# staging/terraform.tfvars
environment      = "staging"
instance_type    = "t3.small"
min_size         = 1
max_size         = 3
db_instance_class = "db.t3.small"
multi_az         = false
backup_retention = 3

# production/terraform.tfvars
environment      = "production"
instance_type    = "t3.medium"
min_size         = 2
max_size         = 10
db_instance_class = "db.t3.medium"
multi_az         = true
backup_retention = 7
```

### Remote State Backend

```hcl
terraform {
  backend "s3" {
    bucket         = "krishna-terraform-state"
    key            = "environments/production/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "terraform-state-lock"
    encrypt        = true
  }
}
```

### CloudWatch Alarms Built Into IaC

```hcl
resource "aws_cloudwatch_metric_alarm" "high_cpu" {
  alarm_name          = "${var.environment}-high-cpu"
  comparison_operator = "GreaterThanThreshold"
  evaluation_periods  = 2
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = 300
  statistic           = "Average"
  threshold           = 80

  alarm_actions = [aws_sns_topic.alerts.arn]

  dimensions = {
    AutoScalingGroupName = aws_autoscaling_group.main.name
  }
}
```

---

## 🚀 CI/CD Pipeline — Jenkins + Terraform

Every infrastructure change goes through a pipeline — just like application code.

```groovy
pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        TF_ENV = "${params.ENVIRONMENT}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/krishnachinthalapati/aws-terraform-infrastructure.git'
            }
        }

        stage('Security Scan') {
            steps {
                sh 'tfsec ./environments/${TF_ENV} --format lovely'
                sh 'checkov -d ./environments/${TF_ENV} --quiet'
            }
        }

        stage('Terraform Init') {
            steps {
                dir("environments/${TF_ENV}") {
                    sh 'terraform init'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir("environments/${TF_ENV}") {
                    sh 'terraform plan -var-file=terraform.tfvars -out=tfplan'
                }
            }
        }

        stage('Approval') {
            when {
                expression { TF_ENV == 'production' }
            }
            steps {
                input message: 'Review the plan above. Approve to apply to production?'
            }
        }

        stage('Terraform Apply') {
            steps {
                dir("environments/${TF_ENV}") {
                    sh 'terraform apply tfplan'
                }
            }
        }
    }

    post {
        failure {
            slackSend channel: '#infra-alerts',
                      message: "Terraform pipeline FAILED for ${TF_ENV} environment"
        }
        success {
            slackSend channel: '#infra-alerts',
                      message: "Terraform apply SUCCESS for ${TF_ENV} environment"
        }
    }
}
```

**Key design decisions in this pipeline:**
- `tfsec` and `Checkov` run BEFORE plan — misconfigs are caught early
- `terraform plan` output is saved and reused in `apply` — no drift between what you approved and what runs
- Production requires manual approval — a human always reviews before prod changes apply
- Slack notifications on success and failure — full visibility into infra changes

---

## 🔒 Security Scanning with tfsec

I added `tfsec` to catch common AWS misconfigurations before they ever reach production:

```bash
tfsec ./environments/production
```

**Examples of issues tfsec catches:**
| Check | What it catches |
|-------|----------------|
| `aws-s3-no-public-access` | S3 buckets accidentally set to public |
| `aws-ec2-no-public-ingress-sgr` | Security groups open to 0.0.0.0/0 |
| `aws-rds-encrypt-instance-storage-data` | Unencrypted RDS instances |
| `aws-iam-no-policy-wildcards` | IAM policies with `*` actions |
| `aws-cloudwatch-log-group-customer-key` | Unencrypted CloudWatch logs |

---

## 🛠️ How to Use This Project

### Prerequisites
- AWS CLI configured (`aws configure`)
- Terraform >= 1.5.0
- AWS account with appropriate IAM permissions

### Step 1 — Bootstrap remote state (do this once)

```bash
cd backend
terraform init
terraform apply
```

### Step 2 — Deploy dev environment

```bash
cd environments/dev
terraform init
terraform plan -var-file="terraform.tfvars"
terraform apply -var-file="terraform.tfvars"
```

### Step 3 — Check what was created

```bash
# See all outputs (VPC ID, ALB DNS, RDS endpoint etc.)
terraform output

# Verify in AWS CLI
aws ec2 describe-vpcs --filters "Name=tag:Environment,Values=dev"
aws rds describe-db-instances
```

### Step 4 — Destroy when done (saves cost)

```bash
terraform destroy -var-file="terraform.tfvars"
```

---

## 📊 Results

| Metric | Manual Setup | With Terraform | Improvement |
|--------|-------------|----------------|-------------|
| Environment spin-up time | 2–3 hours | < 15 minutes | ~90% faster |
| Config consistency across envs | ❌ Always drift | ✅ Identical | 0 drift |
| Infra changes tracked in Git | ❌ No | ✅ Yes | Full audit trail |
| Security misconfigs caught | ❌ Manual review | ✅ Automated scan | Before every apply |
| New engineer onboarding | Days of setup | 1 command | Instant |

---

## 💡 What I Learned

**Terraform fundamentals**
- Modules are the key to reusability — writing one VPC module that works across all environments with different variables saved hundreds of lines of duplicated code.
- `terraform plan` is your best friend — always read it carefully before applying. I once almost deleted a production RDS instance because I missed a resource rename.
- Output values in modules are how you pass data between modules — for example, the VPC module outputs the `vpc_id` which the compute module uses as input.

**Remote state — why it matters**
- The first time I ran `terraform apply` from two terminals simultaneously on local state, both runs succeeded but left the state file corrupted. Remote state with DynamoDB locking prevents exactly this.
- State files contain sensitive data (RDS passwords, etc.) — S3 encryption is not optional.

**Security as code**
- Running `tfsec` before every apply caught a misconfigured security group I had open to `0.0.0.0/0` — the kind of mistake that causes data breaches.
- IAM least-privilege is hard to get right the first time. I started too permissive and gradually tightened policies as I understood what each service actually needed.

**CI/CD for infrastructure**
- Treating infra like code — PRs, reviews, pipelines — feels strange at first but becomes obviously correct once you've done it. You get a full audit trail of every change ever made to your infrastructure.
- The manual approval gate for production was something I added after accidentally applying a staging config to the wrong environment. One extra click saves a lot of headaches.

---

## 🗺️ What I'm Working on Next

- [ ] Add Terragrunt to reduce boilerplate across environments
- [ ] Implement AWS Config rules for continuous compliance monitoring
- [ ] Set up cost estimation with Infracost in the Jenkins pipeline
- [ ] Explore AWS Control Tower for multi-account governance
- [ ] Add drift detection — alert when real infra diverges from Terraform state

---

## 📚 Resources That Helped Me

- [Terraform Official Docs](https://developer.hashicorp.com/terraform/docs)
- [AWS Provider Registry](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [tfsec Documentation](https://aquasecurity.github.io/tfsec/)
- [Checkov Documentation](https://www.checkov.io/1.Welcome/What%20is%20Checkov.html)
- [Terraform Best Practices](https://www.terraform-best-practices.com/)

---

## 👤 About Me

**Krishna Chinthalapati**
Passionate about DevOps, cloud infrastructure, and automating everything that can be automated.
Currently looking for internship opportunities in DevOps / Cloud / SRE.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/krishnachinthalapati)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/krishnachinthalapati)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

*Built after spending 3 hours manually setting up a VPC and thinking — there has to be a better way. There was.* 😄
