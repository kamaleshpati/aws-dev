* Install AWS CLI
    # Download Binary
    curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"

    # Install the binary
    sudo installer -pkg ./AWSCLIV2.pkg -target /

    # check
    aws --version
    aws-cli/2.0.7 Python/3.7.4 Darwin/19.4.0 botocore/2.0.0dev11

    which aws

    # set creds
    ============
    aws configure

    # list ec2
    =============
    aws ec2 describe-vpcs

* Install kubectl CLI

    # Download the Package
    mkdir kubectlbinary
    cd kubectlbinary
    curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/darwin/amd64/kubectl

    # Provide execute permissions
    chmod +x ./kubectl

    # Set the Path by copying to user Home Directory
    mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
    echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile

    # Verify the kubectl version
    kubectl version --short --client
    Output: Client Version: v1.16.8-eks-e16311

* Install eksctl CLI

    # Install Homebrew on MacOs
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

    # Install the Weaveworks Homebrew tap.
    brew tap weaveworks/tap

    # Install the Weaveworks Homebrew tap.
    brew install weaveworks/tap/eksctl

    # Verify eksctl version
    eksctl version




# All the access policy
=======================
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "iam:CreateInstanceProfile",
                "iam:DeleteInstanceProfile",
                "iam:GetRole",
                "iam:GetInstanceProfile",
                "iam:RemoveRoleFromInstanceProfile",
                "iam:CreateRole",
                "iam:DeleteRole",
                "iam:AttachRolePolicy",
                "iam:PutRolePolicy",
                "iam:ListInstanceProfiles",
                "iam:AddRoleToInstanceProfile",
                "iam:ListInstanceProfilesForRole",
                "iam:PassRole",
                "iam:DetachRolePolicy",
                "iam:DeleteRolePolicy",
                "iam:GetRolePolicy"
            ],
            "Resource": [
                "arn:aws:iam::254359228911:instance-profile/eksctl-*",
                "arn:aws:iam::254359228911:role/eksctl-*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "cloudformation:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "eks:*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:DescribeLaunchConfigurations",
                "autoscaling:DescribeScalingActivities",
                "autoscaling:CreateLaunchConfiguration",
                "autoscaling:DeleteLaunchConfiguration",
                "autoscaling:UpdateAutoScalingGroup",
                "autoscaling:DeleteAutoScalingGroup",
                "autoscaling:CreateAutoScalingGroup"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "ec2:DeleteInternetGateway",
            "Resource": "arn:aws:ec2:*:*:internet-gateway/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:DeleteSubnet",
                "ec2:DeleteTags",
                "ec2:CreateNatGateway",
                "ec2:CreateVpc",
                "ec2:AttachInternetGateway",
                "ec2:DescribeVpcAttribute",
                "ec2:DeleteRouteTable",
                "ec2:AssociateRouteTable",
                "ec2:DescribeInternetGateways",
                "ec2:CreateRoute",
                "ec2:CreateInternetGateway",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:CreateSecurityGroup",
                "ec2:ModifyVpcAttribute",
                "ec2:DeleteInternetGateway",
                "ec2:DescribeRouteTables",
                "ec2:ReleaseAddress",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:DescribeTags",
                "ec2:CreateTags",
                "ec2:DeleteRoute",
                "ec2:CreateRouteTable",
                "ec2:DetachInternetGateway",
                "ec2:DescribeNatGateways",
                "ec2:DisassociateRouteTable",
                "ec2:AllocateAddress",
                "ec2:DescribeSecurityGroups",
                "ec2:RevokeSecurityGroupIngress",
                "ec2:DeleteSecurityGroup",
                "ec2:DeleteNatGateway",
                "ec2:DeleteVpc",
                "ec2:CreateSubnet",
                "ec2:DescribeSubnets"
            ],
            "Resource": "*"
        }
    ]
}


# Create Cluster
eksctl create cluster --name=kamalesheksdemo1 \
                      --region=us-east-1 \
                      --zones=us-east-1a,us-east-1b \
                      --without-nodegroup 

# delete stack
aws --region us-east-1 cloudformation delete-stack --stack-name eksctl-kamalesheksdemo1-cluster

# delete cluster
eksctl delete cluster --region=us-east-1 --name=kamalesheksdemo2 


# get nodes
kubectl get nodes
//// it is no nodes in this case --No resources found

# get cluster 
eksctl get clusters

# add iam-oidc-provider
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster kamalesheksdemo3 \
    --approve

create aws key pair using UI : `kamalesh-kube-demo`

# Create Node Group with additional Add-Ons in Public Subnets
eksctl create nodegroup --cluster=kamalesheksdemo3 \
                       --region=us-east-1 \
                       --name=kamalesheksdemo3-ng-public1 \
                       --node-type=t3.medium \
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=kamalesh-kube-demo \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access 

eksctl get nodegroup --cluster=kamalesheksdemo3

eksctl delete cluster kamalesheksdemo3
eksctl delete nodegroup --cluster=kamalesheksdemo3 --name=kamalesheksdemo3-ng-public1ß