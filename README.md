# GKE-Introduction-using-CLI-Several-deployments

In this section we will discuss the GKE from scratch to understand how we can manage cluster using CLI . Then we deep dive into some advance concepts of Deployments with all commands & its comments on end to end basis .

# Activate Cloud Shell :-

# Authorize the CloudShell :-
gcloud auth list

# You can list the project ID with this command:
gcloud config list project

# Set your working Google Cloud zone by running the following command, substituting the local zone as ZONE:

gcloud config set compute/zone ZONE

# Get the sample code for creating and running containers and deployments. Note that the sample code reside under a Cloud Storage Bucket 

gsutil -m cp -r gs://spls/gsp053/orchestrate-with-kubernetes .
cd orchestrate-with-kubernetes/kubernetes

Or to flag the network & specific sub network 

gcloud container clusters create griffin-dev --zone us-east4-b --num-nodes 2 --machine-type e2-standard-4 --network griffin-dev-vpc --subnetwork
 griffin-dev-wp

# Create a cluster with 3 nodes (this will take a few minutes to complete):

gcloud container clusters create bootcamp \
  --machine-type e2-small \
  --num-nodes 3 \
  --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"

###Learn about the deployment object###

Explain the Deployment object 

kubectl explain deployment

You can also see all of the fields using the --recursive option:

kubectl explain deployment --recursive

# To extract the Meta data of the respective Deployment :-

kubectl explain deployment.metadata.name



###Create a deployment###

#Update the deployments/auth.yaml configuration file:

vi deployments/auth.yaml

Check the image details section :-

...
containers:
- name: auth
  image: "kelseyhightower/auth:1.0.0"
...

  apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth
spec:
  replicas: 1
  selector:
    matchLabels:
      app: auth
  template:
    metadata:
      labels:
        app: auth
        track: stable
    spec:
      containers:
        - name: auth
          image: "kelseyhightower/auth:1.0.0"
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
...
Notice how the deployment is creating one replica and it's using version 1.0.0 of the auth container.


# Go ahead and create your deployment object using kubectl create:

kubectl create -f deployments/auth.yaml

# Once you have created the deployment, you can verify that it was created:

kubectl get deployments

# Once the deployment is created, Kubernetes will create a ReplicaSet for the deployment. You can verify that a ReplicaSet was created for the deployment

kubectl get replicasets

# View the Pods that were created as part of the deployment. The single Pod is created by the Kubernetes when the ReplicaSet is created:

kubectl get pods

# Use the kubectl create command to create the auth service:

kubectl create -f services/auth.yaml

# Now, do the same thing to create and expose the hello deployment:

kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml

# And one more time to create and expose the frontend deployment:

kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml

# Connect the Frontend to verify the same 

Interact with the frontend by grabbing its external IP and then curling to it:

kubectl get services frontend

# Connection check
curl -ks https://<EXTERNAL-IP>

# You can also use the output templating feature of kubectl to use curl as a one-liner:-

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`

###Scale a deployment###

# View the Replicas & extract by the following command :-

kubectl explain deployment.spec.replicas

# The replicas field can be most easily updated using the kubectl scale command:

kubectl scale deployment hello --replicas=5

# Verify the number of pods running :-

kubectl get pods | grep hello- | wc -l

# Now scale down the pods 

kubectl scale deployment hello --replicas=3

# Now again verify the number of pods :-
kubectl scale deployment hello --replicas=3








  
