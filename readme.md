#  This challenge will give you an opportunity to level-up your Kubernetes skill set before the end of the year.

https://www.digitalocean.com/community/pages/kubernetes-challenge

**Topic:** 

Deploy a security and compliance system using [Falco](https://falco.org/blog/intro-k8s-security-monitoring/) 


## Steps

1.  Follow the [doc](https://docs.digitalocean.com/products/kubernetes/quickstart/#create-clusters) to spin up a DOKS Cluster 

    - Manual setup only take a couple of minutes
    - Could also use IaC tools like Terraform, Pulumi 
    - Download the kube config for connecting to Kubernetes

2. Interact with the Cluster 
    
   2.1 Using the fancy K8S IDE [Lens](https://k8slens.dev/)

    - easy to use with visual interface 

   2.2 Using kubectl 
       

The folder structure looks like below, 

```
.kube
  ├── -
  ├── cache
  ├── config
  ├── custom-contexts
  └── http-cache
```

All the custom kube configs can be placed in the `custom-contexts` folder.

In order to switch multiple clusters easily, add the following code in the `bashrc` or `zshrc`, 

 ```
# Set the default kube context if present
DEFAULT_KUBE_CONTEXTS="$HOME/.kube/config"
if test -f "${DEFAULT_KUBE_CONTEXTS}"
then
  export KUBECONFIG="$DEFAULT_KUBE_CONTEXTS"
fi

# Additional contexts should be in ~/.kube/custom-contexts/
CUSTOM_KUBE_CONTEXTS="$HOME/.kube/custom-contexts"
mkdir -p "${CUSTOM_KUBE_CONTEXTS}"

OIFS="$IFS"
IFS=$'\n'
for contextFile in `find "${CUSTOM_KUBE_CONTEXTS}" -type f -name "*.yaml"`
do
   export KUBECONFIG="$contextFile:$KUBECONFIG"
done

IFS="$OIFS"
 ``` 

3. Deploy Falco using [Helm](https://helm.sh/) 

    - easy to deploy K8S manifests 

   3.1 Add Helm repo 
   
      `helm repo add falcosecurity https://falcosecurity.github.io/charts`

   3.2 Install Falco Chart 

      ` helm install my-falco falcosecurity/falco --version 1.16.3`

 4. Check Falco Pods Status
     
     4.1 Get pods 

      ```
     NAME                     READY   STATUS    RESTARTS   AGE
      my-falco-bs462           1/1     Running   0          37m
      my-falco-ljccw           1/1     Running   0          37m
      my-falco-vv442           1/1     Running   0          37m
     ``` 

5. Set up an environment to emulate the attacks and detect

   Lets run the following commands to deploy in the env, 

   ` helm repo add stable https://charts.helm.sh/stable`
   
   ` helm repo update`

   `helm install mysql-db stable/mysql`
   
   `kubectl get pods`  

    Now. we have a running mysql pod, so we can start detecting the following 
    scenarios. 

    5.1 Detecting terminal shells being spawned in pods
    
    `kubectl exec -it mysql-db-6696899956-v84vc -- bash -il`
    
     and check Falco logs, you will see the log like below, 

    `Notice A shell was spawned in a container with an attached terminal `

    
## Conclusion

  In this short blog, we looked at the basics of DigitalOcean Kubernetes setup and how Falco allows us to achieve the detection of security issues. Most importantly, this is a truly starting point to get in the world of Kubernetes! 

    

