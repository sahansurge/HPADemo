# HPADemo
A HorizontalPodAutoscaler (HPA for short) automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand.

Horizontal scaling means that the response to increased load is to deploy more Pods.

Here we experiment HPA

```
minikube start
```

```
minikube addons enable metrics-server
```

#Allows you to view the metrics of your cluster. If you would like to experiment with Horizontal Pod Autoscaling (HPA), metrics server is a necessary component in Minikube.

```
kubectl apply -f deployment.yaml
```

#This Deployment configuration, when applied to a Kubernetes cluster, will create and manage Pods running a PHP Apache application. The specified resource limits and requests help Kubernetes make scheduling decisions based on resource availability.

```
kubectl apply -f service.yaml
```

#This Service configuration, when applied to a Kubernetes cluster, creates a network endpoint for the PHP Apache Pods. Other services or external clients can access the PHP Apache application through this Service on port 80. The label selector ensures that the Service routes traffic to Pods with the specified label.

```
kubectl apply -f hpa.yaml
```

#In simpler terms, this HPA ensures that the number of Pods running the PHP Apache application scales dynamically based on CPU utilization. It aims to keep the CPU usage around 50%, scaling the number of Pods between 1 and 5 to handle varying workloads.

```
helm upgrade --install prometheus-adapter prometheus-community/prometheus-adapter -f values.yaml
```

#This setup is customizing the Prometheus Adapter to fetch and map metrics from a Prometheus query related to PHP Apache requests, and it installs or upgrades the adapter using Helm with the specified configuration values. The Prometheus Adapter is used to expose custom metrics from your application to Prometheus, which can then be utilized by the Horizontal Pod Autoscaler (HPA) for scaling decisions.
#Note: without the Prometheus adapter there to get metrics it will show <unknown>/50% something similar in the pod target.

#run this in another terminal to generate load

```
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- <IP>:<port>; done"
```

#Here you give the IP and the port of the Apache application, this will Increase the load in this case port 80.

```
kubectl get hpa php-apache --watch
```

#Now you can watch how it scales the pods from 1 to 5 replicas, to stop ctrl+c the terminal which generates load

References: https://faun.pub/kubernetes-horizontal-pod-autoscaling-53fb9d0c2396, https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/
