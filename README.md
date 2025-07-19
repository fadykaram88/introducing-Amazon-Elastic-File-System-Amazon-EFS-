# Project 4 – Introducing Amazon Elastic File System (Amazon EFS)

## Task 1: Creating a Security Group to Access Your EFS File System

1. In the AWS Console, search for **VPC** and select **Your VPCs** > click **Create VPC**.

   - (Tip: Steps from Project 3 can help here)

2. After creating the VPC, go to **Security Groups** and create a new one:

   - **Security Group Name**: `EFSClient`
   - Save the **Security Group ID** to a text editor (we will use it later).

3. Create another security group:

   - **Security Group Name**: `EFS Mount Target`
   - **Description**: `Inbound NFS access from EFS clients`
   - **VPC**: Select the one you just created.

4. In **Inbound Rules**, click **Add Rule**:

   - **Type**: `NFS`
   - **Source**: Paste the **Security Group ID** you saved earlier.

---

## Task 2: Create the EFS File System

5. In the AWS Console, search for **EFS** > click **Create File System**.

6. Choose **Customize**.

7. Uncheck **Enable automatic backups**.

8. Under **Lifecycle Management**, for **Transition into infrequent access (IA)**, select `None`.

9. Under **Tags** (optional):

   - **Key**: `Name`
   - **Value**: `My First EFS File System`

10. Click **Next**.

11. For **VPC**, choose the VPC you just created.

12. For **Security Group**:

- Remove the default
- Select only `EFSClient`

13. Click **Next**, then **Next** again, then **Create**.

---

## Task 3: Launch EC2 Instance to Access the EFS

14. In the AWS Console, search **EC2** > click **Launch Instance**:

- **Name**: `EFS Client`
- **App and OS Image**: Amazon Linux AWS 2023 kernel-6.1 AMI
- **Instance Type**: `t3.micro`

15. Under **Key Pair (Login)**:

- Click **Create new key pair**
- **Name**: `Vockey`
- **Key Pair Type**: RSA
- **Format**: `.pem`
- Click **Create key pair**

16. Under **Network Settings**:

- Click **Edit**
- **VPC**: Select the one you created
- **Subnet**: Public subnet
- **Auto-assign Public IP**: Enable
- **Security Group**: Select existing and choose only `EFSClient`

17. Click **Launch Instance**.
18. After launching, go to the instance > **Connect** > **Session Manager** > **Connect**.

---

## Task 4: Mount the EFS File System to EC2

19. In the terminal (Session Manager), run:

```bash
sudo su -l ec2-user
sudo yum install -y amazon-efs-utils
sudo mkdir efs
```

20. Go back to the AWS Console > EFS > `My First EFS File System` > click **Attach**.

21. Under **Using the NFS client**, copy the command. Example:

```bash
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-xxxxxxxx.efs.us-east-1.amazonaws.com:/ efs
```

22. Paste it in the EC2 terminal and wait for it to complete.

23. To check the mounted file system:

```bash
sudo df -hT
```

---

## Task 5: Measure Performance Behavior Using Flexible IO

24. To benchmark the EFS file system, run:

```bash
sudo fio --name=fio-efs --filesize=10G --filename=./efs/fio-efs-test.img \
--bs=1M --nrfiles=1 --direct=1 --sync=0 --rw=write \
--iodepth=200 --ioengine=libaio
```

25. Wait until the command finishes. You will see performance metrics.

---

## ✅ Project Summary:

- VPC created and security groups configured
- Amazon EFS deployed and attached to EC2
- EC2 instance connected and EFS mounted
- Performance tested using FIO

---

## 👋 See you in the next project!
