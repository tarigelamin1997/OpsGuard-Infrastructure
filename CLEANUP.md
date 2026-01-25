🗑️ Cleanup Protocol: How to Destroy the Stack ($0 Bill)
To avoid incurring costs after testing, follow this strict teardown sequence.

Step 1: Empty the S3 Buckets CloudFormation cannot delete an S3 bucket if it contains files (even logs). You must empty them first.

Go to the S3 Console.

Find the bucket named opsguard-logs-... and opsguard-templates-....

Select all objects → Delete.

Alternative CLI Command:

Bash
aws s3 rm s3://YOUR-BUCKET-NAME --recursive
Step 2: Delete the Master Stack

Go to the CloudFormation Console.

Select the OpsGuard-Master stack (the parent).

Click Delete.

Watch: You will see it automatically delete the Compute stack, then Security, then Network (Reverse dependency order).

Step 3: The "Zombie Hunter" Check CloudWatch Log Groups and EFS Backups sometimes persist. Check these locations:

CloudWatch Log Groups: Search for /aws/ec2/OpsGuard and delete the group.

EFS File Systems: Go to EFS Console -> Delete any file system with "OpsGuard" tag (only if you removed the deletion policy).

EBS Snapshots: Go to EC2 -> Snapshots -> Delete any backups created during the run.