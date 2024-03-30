1. We are using **Minikube** so we need to run below command before creating image.

 ``` $ eval $(minikube docker-env) ```

  This will use the minikube docker-env for the current session.

  Ref: https://stackoverflow.com/questions/56392041/getting-errimageneverpull-in-pods
    
2. Create a docker image.
   
  ``` $ docker build -t python-demo-app:v1 . ```

3. Create a **Deployment**
   
  ``` $ kubectl apply -f deployment.yml ```

4. Verify the Deployment

  ``` $ kubectl get deploy ```

    ubuntu@ip-172-31-50-106:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ ``` kubectl get deploy ```

```
    NAME                READY   UP-TO-DATE   AVAILABLE   AGE

    sample-python-app   2/2     2            2           66m

```

5. Verify Pods

  ``` $ kubectl get pod ```

   ubuntu@ip-172-31-50-106:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ ``` kubectl get pod ```

   ```

   NAME                                 READY   STATUS    RESTARTS   AGE

   sample-python-app-86d7fd95fb-cbcdp   1/1     Running   0          11m

   sample-python-app-86d7fd95fb-cslgs   1/1     Running   0          67m

```

6. Verify pods with more details.

   ``` $ kubectl get pod -o wide ```

ubuntu@ip-172-31-50-106:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ ``` kubectl get pod -o wide ```

```

   NAME                                 READY   STATUS    RESTARTS   AGE   IP            NODE       NOMINATED NODE   READINESS GATES
   
   sample-python-app-86d7fd95fb-cbcdp   1/1     Running   0          12m   10.244.0.13   minikube   <none>           <none>
   
   sample-python-app-86d7fd95fb-cslgs   1/1     Running   0          68m   10.244.0.11   minikube   <none>           <none>

```

7. Access to Minikube Cluster and verify the url like below and it should display the output.

``` docker@minikube:~$ curl -L http://10.244.0.13:8000/demo ```

   **Above we saw that, within cluster we are able to access the application now we will look how we can access from Node.**

8. Create a **Service** and type a **_NodePort_**

   ``` $ kubectl apply -f service.yml ```

9. Verify Service.

``` $ kubectl get svc ```

   ubuntu@ip-172-31-50-106:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ ``` kubectl get svc ```

   ```
   
   NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
   
   kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        123m
   
   my-service   NodePort    10.100.42.148   <none>        80:30007/TCP   62m

```

10. Get Minikube ip and check, if you are using K8s Cluster using **kOps**, Identify EC2 instance ip and check.

  ```  ubuntu@ip-172-31-50-106:~/k8s/Docker-Zero-to-Hero/examples/python-web-app$ ``` minikube ip ```

   ``` 192.168.49.2 ```

11. Access above IP and verify the url like below and it should display the output.

  ``` $ curl -L http://192.168.49.2:30007/demo ```

  **Now We will look into Ingress & Ingress Controller**

  1. Create a **Ingress** resource by creating **ingress.yml** file.

     ``` kubectl apply -f ingress.yml ```

  2. Verify Ingress resource.

     ```
        ubuntu@ip-172-31-60-227:~/Docker-Zero-to-Hero/examples/python-web-app$ kubectl get ingress
         NAME              CLASS    HOSTS         ADDRESS        PORTS   AGE
         ingress-example   <none>   foo.bar.com   192.168.49.2   80      4h7m
     ```

  3. In order to work Ingress resource, cluster must have an Ingress Controller so create a **Ingress Controller**, use below command If 
     you are using **Minikube**

     ``` minikube addons enable ingress ```

     ref: https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

   4. Get **Minikube IP** and add host, in our case host is **foo.bar.com** in  **/etc/hosts** file like below.

      ``` 192.168.49.2 foo.bar.com ```

   5. Now Verify that the **Ingress controller** is directing traffic

      ``` curl -L "http://foo.bar.com/demo" ```

       **You should see:**

      ![image](https://github.com/digupawar/Docker-Zero-to-Hero/assets/72307345/b3e61ad2-86d8-4944-8b38-db3cb18166b5)


