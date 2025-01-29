Deployments support updating images to a new version through a rolling update mechanism. When a deployment is updated with a new version, it creates a new ReplicaSet and slowly increases the number of replicas in the new ReplicaSet as it decreases the replicas in the old ReplicaSet.

![image](https://github.com/user-attachments/assets/69759157-b5f7-4daa-a493-872818ea6240)

# Trigger a rolling update

kubectl edit deployment hello

# Change the image in the containers section of the deployment to the following:

...
containers:
  image: kelseyhightower/hello:2.0.0
...

# View the present replicas :-

kubectl get replicaset

# You can also see a new entry in the rollout history:

kubectl rollout history deployment/hello

# Run the following to pause the rollout:

kubectl rollout pause deployment/hello

# Verify the current state of the rollout:

kubectl rollout status deployment/hello

# You can also verify this on the Pods directly:

kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'

Resume a rolling update

# Continue the rollout using the resume command:
kubectl rollout resume deployment/hello

# When the rollout is complete, you should see the following when running the status command:

kubectl rollout status deployment/hello

##Roll back an update##
Assume that a bug was detected in your new version. Since the new version is presumed to have problems, any users connected to the new Pods will experience those issues. You will want to roll back to the previous version so you can investigate and then release a version that is fixed properly.

# Use the rollout command to roll back to the previous version:

kubectl rollout undo deployment/hello

# Verify the roll back in the history

kubectl rollout history deployment/hello

Finally, verify that all the Pods have rolled back to their previous versions:

kubectl get pods -o jsonpath --template='{range .items[*]}{.metadata.name}{"\t"}{"\t"}{.spec.containers[0].image}{"\n"}{end}'







