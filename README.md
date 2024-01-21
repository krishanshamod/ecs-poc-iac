# ECS-PoC-IaC Repository

This repository serves as a comprehensive Infrastructure as Code (IaC) solution for deploying key AWS resources. Utilizing AWS CloudFormation, these scripts automate the provisioning of a Virtual Private Cloud (VPC), Amazon ECS cluster, RDS database, Bastion Host, S3 bucket, and ECR repository.

## Infrastruture Diagram

![Infrastructure Diagram](https://github.com/krishanshamod/ecs-poc-iac/blob/main/assets/infrastructure_diagram.png?raw=true)

## Obtaining Bastion Host Private Key

To retrieve the Bastion host private key securely, use the following command:

```bash
aws ssm get-parameter --name /ec2/keypair/<key-pair-id> --with-decryption --query Parameter.Value --output text > bastion-host-key.pem
```

Replace `<key-pair-id>` with the actual ID of your key pair. This command uses AWS Systems Manager (SSM) to securely fetch the private key parameter and save it locally as bastion-host-key.pem. Ensure that you have the AWS CLI configured with the necessary permissions.

## Accessing the Database Locally

### Prerequisites

1. Ensure that you have AWS CLI installed on your local machine, and the version is higher than 2.12.0.
2. Ensure that you have configured the AWS CLI with the user or role that have following permissions:
   - `ssm:GetParameter`
   - `ec2:DescribeInstances`
   - `ec2:DescribeInstanceConnectEndpoints`
   - `ec2-instance-connect:OpenTunnel`
3. Ensure that you have the Bastion host private key file (bastion-host-key.pem) in the current directory.

### SSH Port Forwarding

To access the database securely from your local machine, you can use the following SSH port forwarding command:

```bash
ssh -N -L 5432:<database-endpoint>:5432 -p 22 -i bastion-host-key.pem -o ProxyCommand='aws ec2-instance-connect open-tunnel --instance-id %h' ec2-user@<instance-id>
```

Replace the placeholders:

- `database-endpoint`: Replace with the actual AWS RDS endpoint.
- `instance-id`: Replace with the instance ID of your Bastion host.

This command establishes an SSH tunnel using EC2 instance connect endpoint and forwarding the database port from the AWS RDS endpoint through the Bastion host to your local machine. After running this command, you can connect to the database on your local machine as if it were running on `localhost`.
