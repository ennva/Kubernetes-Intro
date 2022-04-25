# Kubernetes-Intro


## Architecture of kubernetes cluster
<h2>Overview</h2>

![kebernetes architecture](assets/arch-cluster.png "arch")

<h2>Master Node</h2>

![kebernetes architecture](assets/master-node.png "arch")

The <b>Master Node</b> is responsible for the overall management of the Kubernetes Cluster. <br />
Its got 3 components:
* <b>API Server</b>: allows to interact with the kubernetes API. It's a front-end of the kubernetes control plane.
* <b>Scheduler</b>: it watches created Pods, who do not have a Node design yet, and designs the Pod to run on a specific Node
* <b>Controller manager</b>: it run controllers. These are backgroung threads that run tasks in a cluster. The Controller actually has a bunch of different roles, but that's all compiled into a single binary. The roles include:
  - <b>Node Controllers</b> who is responsible for worker states.
  - <b>Replication Controllers</b> which is responsible for maintaining the correct number of Pods for the replicated controllers.
  - <b>End-Point Controllers</b> which joins services and pods together.
  - <b>Service account and Token Controllers</b> that handle access management.
  
<h2>etcd</h2>

![kebernetes architecture](assets/etcd.png "arch")

The <b>etcd</b> is a simple distributed key value stored. <br />
Kubernetes uses etcd as its database and stores all cluster data here. Some of the information that might be stored, is:
* Job scheduling
* Pod details
* Stage information, etc..
	
<h2>kubectl</h2>

![kebernetes architecture](assets/kubectl-config.png "arch")

You interact with the master node using <b>kubectl</b> application which is the command line interface for kubernetes. <br />
Kubectl has a config file called <b>kubeconfig</b>. This file has a server information, as well as authentication information to access the API server.

<h2>Worker Node</h2>

![kebernetes architecture](assets/worknode.png "arch")

<b>worker nodes</b> are the nodes where your application operate. <br />
The <b>worker nodes</b> communicate back with the Master node.

<h2>Worker Node Inside</h2>

![kebernetes architecture](assets/worknode-inside.png "arch")

