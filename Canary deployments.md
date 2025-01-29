Canary Deployment is the type of deployment on where new version of application we tested on a subset of users . This activity can be performed on parallel basis as v1 & v2 can be proportionate as 80% & 20% or 90% & 10% respectively .
A canary deployment consists of a separate deployment with your new version and a service that targets both your normal, stable deployment as well as your canary deployment.

![image](https://github.com/user-attachments/assets/c84ae4bd-9f0d-424f-916a-2b1af53d2d4c)

# First, create a new canary deployment for the new version:

cat deployments/hello-canary.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-canary
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
        track: canary
        # Use ver 2.0.0 so it matches version on service selector
        version: 2.0.0
    spec:
      containers:
        - name: hello
          image: kelseyhightower/hello:2.0.0
          ports:
            - name: http
              containerPort: 80
            - name: health
              containerPort: 81
...

# Next apply the changes :-

kubectl create -f deployments/hello-canary.yaml

# After the canary deployment is created, you should have two deployments, hello and hello-canary. Verify it with this kubectl command

kubectl get deployments

# You can verify the hello version being served by the request:

curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version

** Please run the above command thrice or more than that to verufy how the traffic shifting is happening from v1 to v2 .

###Canary deployments in production - session affinity###

In case of an Ecommerce application post upgrade into V2 . Users now both access v1 & v2 . If a user lands on v1, they continue seeing v1 during their session. If a user lands on v2, they stick to v2 to avoid switching experiences. To ensure Session Affinity comes into picture .

# Session Affinity:

Once a user is sent to either v1 or v2, they stick to that version during their session. This is done using a cookie or their IP address.

In the service.yaml this will write as follows :-

kind: Service
apiVersion: v1
metadata:
  name: "hello"
spec:
  sessionAffinity: ClientIP
  selector:
    app: "hello"
  ports:
    - protocol: "TCP"
      port: 80
      targetPort: 80


      


