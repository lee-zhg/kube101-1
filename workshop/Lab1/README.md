# Lab 1. Set up and deploy your first application

Learn how to deploy an application to a Kubernetes cluster hosted within
the IBM Container Service.

# 0. Setup access to the Kubernetes Cluster running on IBM Cloud Private
If you haven't already:
1. Open the developer VM. Obtain this IP, plus the IP for the ICP master from your instructor 
2. Login to the ICP Cluster

   ```$ cloudctl login -a [ICP Master IP]:8443 --skip-ssl-validation```
   
That's it! We have installed an instance of IBM Cloud Private for you, with a running Kubernetes. You are ready to deploy your first application, `guestbook`.

# 1. Deploy your application

In this part of the lab we will deploy an application called `guestbook`
that has already been built and uploaded to DockerHub under the name
`ibmcom/guestbook:v1`.

1. Start by running `guestbook`:

   ```$ kubectl run guestbook --image=ibmcom/guestbook:v1```

   This action will take a bit of time. To check the status of the running application,
   you can use `$ kubectl get pods`.

   You should see output similar to the following:

   ```console
   $ kubectl get pods
   NAME                          READY     STATUS              RESTARTS   AGE
   guestbook-59bd679fdc-bxdg7    0/1       ContainerCreating   0          1m
   ```
   Eventually, the status should show up as `Running`.
   
   ```console
   $ kubectl get pods
   NAME                          READY     STATUS              RESTARTS   AGE
   guestbook-59bd679fdc-bxdg7    1/1       Running             0          1m
   ```
   
   The end result of the run command is not just the pod containing our application containers,
   but a Deployment resource that manages the lifecycle of those pods.
 
   
3. Once the status reads `Running`, we need to expose that deployment as a
   service so we can access it through the IP of the worker nodes.
   The `guestbook` application listens on port 3000.  Run:

   ```console
   $ kubectl expose deployment guestbook --type="NodePort" --port=3000
   service "guestbook" exposed
   ```

4. To find the port used on that worker node, examine your new service:

   ```console
   $ kubectl get service guestbook
   NAME        TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
   guestbook   NodePort   10.10.10.253   <none>        3000:31208/TCP   1m
   ```
   
   We can see that our `<nodeport>` is `31208`. We can see in the output the port mapping from 3000 inside 
   the pod exposed to the cluster on port 31208. This port in the 31000 range is automatically chosen, 
   and could be different for you.

5. `guestbook` is now running on your cluster, and exposed to the internet. We need to find out where it is accessible.
   Obtain the IP for the IBM Cloud Private proxy machine from your instructor.
   
6. Now that you have both the address and the port, you can now access the application in the web browser
   at `<ICP Proxy IP>:<nodeport>`. In the example case the nodeport is `31208`.
   
Congratulations, you've now deployed an application to Kubernetes!

When you're all done, you can either use this deployment in the
[next lab of this course](../Lab2/README.md), or you can remove the deployment
and thus stop taking the course.

  1. To remove the deployment, use `$ kubectl delete deployment guestbook`.

  2. To remove the service, use `$ kubectl delete service guestbook`.

You should now go back up to the root of the repository in preparation
for the next lab: `$ cd ..`.