* <b>Kubelet</b>
Communication to a Worker Node is handled by the Kubelet Process. <br /> 
It's an agent that communicates with the API Server to see if Pods have been designed to the Nodes. <br /> 
It executes Pod containers via the container engine. <br /> 
It mounts and runs Pod volume and secrets. And finally, is aware of Pod of Node states and responds back to the Master. <br /> 
It's safe to say that if the Kubelet isn't working correctly on the Worker Node, you're going to have issues. <br /> 
* <b>Docker</b>
Kubernetes is an container orchestrator, so the expectation is that you have a container native platform running on your Worker Nodes. <br /> 
This is where Docker comes in and works together with Kubelet to run containers on the Node.<br /> 
You could use alternate container platforms, as well, like mumbles ( https://wiki.mumble.info/wiki/Main_Page), but not a lot of folks do this anymore. <br />
* <b>Kube-proxy</b>
This process is the Network Proxy and load balancer for the service, on a single Worker Node. <br />
It handles the network routing for TCP and UDP Packets, and performs connection forwarding. <br /> 
* <b>Pod</b>
Having the Docker Demon allows you to run containers. <br /> 
Containers of an application are tightly coupled together in a Pod. <br /> 
By definition, a Pod is the smallest unit that can be scheduled as a deployment in Kubernetes. This group of containers share storage, Linux name space, IP addresses, amongst other things. They're also call located and share resources that are always scheduled together. <br /> 
Once Pods have been deployed, and are running, the Kubelet process communicates with the Pods to check on state and health, and the Kube-proxy routes any packets to the Pods from other resources that might be wanting to communicate with them <br />

<h2>Worker Node expose outside</h2>

![kebernetes architecture](assets/worknode-outside.png "arch")

Worker Nodes can be exposed to the <b>internet</b> via load balancer. And, traffic coming into the Nodes is also handled by the Kube-proxy, which is how an <b>End-user</b> ends up talking to a Kubernetes application. <br />

## Production kubernetes deployments
* Kubernetes The Hard Way (https://github.com/kelseyhightower/kubernetes-the-hard-way)
* <b>kubeadm</b>: <br />
The most common way to install Kubernetes today is using the Kube admin tool, also known as kubeadm. <br />
This is probably the easiest way to install it on any public cloud, private cloud, or on premise infrastructure. <br />
Essentially, you're required to initially provision the master host with Docker and the Kubernetes distribution you're looking for. <br />
Once this is done, you can run kubeadm init, which starts up Kube admin and provisions the control plane. <br />
Once completed, Kube admin will provide you with a token called a join token. <br />
On each of your worker nodes, you can simply run the Kube admin join command with your join token, and the workers will join your cluster. <br />
Once the nodes have joined, you'll need to install a pod network for your pods to be able to communicate with each other. <br />
There are many different networking solutions that you can implement which have different pros and cons depending on your infrastructure needs. However <b><i>Flannel</i></b> and <b><i>Weave Net</i></b> are a great place to start
* <b>kops</b>: <br />
If you're working in an AWS infrastructure and you're wanting to install a cluster, then kops is the tool for you. <br />
Kops has a number of useful features. It automates the Kubernetes cluster provisioning in AWS. <br />
It deploys highly-available masters, also known as HA. <br />
It permits upgrading with kube-up, uses a state-sync model for dry runs and automatic idempotency. <br />
It generates configuration files for AWS CloudFormation and Terraform configuration, supports custom add-ons, and finally uses a manifest-based API configuration.<br />
* <b>cloud provider</b>: <br />
If you're going to run Kubernetes on a cloud provider like <b>AWS</b>, <b>Azure</b>, <b>Google</b> or <b>Oracle Cloud</b>, you might want to consider one of their native container services like <b>Amazon Elastic Kubernetes Service on AWS</b>, <b>Azure Container Service for Azure</b>, <b>Google Container Engine for Google Cloud</b> or <b>Oracle Container Engine for Kubernetes in Oracle Cloud</b>. <br />
Now you might ask, why am I wasting time using kube admin or kops when I can just use a managed service? This is a really good question. <br />
For those folks working in the cloud, managed Kubernetes services have become very popular. You spend a lot less time managing the Kubernetes control plane and spend more time on the applications deployed in your data plane. However, providers don't let you configure all the different elements of Kubernetes that you may need and might trail the latest Kubernetes version as well. <br />
So my recommendation is this. If you're running Kubernetes in the cloud, start with a managed Kubernetes service because it reduces overhead of managing the Kubernetes control plane. <br />
If you realize that there are other Kubernetes features your applications might require that are not provided by the managed service, then perhaps roll your own installation. <br />
If the managed service fits your use case, I'd highly recommend using it versus rolling your own. <br />
A Kubernetes install isn't as hard as it used to be with the advent of new tools like kube admin, kops and managed services on cloud providers doing all the heavy lifting for you. <br />
Getting a cluster up and running today is quick, and building a production-worthy platform isn't as hard as it used to be.

## Namespaces use cases
1. <b>Roles and responsabilities in an enterprise</b> <br />
Enterprises have many teams that typically operate independently of each other and might have some shared interfaces and APIs to communicate with each other. The use of namespaces is perfect in this scenario, because teams are able to operate independently without stepping on each other. Namespaces also prevent them from confusing services and deployments that might not belong to them. Here are a couple of things you'll need to watch out for. Plan in advance how you want to manage your enterprise in a Kubernetes environment. Setting standards up front will help you in the long-term with infrastructure management. Don't simply transpose existing on-premise controls and procedures to the new environment. This might result in bringing your existing infrastructure baggage to the new environment, which is supposed to be more agile.
2. <b>partitioning landscapes: dev vs. test vs. prod</b> <br />
With most teams building a continuous integration, or CI pipelines, you can easily define CI pipelines to deploy an entire environment in a quick and easy fashion. Some anti-patterns to watch out here for are, sometimes you will end up with a really large namespace because you have many applications that are running. In these scenarios, it might be good to create additional namespaces for groups of application. For example, E-Commerce Dev namespace for all E-commerce related applications. Another scenario is, you might end up with too many environments. Just because you can, does not mean that you should. Don't abuse the feature because it will result in too many unnecessary environments. If you don't use a staging deployment, or you don't use a staging namespace, then don't create one.
3. <b>Customer partitioning for non-multi-tenant scenarios</b> <br />
Consulting companies and small software vendors might use this method. You could create a namespace for each customer or project to keep them distinct, while not needing to worry about reusing the same names or resources across different projects. A new side effect of using the Helm package manager introduced by Kubernetes, is that k8s applications, which are typically comprised of deployment, services, et cetera, have gotten very complex. And when installing these applications, they're delivered in their own namespace, which makes it easier to encapsulate the application in one single spot in your Kubernetes cluster, all in just a single namespace. When you're done using the application, you can just delete the namespace.
5. <b>Application partitioning</b> <br />

## Monitoring and logging with kubernetes
* For development: <b>stdout and `kubectl logs pod_name`</b><br />
* For production: <b>(ELK stack) kibana + Elasticsearch + Logstask(Fluentd or Filebeat)</b><br />
* <b>cAdvisor:</b><br /> is an open-source resource usage collector that was built for containers. It auto discovers all containers in a given node and collects CPU, memory, file system and network usage statistics. cAdvisor also provides the overall machine usage by analyzing the root container on the machine.
* <b>Prometheus</b><br />
Prometheus is an open-source system monitoring and alerting toolkit with a query language that works really well for application-specific metrics and can also be used from an alerting standpoint. Prometheus was initially used to collect application metrics, y'know, like number of successful logins per hour, but can also be used natively to monitor kubernetes itself, with projects like kube-prometheus.
* Enterprise tooling like <b>Datadog</b>, <b>Splunk</b> or <b>Sysdig</b> <br />
<b>NOTES:</b> <br />
   - If you're going the open-source route, read the docs for the projects. 
   - Setting up logging and monitoring can be challenging and there's a lot of work being done around the user experience and setup of these tools to make things easier. 
   - If you're already using these tools for your infrastructure, take a look at what kubernetes support they have and use them where applicable. No point reinventing the wheel when you don't have to.
