# OpsGuard Infrastructure

![Status](https://img.shields.io/badge/Status-Active-success)
![AWS](https://img.shields.io/badge/AWS-CloudFormation-orange)

OpsGuard is a robust, modular Infrastructure-as-Code (IaC) solution built with AWS CloudFormation. It enables the automated deployment of a secure, scalable, and self-healing web application environment.

Currently configured to host a dynamic **Portfolio Website** pulled directly from GitHub.

## 🏗️ Architecture

The infrastructure is orchestrated via a Master Stack (`main.yaml`) which manages three nested stacks:

1.  **Network Stack (`templates/network.yaml`)**
    *   VPC with public and private subnets across two Availability Zones.
    *   Internet Gateway and Route Tables for secure routing.

2.  **Security Stack (`templates/security.yaml`)**
    *   IAM Roles for EC2 (S3 access, SSM, CloudWatch).
    *   Security Groups (Strict firewall rules for ALB and App Servers).
    *   KMS Key for encryption at rest.

3.  **Compute Stack (`templates/compute.yaml`)**
    *   **Auto Scaling Group:** Ensures high availability and self-healing.
    *   **Application Load Balancer:** Distributes traffic to healthy instances.
    *   **Launch Template:** Defines server configuration (Amazon Linux 2).
    *   **UserData Script:** Automatically installs dependencies and clones the application repository on boot.

## 🚀 Deployment

### Prerequisites
*   AWS CLI installed and configured.
*   An S3 bucket for storing CloudFormation templates.
*   A Route 53 Hosted Zone (domain name).
*   EC2 Key Pair.

### Steps

1.  **Upload Templates to S3**
    ```powershell
    aws s3 sync ./templates s3://<your-bucket-name>/templates
    ```

2.  **Deploy Master Stack**
    ```powershell
    aws cloudformation deploy `
      --template-file main.yaml `
      --stack-name OpsGuard-Master `
      --parameter-overrides file://parameters-dev.json `
      --capabilities CAPABILITY_NAMED_IAM
    ```

3.  **Update Application (Zero Downtime)**
    To deploy new code from your Git repository:
    ```powershell
    # 1. Push code to your GitHub repo
    # 2. Trigger an Instance Refresh
    aws autoscaling start-instance-refresh --auto-scaling-group-name OpsGuard-ASG
    ```

## ⚙️ Configuration

Usage is driven by `parameters-dev.json`. Key parameters include:

| Parameter | Description |
| :--- | :--- |
| `S3TemplateBucket` | Bucket name where nested templates are stored. |
| `AppRepositoryUrl` | Git URL of the application to deploy (e.g., your portfolio). |
| `DomainName` | The full domain for the application (e.g., `opsguard.example.com`). |
| `KeyName` | EC2 Key Pair name for SSH access. |

## 📂 Project Structure

```
.
├── main.yaml                # Master CloudFormation Template
├── parameters-dev.json      # Environment-specific parameters
├── templates/
│   ├── network.yaml         # VPC and Networking
│   ├── security.yaml        # IAM, Security Groups, KMS
│   └── compute.yaml         # ASG, ALB, Launch Template
└── CLEANUP.md               # Instructions for destroying resources
```
