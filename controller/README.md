# **Citrix CPX and Ingress Controller** 

Citrix CPX is a container based ADC which can be used for load balancing or exposes routes from outside the k8s cluster to services within the k8s cluster. Citrix Ingress controller is a micro service which runs on a Kubernetes cluster and pick up the ingress and configure the appropriates routes on Citrix CPX. Citrix ADC has many form factor which include SDX, MPX, VPX, CPX and BLX. This section talks about how to use Citrix CPX, Citrix ingress controller and expose application routes to CPX via Citrix Ingress controller. Citrix has CPX with builtin ingress controller we are using that form factor here.

## **Who should read this?**

1. If you want to setup a micro service on a Kubernetes and want to create route using citrix ingress controller.
2. If you want to validate Citrix ingress controller and Citrix ADC (CPX).
3. If you want to understand Kubernetes ingress concepts.
 
## **This Section contains**

1. Deploy an application on Kubernetes cluster  
2. Citrix ADC as Ingress Device 
3. Access the  application.

## **Deploy guestbook application**

First we can deploy the application on a kubernetes cluster.

```
    kubectl apply -f https://raw.githubusercontent.com/Chorusio/NetworkSolutionArchitect/master/controller/apps/guestbook-all-in-one.yaml
```

## **Deploy CPX**

Deploy the CPX with builtin ingress controller as a pod in the cluster.

```    
    kubectl apply -f https://raw.githubusercontent.com/Chorusio/NetworkSolutionArchitect/master/controller/citrix/citrix-k8s-cpx-ingress.yml
```

## **Verify the states of APPS and CPX**

Check the states of CPX and guest book application which we have deployed using 
```
 kubectl get pods
```
will get the output like following.

![](./images/Status.png)

## **Expose guestbook application using ingress resource**

Now your guestbook application and CPX is up and running. Next step is to expose this application for users. 
Citrix ADC is being used here to route the traffic to the guestbook service. Citrix CPX is running as POD in Kubernetes cluster. 

1. Deploy the ingress resource for guestbook application.
   
   ```
      kubectl apply -f  https://raw.githubusercontent.com/Chorusio/NetworkSolutionArchitect/master/controller/apps/guestbook-ingress.yaml
   ```

2. Verify configurations has been created on Citrix ADC.
   
   
   1. Check CS vserver configuration.

      ![](./images/csvserver.png)

   2. Check LB and Servicegroup configuration.

      ![](./images/lbvserver.png)

  
## **Access the application** 

Create a host entry for dns resolution for ``` www.guestbook.com X.X.X.X(IP of k8s master node) ``` in hostfile.
Get NodePort information for cpx-service using ```kubectl describe service cpx-service```.
Access the application using ```http://www.guestbook.com:NodePort``` from browser which opens guestbook application.

The application UI looks like below.
![](./images/guestbook.png)


  
