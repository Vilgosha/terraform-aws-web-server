# terraform-aws-web-server

Terraform project that provisions a highly available web server on AWS — ALB + Auto Scaling Group across two availability zones, with a dynamic Launch Template and Apache bootstrapped via user data.

Built while studying for the Terraform Associate certification.

## What it deploys

```
Internet
    │
    ▼
[ ALB ] ── eu-north-1
    │
    ├──▶ [ EC2 ] AZ1  (Amazon Linux 2, Apache)
    └──▶ [ EC2 ] AZ2  (Amazon Linux 2, Apache)
```

- **Application Load Balancer** — distributes traffic across both instances
- **Auto Scaling Group** — keeps exactly 2 instances running (one per AZ)
- **Launch Template** — t3.micro, latest Amazon Linux 2 AMI, resolved dynamically
- **Security Group** — ports 80 and 443 open, built with a dynamic block
- **user_data.sh** — installs Apache, generates an index page showing the instance's private IP
- **Default tags** — `Owner` and `CreatedBy` applied to all resources automatically

## Usage

```bash
# Configure AWS credentials first
aws configure

terraform init
terraform apply
```

After apply, Terraform outputs the ALB DNS URL. Open it in your browser and you'll see which instance handled the request.

```bash
terraform destroy   # clean up everything
```

## Things worth noting

The AMI is resolved at runtime using a `data` source — no hardcoded AMI IDs. The ASG name includes the Launch Template version, so any change to the template triggers a clean replacement cycle (`create_before_destroy = true`).

## Stack

Terraform · AWS EC2 · ALB · Auto Scaling · Amazon Linux 2
