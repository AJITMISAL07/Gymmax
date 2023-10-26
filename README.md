
 



# Project Name :Deploying a Gymmax website on Azure using Kubernetes.


### Team Members : 
⦁	Vivek Mane 
⦁	Harshal Sonawane 
⦁	Ajit Misal 



## Case :
In this case we are creating an example website called “Gymmax". Gymmax, a fictitious fitness chain, initially launched in Amsterdam, has been experiencing significant growth and expansion. With new establishments in the USA, Asia, and Africa, Gymmax now faces the challenge of ensuring its website can effectively handle the anticipated increase in web traffic and provide a seamless user experience. 
Gymmax, a rapidly growing fitness chain expanding into the USA, Asia, and Africa, was faced with the challenge of ensuring its website could handle increased web traffic while providing a seamless user experience. To address this challenge, Gymmax opted to host its web application in the Azure cloud using Kubernetes through Azure Kubernetes Service (AKS).
hosting its web application in Azure Kubernetes Service (AKS) demonstrates the effectiveness of using container orchestration and cloud services to address the challenges of managing increased web traffic and delivering a seamless user experience in a global market. This case highlights the importance of leveraging cloud-native technologies and services to support business expansion and digital infrastructure scalability.

## Services Used : 	Kubernetes    :   
Azure Kubernetes Service (AKS) is a managed container orchestration service provided by Microsoft Azure. It simplifies the deployment, management, and scaling of containerized applications using Kubernetes. Azure Kubernetes Service (AKS) offers the quickest way to start developing and deploying cloud-native apps in Azure, datacenters, or at the edge, with built-in code-to-cloud pipelines and guardrails. As a hosted Kubernetes service, Azure handles critical tasks, like health monitoring and maintenance.
Here are some key features and benefits of Azure Kubernetes Service:
Managed Kubernetes: AKS takes care of the complexity of managing Kubernetes clusters, making it easier for developers to focus on building and deploying applications.
Automatic Updates and Scaling: AKS automates tasks such as updating and patching the Kubernetes cluster, as well as scaling applications up or down based on demand.
Integration with Azure Services: AKS seamlessly integrates with various Azure services, including Azure Container Registry, Azure Monitor, andAzure Active Directory, providing a comprehensive ecosystem for containerized applications.
Multi-Region Deployment: You can deploy AKS clusters in multiple Azure regions to ensure high availability and reduce latency for users across the globe.
Security and Compliance: AKS includes features like role-based access control (RBAC), network policies, and Azure Policy integration, enhancing security and compliance.
Resource Management: Azure Kubernetes Service optimizes resource management through auto-scaling and dynamic allocation, reducing operational overhead and cost.
⦁	Load balancer  :     Load balancer
  
With Azure Load Balancer, you can scale your applications and create highly available services. Load balancer supports both inbound and outbound scenarios. Load balancer provides low latency and high throughput, and scales up to millions of flows for all TCP and UDP applications.
⦁	Autoscaling  :      
 

Autoscale allows you to automatically scale your applications or resources based on demand. Use Autoscale to provision enough resources to support the demand on your application without over provisioning and incurring unnecessary costs.
If your application demand increases, the load on the VM instances in your scale set increases. If this increased load is consistent, rather than just a brief demand, you can configure autoscale rules to increase and decrease the number of VM instances in the scale set.

## Nodes : 
In a Kubernetes cluster, nodes serve as the underlying infrastructure where your application containers run. These nodes can be either physical machines or virtual machines (VMs), depending on your deployment and infrastructure choices. 
Nodes can be added or removed from a Kubernetes cluster to scale applications and services up or down based on demand. This flexibility is one of the key advantages of Kubernetes, allowing for efficient resource utilization and effective management of containerized applications.

## Pods : 
This is essentially the smallest deployable unit of the Kubernetes ecosystem, more accurately, it's the smallest object. A pod specifically represents a group of one or more containers running together on your cluster.
Pods are the atomic unit of deployment in Kubernetes and serve as the smallest building block for your applications. They encapsulate one or more containers, allowing for efficient resource sharing and simplifying the deployment and management of containerized applications in a Kubernetes cluster.

Go to Portal  Kubernetes Service  Create
![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/baf55ef6-f52a-4acc-a193-a0c59e739647)

 
Click on Create Kubernetes cluster
 ![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/033ae4bb-a486-4e1c-b4b9-2e1574efa246)


Create Kubernetes cluster
 ![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/8577e210-b188-41a6-9316-24e1a7177764)

Click on Node pools  agentpool
 ![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/52e61967-195e-444d-8c6a-0b4ca32796e4)



change in Maximum node count  Now click on Updata Review & create
 ![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/752e9038-c6d0-4bdf-b504-776f5215b2f7)


Go to resources 
 
![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/ad4d1367-1867-472b-85c3-a70b7dacc99b)

Click on Connect to cluster 
 ![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/411f5972-7dee-4313-b2ba-831aa126869e)

 open cloud shell Create Storage 
 ![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/e00cd0bd-4f2a-4a61-8cf2-feabc45f95b7)



Copy Commands and paste in Connect To GymCluster 
 
1) az account set --subscription af414ca9-fb64-4515-8e26-7d9abe44ffa6
![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/f9d52b93-dccc-412e-a9b3-a4d9ddf13a94)

 
2)az aks get-credentials --resource-group APrg --name GymCluster
 
3) kubectl get deployments --all-namespaces=true
 
Once you have run the command above to connect to the cluster, you can run any kubectl commands. Here are a few examples of useful commands you can try.


Enter Command  nano webdep.yaml  Enter 
 
 Enter yaml code  ctrl x & ctrl y  Enter
 








# Enter Yaml Code :  Using this yaml file we  pull image from Docker & created pod in cluster 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: gymmax
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gymmax
  template:
    metadata:
      labels:
        app: gymmax
    spec:
      containers:
      - name: gymmax
        image: mylandmax/kubwebtest:v3
        ports:
        - containerPort: 80
        resources:
          requests:
          # 1000 milicore = 1 core. 1 node has 1 core.
          # For that reason, 5 pods with 250 milicore isn't possible on 1 node. The pod remains pending. 
            cpu: "250m"

Enter Command  kubectl apply -f webdep.yaml
 

Enter  kubectl get pods
 

Enter  nano gymmax-load.yaml 
 
















# Enter yaml code : 
To Make Pod our available to public we did it using following yaml Which is the load balancer which gives External IP so it will reachable to public
apiVersion: v1
kind: Service
metadata:
  name: service-gymmax
  labels:
    app: service-gymmax
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: gymmax
Enter kubectl apply -f gymmax-load.yaml
 

Enter Command to get External -IP   kubectl get services
 


Here we copy EXTERNAL -IP and paste in another browser tab  our web page get load
 

We enter 3rd yaml to which we use to configure Autoscaling on our cluster
Enter yaml code  
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: gymmax-autoscale
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: gymmax
  minReplicas: 1
  maxReplicas: 8
targetCPUUtilizationPercentage: 80
Enternano autoscale.yaml—Entering the 3rd CodeCtrl X& Ctrl y Enter
 
 


Apply Command : kubectl apply -f autoscale.yaml
 

To test Horizontal scaling is working Use command  kubectl get hpa : 
 




If  VM get targeted load it will automatically get replicated 

# Final Output : 
![image](https://github.com/AJITMISAL07/Gymmax/assets/125040768/667a6abd-1120-491c-b061-6623f763ed36)


## Conclusion : 

Now we can see Our Gymmax web page is successfully uploaded on azure using Kubernetes 


## Services we Use :
Load balancer
Auto Scaling 
Kubernetes 





















