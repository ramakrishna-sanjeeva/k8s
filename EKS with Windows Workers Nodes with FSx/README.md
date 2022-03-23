# Amazon EKS with Windows Worker Nodes and Amazon FSx for Windows File Server 

Amazon FSx for Windows File Server will need to be mounted as Windows via Microsoft SMB Protocol for usage. This requires Active Directory Domain Services to be available. 

**Deployment architecture**

![alt text](arch.png "Architecture")


**Following are the steps** 
1. Provision Active Directory Domain Services in a shared services VPC. 

	Leverage Active Directory Domain Services on the AWS Cloud Quick Start Reference Deployment guide available @ https://aws-quickstart.github.io/quickstart-microsoft-activedirectory/ 
	Refer to Deploy scenario 3 (AWS Managed Microsoft AD) into an existing VPC use case for the deployment @ https://aws-quickstart.github.io/quickstart-microsoft-activedirectory/#_deployment_steps 

2. Once deployed, login to the management instance and configure it to be connect to the AD Domain. Refer to https://docs.aws.amazon.com/directoryservice/latest/admin-guide/join_windows_instance.html 

3.  Once Windows EC2 instance joins the domain, login using AD admin credentials and create a service account for usage of FSx Windows File Server.

4. Establish network connectivity between the shared services VPC and the VPC where EKS workloads and FSx Windows File Server are provisioned using either VPC Peering/AWS Transit Gateway. 

5. Provision FSx Windows File Server by specifying the AWS Managed Directory created in above step. 
6. Once the Windows worker nodes has been provisioned, join the EC2 servers by leveraging AWS Systems Manager capability as documented @ https://aws.amazon.com/premiumsupport/knowledge-center/ec2-systems-manager-dx-domain/. Build automation to automatically register the nodes to the AD domain. 

Following steps are documented @ https://aws.amazon.com/blogs/containers/using-amazon-fsx-for-windows-file-server-on-eks-windows-containers/ . Summarizing the key steps. 

8. Create Systems Manager Parameters to store the service account's username and password. Ex: domainUserName and domainUserPassword.
9. Create an IAM policy with access to the Systems Manager Parameters to be retrieved and associate the policy to the IAM role associated with the EKS Windows worker nodes. Refer to execute-mount-iam-policy.json

9. Create a Systems Manager Document with the document as provided in load-fsx-as-mount-on-windows-node.json and review the FSx DNS Name and the mount directory. Execute the document. 
10.Verify whether the Fsx for Windows File Server is mounted. 
11. Deploy sample workload mentioned @  ttps://aws.amazon.com/blogs/containers/using-amazon-fsx-for-windows-file-server-on-eks-windows-containers/ 
