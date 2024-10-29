# eks-cluster-creation

step 1:

![image](https://github.com/user-attachments/assets/98002a66-b47b-4cdb-b5ca-37cdf5c73c37)


step2:
by default we dont have cluster IAM role, we need to create it 

<img width="912" alt="image" src="https://github.com/user-attachments/assets/683895a6-2497-4fb4-9d33-9f65d2233a62">

![image](https://github.com/user-attachments/assets/1017f272-a8fc-49b6-831f-2959cbb07210)

![image](https://github.com/user-attachments/assets/17a3d1ac-1b89-4b68-b57f-a5303aa844a9)

ste3: click on next > next

enter the role name & tags as per standards & click on create role
![image](https://github.com/user-attachments/assets/d706d771-920c-4c6d-80fa-cc2914fcc545)

![image](https://github.com/user-attachments/assets/d295d09c-148c-4b8d-afe7-a6847ec675d3)

step 4: attach additional policies required for the role
![image](https://github.com/user-attachments/assets/594a3725-a722-49e1-adbb-adb26ae4b839)

![image](https://github.com/user-attachments/assets/b71abbee-f46d-40f4-8316-e4205263a092)

step5: select the role created

![image](https://github.com/user-attachments/assets/a8dd0b32-8c0a-4559-9199-d59b6542f4bc)

![image](https://github.com/user-attachments/assets/8b275a51-762d-4f06-a42c-9b3b3c1581b5)

![image](https://github.com/user-attachments/assets/7165b8b9-daab-482e-92b0-445c243cf162)


Add tags as per requirement and select next

![image](https://github.com/user-attachments/assets/6d677165-d244-4c7b-bb77-991c0ea59643)

select the default security group & security group should contain following rules --> ssh 22, http 80, https 443
![image](https://github.com/user-attachments/assets/8cd32f3b-df13-4c6b-ac32-f5769d630f2d)

![image](https://github.com/user-attachments/assets/337b86a5-ff0d-40a8-a88c-49e9b349a9ea)

![image](https://github.com/user-attachments/assets/514032c0-9173-4638-a5a5-bf863d7ec07c)

![image](https://github.com/user-attachments/assets/2d90c3cc-eb6b-453b-9f48-6e3563fb7e07)

![image](https://github.com/user-attachments/assets/498e07bc-15b1-4106-86e7-318a94e640d4)

Remove all addons

![image](https://github.com/user-attachments/assets/97c6d245-a96a-4afc-ad76-9ab99f6ba6bb)


select create cluster

create another role for node groups & bastion
![image](https://github.com/user-attachments/assets/7302a12c-57e0-4f3a-b0c6-fbf56e651b2b)


wait for 10-15 mins
once the cluster is up & running  select compute tab and select add node group

![image](https://github.com/user-attachments/assets/5606f7ce-ce69-4fc8-bfc4-4fba8b7377dd)

enter the nodegroup name & select the role created in the above step
![image](https://github.com/user-attachments/assets/9c965608-ae20-47d2-8cfa-f3a7ca0f0d66)

leave rest of the feilds as same and select next & select AMI type, instance type, node group scaling config
![image](https://github.com/user-attachments/assets/52dd28ba-5f4c-430e-9c43-80d17fa8bb7e)

![image](https://github.com/user-attachments/assets/f208bf27-4c5c-49fe-ac5c-bdd75a156c6b)

select next

![image](https://github.com/user-attachments/assets/896ac245-08bf-40bc-851e-43d6aaacf938)

select next > create
![image](https://github.com/user-attachments/assets/077fbb1c-5041-414e-be62-218af2991978)

##################################################################################################################
Bastion setup to access cluster

spinup a virtual machine (ec2) & dont forget to select the instance profile which we used for node group  & rest all configurations will be as per your requirement

![image](https://github.com/user-attachments/assets/6adb7913-5d68-487b-a533-e32d91fd14b0)

once vm is up & running  login to the vm & install following packages

ec2 login:
select the instance u want to login

![image](https://github.com/user-attachments/assets/190c9c57-9732-49ce-83d8-488d47573b6d)

select ssh client as shown below
![image](https://github.com/user-attachments/assets/92ffaa5c-8b68-48cb-ab2a-69c37bb0e88e)

while creating ec2 u need to download pem key that name should be given in below command

copy example cmd below: ssh -i "eksbastion.pem" ec2-user@ec2-13-239-134-157.ap-southeast-2.compute.amazonaws.com

![image](https://github.com/user-attachments/assets/37a249e3-3c3f-4411-b2ac-7146638dcd0a)

in you laptop
go to cmd and type cd Downloads as shown below, as u have downloaded key their

![image](https://github.com/user-attachments/assets/f889e62f-ddb3-43ed-8156-fc2ee46ead5f)

now run the above cmd which u copied from connect--->ssh-client-->
ssh -i "eksbastion.pem" ec2-user@ec2-13-239-134-157.ap-southeast-2.compute.amazonaws.com


aws cli:

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
./aws/install -i /usr/local/aws-cli -b /usr/local/bin
aws --version
aws eks --region ap-southeast-2 update-kube... by Hemanth Katkoju


helm:

wget https://get.helm.sh/helm-v3.8.0-linux-amd64.tar.gz
 
tar -zxvf helm-v3.8.0-linux-amd64.tar.gz
 
mv linux-amd64/helm /usr/local/bin/helm
 
ln -s /usr/local/bin/helm /bin/helm

kubectl:

cat << EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.28/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.28/rpm/repodata/repomd.xml.key
EOF

yum install kubectl -y

nowuse the command to connect to the eks cluster from your bastion:

aws eks --region (region-where-your-cluster-is-configured) update-kubeconfig --name (your-cluster-name)

now you can use kubectl commands like

kubectl get nodes, kubectl get namesapace, kubectl get po

if you are not able to list any resources --> configure aws in your bastion using below command

aws configure

enter the access key & secret key of the account

and now you will be able to list all the objects in your cluster  


argocd-installation:

kubectl create ns argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

kubectl get svc -n argocd
copy the selected external-ip shown in below image: (a3b8552c14e48466aa1616e71b7725d3-1436231275.ap-southeast-2.elb.amazonaws.com)
![image](https://github.com/user-attachments/assets/059f73b0-d216-4f3e-809c-18a7c9b5d6ec)

http://(external-ip)
http://a3b8552c14e48466aa1616e71b7725d3-1436231275.ap-southeast-2.elb.amazonaws.com

 kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d

 ![image](https://github.com/user-attachments/assets/062b8fd5-d1da-47dc-9e00-120d9e987958)
(dPzpzcHiSrEfTHiY)

![image](https://github.com/user-attachments/assets/9d896091-96b9-4277-9f53-6fe04d9ff3b5)









