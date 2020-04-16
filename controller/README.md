# **Citrix CPX and Ingress Controller** 

Citrix CPX can be used for loadbalancing or exposes routes from outside the cluster to services within the cluster. Citrix Ingress controller is micro service which runs on kubernetes cluster and pick up the ingress and configure the appropriates routes/loadbalnce algorithms in Citrix CPX. Citrix ADC has many form factor which include SDX, MPX, VPX, CPX and BLX. CPX is freely available for education/experiment purpuse with limited bandwidth. This section talks about how to use Citrix CPX, Citrix ingress controller and expose aplication routes to CPX via Citrix Ingress controller.  

## **Who should read this?**

1. If you want to setup a micro service on a Kubernetes and want to create route using citrix ingress controller.
2. If you want to validate citrix ingress controller and citrix adc (CPX).
3. If you want to understand kubernetes ingress concepts.
 
## **This Section contains**

1. Deploy a micro service on a Kubernetes cluster  
2. Citrix ADC as Ingress Device 
3. Access the  application.
4. Why Citrix ADC is better choice for loadbalancing?

## **Deploy guestbook micro service**


## **Citrix ADC to route the traffic to SFTP service**

Now your SFTP service is up and running. Next step is to expose this application for users. 
Citrix ADC is being used here to route the traffic to the SFTP service.

1. Create an ingress for the sftp application.
   1. Download the ingress yaml.
      ```
       wget https://raw.githubusercontent.com/janraj/Networking/master/sftp/ingress.yaml
      ```
   2. Citrix Ingress controller uses following annotations to configure the Citrix ADC.
      ![](./images/Ingress.png)
      Set the ```ingress.citrix.com/frontend-ip``` with the IP which you want to use for exposing SFTP service.
      Set the username, password and file name on ```ingress.citrix.com/monitor```.
   
   3. Deploy the updated ingress.
      ```
      kubectl create -f ingress.yaml -n sftp
      ```
2. Deploy Citrix Ingress controller to configure the Citrix ADC.
     1. Download the citrix ingress controller using
        ```
          wget https://raw.githubusercontent.com/citrix/citrix-k8s-ingress-controller/master/deployment/baremetal/citrix-k8s-ingress-controller.yaml
        ```
     2. Update the enviornment variable follow these [steps](https://github.com/citrix/citrix-k8s-ingress-controller/tree/master/deployment/baremetal).
     3. Update the ingress class for Citrix Ingress controller with ```sftp``` which is being used in ingress. 
     4. Deploy the Citrix ingress controller using
        ```
         kubectl create -f citrix-k8s-ingress-controller.yaml -n sftp
        ```
4. Verify configurations has been created on Citrix ADC.
   
   
   1. Check CS vserver configuration.

      ![](./images/csvserver.png)

   2. Check LB vserver configuration.

      ![](./images/lbvserver.png)

   3. Check Servicegroup configuration.

      ![](./images/servicegroup.png)

   4. Check Monitor configuration.   

      ![](./images/monitor.png)

  
## **Access the application** 

Access the application using the csvserver IP used in the ADC configuration. We can use curl, FileZilla or any such tools.
   
![](./images/SFTP_Connection.png)

Once  its connected, we can transfer files between local host and server.
   

## **Why Citrix ADC is better choice for exposing the SFTP service.**

1. Citrix ADC allows you to monitor your service at deep level by checking whether the file present in the service or not. This can be easily set using smart annotation provided [here](https://github.com/citrix/citrix-k8s-ingress-controller/blob/master/docs/configure/annotations.md).
2. Citrix ADC allows to distribute the traffic to right pod in the right Node which reduces the latency compared to other ADC.
3. Citrix ADC allows to reach to the service IP (pod) irrespective of which subnet your cluster and Citrix ADC runs. refer [here](https://github.com/citrix/citrix-k8s-node-controller)  


  
