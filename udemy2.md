## Certified Kubernetes Administrator
------------------------------------------------------------------------------
     1. Domain 1- Cluster Architecture, Installation & Configuration 
	2 - Workloads and Scheduling 3 - Services and Networking 4 - Storage 
	5 - Troubleshooting(github.com/cncf/curriculum) i
	(https://github.com/zealvora/certified-kubernetes-administrator)
     2. Container Orchestration is all about managing the life cycles of 
	containers, especially in large, dynamic environments.
        Conatiner orchestration can be used to perfrom lots of tasks, 
	some of them include:
        - Provisioning and deploymet of containers
        - Scaling up or removing containers to spread application load evenly
	- Movement of containers from one host to another if there is a 
	shortage of resources
	- Load balancing of service discovery between containers
	- Health monitoring of containers and hosts.
        Container Orchestration Solutions
	- Docker Swarm
	- Kubernetes
	- Apache Mesos
	- AWS ECS 
	- Platforms : EKS, AKS GKE
     3. kubectl , kubeconfig file. (https://kubernetes.io/docs/tasks/tools/)
     4. Kubectl run mywebserver --image=nginx
	kubectl exec -it mywebserver --bash
	kubectl delete pod mywebserver
        kubectl get pods
	kubectl apply -f pod.yaml
        Kubectl api-resources
	kubectl explain pod
	kubectl delete -f pod.yaml
	kubectl describe pod mywebserver
	netstat -ntlp ( to check the port listening on)
	ifconfig (get if address on pod)
	kubectl exec -it mywebserver -c container2 sh i
	( to connect to specific container )
     5. A pod in kubernetes represents a group of one or more application 
	containers, and some shared resources for those containers.
	-IP address, volume and containerized apps.
	Conatiners within a Pod share an Ip address and port space and can 
	find ecah other via localhost.
	A pod always runs on a Node
	A Node is a worker machine in Kubernetes
	Ecah Node is managed by the Master
	A Node can have multiple Pods.
     6. Kubernetes Objects is basically a record of intent that you pass on to
	 the Kubernetes cluster. Once you create the object, kubernetes system 
	will constantly work to ensure that object exists.
	Kubernetes object can be created either through Kubectl commands or a 
	configuration file written in YAML.
	Benefits of configuration file : Integrates well with changes review 
	process, provides the source of record on what is live within the 
	kubernetes cluster, easier to troubleshoot changes with version control
     7. Kubernetes Master Components : Kube-controller-manager, 
	cloud-controller-manager, kube-apiserver, etcd, kube-scheduler.
	kube-apiserver- Component on the master that exposes the kubernetes API.
	etcd - key value store used as kubernetes backing store for all cluster data.
	kube-scheduler - Component on gthe master that wtaches newly created 
	pods that have no node assigned and selects a n ode for them to run on.
	kube-controller-manager - Responsible for controlling various aspects,
	 including Node Controllers: Responsible when node goes down. 
	Replication controllers, endpoint controllers, Service account and Token controllers.
	cloud-controller-manager - Runs controllers that interact with the underlying cloud providers.
     8. Kubernetes Node components : kubelet An agent that runs on ecah node 
	in the cluster. It makes sure that conatiners are running in a pod. 
	Kube-proxy:  Acts as a network proxy which maintaines network rules 
	on the host and performing connection forwading. 
	Conatiner Runtime : Software which is respon sible for running containers.
	 Supported runtimes, docker, conatinerd, rktlet and others.
     9. etcd : etcd reliably stores the configuration data of the kubernetes 
	cluster, representing the state of the cluster ( what nodes exist in 
	the cluster, what pods should be running, which nodes they are running
	 on, and a whole lot more ) at any given point of time.
      	etcdctl set instrutor sam 
      	etcdctl get instructor
	Anything that you read while running kubectl get pods is stored in etcd
	Any node crashing or process dying causes values in etcd to be changed.
	Whenever you create something with kubectl create/ kubectl run will create an entry in the etcd.
     10. Kube-api-server serevrs acts as a gateway to the kubernetes cluster. 
	The API serer is the only kubernetes component that connects to etcd;
	 all the other components must go through the API Server to work with 
	the cluster state. The API Server is also responsible for the authentication
	 and authorization mechansim. All API clients should be authenticaed 
	in order to interact with the API Server.
     11. Flow : 1 kubectl writes to the API server 
	( kubectl run mywebserver --image=nginx) 
	2 API server will authenticate and authorize. Upon validation it will write to etcd.
	 3 Upon write to etcd, API server will invoke the scheduler. 
	4 Scheduler decides which node the pod should run and return data to 
	API server. API will in-turn write it back to etcd. 
	5: API server will invoke the node decided by the scheduler 
	6: Kubelet communicates to the docker daemon via Docker socket to create the conatiner. 
	7: Kubelet will update the status of the POD back to the API server. 
	8: API server will write the status details back to etcd.
     13: API Primitives : Alpha level - version name contains alpha. Maybe by buggy. Enabling the feature may expose bugs. Disbaled by defau;t
	Beta level : The version names conatin beta. Code is well tested.Enabling the feature is considered safe, Enabled by default. Recommended for only non-business-critical uses because of potential for incompatible changes in subsequent releases.
	Stable Level : the version name os vX where X is an integer. Recommended to use in production environments.
     14: yaml file key fileds: apiVersion - Version of API. Kind - Kind of object you want to create. metadata name - Name of the object that uniquely identifies it. spec - Desired state of the object.
	https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/
	https://github.com/kubernetes/community/blob/master/contributors/devel/sig-architecture/api-conventions.md#metadata
     15: kube-scheduler : some Factors for scheduling :		Resource requirements, hardware/software policy constraints, Affinity & Anti-Affinity and Data Locality.
     16: ENTRYPOINT : The best use for ENTRYPOINT is to set the image's main cpmmand. ENTRYPOINT doesn't allow you to override the command.
	FROM busybox
	ENTRYPOINT ["/bin/ping"]
	#CMD ["sh"]
	no we can append to entry point but cannot override
	e.g docker conatiner run -dt --name base02 base02 -c 20 google.com
     17. Command and Arguments : In kubernetes we can overrride the default entrypoint and CMD with command and args filed.
	 Docker Filed Name ENTRYPOINT -> Kubernetes Filed name -> Command that will be run by container.

	Docker Field Name CMD -> Kubernetes Filed Name args -> Argument passed to the container.
	  apiVersion: v1
	  kind: Pod
	  metadata:
		name: command3
	  spec:
	    conatiners:
	    - name: count
	      image: busybox
	      command: ["sleep"]
	      args: ["3600"]
      
    18. When there is an entrypoint and CMD set for a Docker image and if we are not manually overriding it at K8s manifest level, then dinal decsions is to use the default image specifications,
     Image Entrypoint -Sleep
     Image Command - 3600
     Container Command ping -c5 google.com 
     Conatiner Argument - Not Set
     Final ping -c5 google.com

    19. kubectl explain pod.spec
    20. Docker EXPOSE : Its functions as a type of document between person who builds the image and the person who runs the container, about which ports are intended to be published.
	Kubernetes ports is same as Docker EXPOSE.
	kubectl run nginx --image=nginx
	kubectl run nginx --image-mginx --port=80
	kubectl run nginx --image=mginx --port=80 --dry-run=client -o yaml
	kubectl delete pod nginx
	kubectl delete pod --all

	(https://docs.google.com/document/d/1XpXl9Xm0_4sUtf3gKUA0TFp4qwCvXnu8YO9_0B4OUZo/edit?usp=sharing)	
    21: There can be multiple objects within a Kubernetes cluster. Some of the objects are as follows:
	Pods, Services, Secrets, Namespaces, Deployments and Daemonsets.
    22: Labelling objects
	kubectl label pods nginx-7bb7cd8db5-dj82f env=dev
	kubectl label pods nginx-7bb7cd8db5-dj82f env=prod
	kubectl get pods --show-labels
	kubectl get pods -l env=dev
	kubectl get replicaset
    23: Deployments provides replication functionality with the help of ReplicaSets, along with various additional capability like rolling out of changes, rollback changes if required.
	kubectl get deployments
	kubectl rollout history deployment.v1.apps/kplabs-deployment
	By default it ensures that at least 25% of the desired number of Pods are up (25% max unavailable)
	kubectl rollout undo deployment.v1.apps/kplabs-deployment --to-revision=1
	
	kubectl create deployment nginx-deploy --image=nginx
	kubectl set image deployment nginx-deployment nginx=nginx:1.91 --record
	kubectl scale deploymengt nginx-deployment --replicas 10
	kubectl rollout undo deployment nginx-deployment
    24: Daemon Set can ensure that all Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them.
	kubectl get daemonset
	kubectl describe daemonset mywebserverdaemon
    25.nodeSelector allows us to add a constraint about running a pid in specific worker node.
	kubectl label node node1 disk=hdd
	kubectl label node node2 disk=ssd
	kubectl describe node node1  ( to see label)
	add following in Pod Yaml file
	nodeSelector:
	   disk: ssd 		
	kubectl get pods -o wide
	Nodes come pre-populated with the standard set of labels. These include:
	kubernetes.io/hostname
	kubernetes.io/os
	kubernetes.io/arch
	failure-domain.beta.kubernetes.io/zone
	failure-domain.beta.kubernetes.io/region
	beta.kubernetes,io/instance-type
	Inaddition to above labels the service provider can also add some more labels.
    26. Node Affinity is a set of rules used by the scheduler to determine where a pod can be placed.
	In Kubernetes terms, it is referred as nodeSelector and nodeAffinity/podAffinity fileds under PodSpec.
	Node affinity is conceptually similar to nodeSelector- it allows you to constrain which nodes your pod is eligible to be scheduled on, based on bales on the node.
	requiredDuringSchedulingIgnoreDuringExecution (Hard Preference)
	preferredDuringSchedulingIgnoredDuringExecution (Soft Preference)
	nodeAffinity:
		requiredDuringSchedulingIgnoredDuringexceution
		  nodeSelectorTrems:
		   - matchExperessions:
		     - key: disk
		       operator: In
		       values:
			- ssd
		 containers:
		  - name: with-node-affinity
		    image: nginx
    27: Pod Affinity e.g what if i want to run BackEnd pod in same node as AppA pod ?
	  Step1 : Pod Selector : Here we select the POD based on which the decsions will be made where we want to place the current Pod.
	  Step2: Topology : It can mean same place if we look at the one or region level (same AZ/ same region).  It can also mean different place if we look at the host level.
	  Step3: Same place as the other Pod Yes (Pod Affinity ) or No ( Pod Anti-Affinity)
	   affinity:
	     podAffinity:
                requiredDuringSchedulingIgnoredDuringex
ceution
                  labelSelectorTrems:
                   - matchExperessions:
                     - key: app
                       operator: NotIn
                       values:
                        - frontend
		    topologykey: kubernetes.io/hostname
                 containers:
                  - name: with-node-affinity
                    image: nginx
    28: Resource limit and Request : if you schedule a large application in a node which has limited resource, then it would soon lead to OOM or others and will lead to downtime.
	  Request and Limits are two ways in which we can control the amount of resource that can be assigned to a pod ( resource like CPU and memory)
	  Requests: Gauranteed to get.
	  Limits: Makes sure that conatiner does not take resources above a specific value.
	  Kubernetes Scheduler decided the ideal node to run the pod depending on the requests and limits.
	  spec:
		containers:
		  -  name: kplans-container
		     image: nginx
		     resources:
			request:
			 memory: "64Mi"
			 cpu: "0.5"
			limits:
			 memory: "128Mi"
	                 cpu: "1"		
	   
	   Guaranteed - Requests and limits are equal
	   Burstable - Requests and limits are not equal
	   Best Effort -  No request and limits have been set.
	
	
    29: Scheduling Pods without Scheduler (Static Pods)
	   You can directly inform the kubeket that it needs to run a specific Pod.
	   There are multiple ways in which you can tell kubelet to run a pod.
	   Pod created directly without schedulers are also reffered as static Pods.
	   system status kubelet  - to know kubelet status
	   cd /etc/systemmd/system/kubelet.service.d/
	   look for file 10-kubeadm.conf
	   cd /etc/kubernetes/manifests 
	   kubelet will scan above directory and run your custom yaml file.
    30. Taints are used to repel the pods from a specific nodes.
	   In order to enter the taint worker noce you need a special pass. This pass is called toleration.
	   kubectl taint nodes kubadm-worker-01 key=value:NoSchedule
	   
	   spec:
		containers:
		- name: nginx-container
		  image: nginx
		tolerations:
		- key: "key"
		  operator: "Exists"
		  effect: "NoSchedule"
	   Components of Taints and Tolerations
		key - A key is any string upto 253 characters.
		value - The value is any sring, u to 63 characters.
			effect -  NoSchedule, PreferNoSchedule, NoExecute.
		operator - Equal, Exist

    31.  Multi-Container POD Patterns 
		Sidecar - Running multiple container as part of a pod in a single node.
		Ambassador Pattern - Is a type of sidecar pattern were the second conatiner is primarily used to proxy the requests.
		Adapter Pattern is generally used to transform the application output to standardize/normalize it for aggregation.
		(https://docs.google.com/document/d/1Uoebi-g-Arua9myZCzK3Kx-rpJsPZ9NK9oyWG8MgdxY/edit?usp=sharing)
    32 Service
		Kubernetes Service can act as an abstraction which can provide a single IP address and DNS through which pods can be accessed.
		THis layer of abstraction allows us to perform lot of operations like load balancing, scaling of pods and others.
		kubectl get service
		There are several types of Kubernetes Services which are available:
		NodePort, ClusterIP, LoadBalancer, ExternalName.

		Endpoints tracks the IP address of the objects that service can send traffic to.
		ClusterIP : Whenever service type is ClusterIP, an internal cluster IP address is assigned to the service. Since an internal cluster IP is assigned, it can only be reachable from within the cluster. This is a default ServiceType.
		NodePort: From the name, we can identify that it has to do with opening a port on the nodes. If servicetype is NodePort, then Kubernetes will allocate a port (default: 30000-32767) on every worker node. Each node will proxy that port into your service.
		Loadbalancer : Will automatically deploy an external Load balancer. This load balancer takes care of routing requests to the underlying service. The overall implementation of LoadBalancer depends on your Cloud Provider. If you plan to use it in bare-metal, ten you will have to provide own load balancer implementation.
		
		Create a service - Exposing Pod
		kubectl expose pod nginx --name nginx-svc --port=80 --target-port=80
		create a service - Expose Deployment
		kubectl expsose deployment nginx --name nginx-dep-svc --port=80 --target-port=8000
		create a NodePort Service
		kubectl expose deployment nginx --name nodeport-svc --port=80 --target-port=8000 --type=NodePort
     33. Ingress :- kubectl get ingress
	Kubernetes Ingress is a collection of routing rules which governs how 
	external users access the services running within the Kubernetes cluster.
	Ingress can provide various features which includes
	Load Balancing, SSL Termination and Named=based virtual hosting.
	There are two sub-components when we discuss about Ingress : Ingress Resource, Ingress Controllers. 
	Ingress Resource conatins set of routing rules based on which traffic is routed to a service.
	Ingress Controller takes care of the Layer 7 proxy to implement ingress rules.
	You must have an ingress controller to satisfy an Ingress. Only creating an Ingress resource has no effect.
     34. Helm : Package manager for Kubernetes.
	Helm client and Tiller
     35. Named based Virtual Hosting
	Named-based virtual hosts support routing HTTP traffic to multiple host names at the same IP address. The request are routed based on the HTTP host-hreader.
     36. Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces.
     kubectl get namespace
     kubectl get pods --namespace kube-system
     kubectl create namespace teama
     kubectl run nginx --image=nginx --namespace teama
     List of Namespaces default, kube-system, kube-public, kube-node-release
     37. Service Accounts 
	2 categories of users normal and service accounts.
	Service accounts allows the Pods to communicate with the API server.
	kubectl get serviceaccount -namespace Kplabs
	Default service account is automatically created when you created a new namespace.     
        kubectl get sa default -o yaml
	PODS are automically mounted with the default service accounts.
	kubectl cfreate sa kplabs
	kubectl get secret
	kubectl run nginx-sa --image="nginx" --serviceaccount="kplabs"
     38. NamedPort
	spec:
	 containers:
	 - image: nginx
	   name: nginx
	   ports:
	   - containerPort: 80
                       name:http
	Instead of Port Number we can also specify the name while creating service
	kubectl expose pod nginx --port=80 --target-port=http --name "kplabs-svc"

	(https://docs.google.com/document/d/1QHNHlvPTJSTIk_sLI-c_qm1n4CbG5RufYJmtw1vycbo/edit?usp=sharing)		
     39. Authentication
	Kubernetes does not have objects which represent normal user accounts.
	Kubernetes does not manage the user accounts natively.
	There are multiple ways in which we can authenticate. some of them 
	include: usernames/Passwords, Client certificates, Bearer Tokens etc
	you can also pass a static password file to API server on startup. ( --basic-auth-file=SOMEFILE )
	kubectl get secret
	kubectl describe secret default-token-nlmkm
	curl -k https://IP-HERE:/api/v1/ --header "Authorization: Bearer $TOKEN"
     40. Asymmetric crytography uses public and private keys to encrypt and decrypt data. 
	Either of the keys can be used to encrypt a message, the opposite key from the 
	one used to encrypt the message is used for decryption.
	ssh-keygen  ( check keys in ~/.ssh dir)
        Asymmetric key encryption is used by varipus protocols PGP, SSH, Bitcoin, TLS and S/MIME.
     41. Understanding SSL/TLS
	HTTPS: Communication is encrypted using Transport Layer Security(TLS)
	SSL2.0 -1995, SSL3.0-1996,TLS1.0-1999, TLS11.1-2006, TLS1.2-2008,
	TLS1.3-2018
	Each website has a certificate (like a passport which is issued by a
	trusted entity). Certificate has lot of details like domain name it is
	valid for, the public key, validity and others.Browser(clients) verifies
	if it trusts the certificate issuer. It will verify if all the details
	in the certificate. It will take the public key and initiate a
	negotiation. Asymmetric key encryption is used to generate a new
	temporary symmetric key which will be used for secure communication.
     42. Creating TLS Certificate for Authentication
	Setup Authentocation based on X509 Client Certificate
	zeal-key(openssl)->zeal.csr(openssl)->zeal.csr(k8s object)->k8s
	CA->zeal.crt
	zeal-key(openssl) +  zeal.crt -> Authenticate->kube-api
	Create docker conatiner in linux box
	install openssl
	install kubectl and copy ~/.kube/config
	Pre-Requisite:
	- Create a Linux container / Linux server were we can run openssl commands.

	yum -y install openssl nano
	- Connect the linux box with existing K8s setup.
	Kubectl Installation:

	curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl

	Step 1: Create a new private key  and CSR
	openssl genrsa -out zeal.key 2048
	openssl req -new -key zeal.key -out zeal.csr -subj "/CN=zeal/O=kplabs"

	Step 2: Encode the csr
	cat zeal.csr | base64 | tr -d '\n'

	Step 3: Generate the Kubernetes Signing Request
	apiVersion: certificates.k8s.io/v1beta1
	kind: CertificateSigningRequest
	metadata:
  	name: zeal-csr
	spec:
  	groups:
  	- system:authenticated
  	request: 
  	usages:
  	- digital signature
  	- key encipherment
  	- client auth


	Step 4: Apply the Signing Requests:
	kubectl apply -f signingrequest.yaml

	Step 5: Approve the csr
	kubectl certificate approve zeal-csr

	Step 6: Download the Certificate from csr
	kubectl get csr zeal-csr -o jsonpath='{.status.certificate}' | base64 -d > zeal.crt

	Step 7: Create a new context
	kubectl config set-credentials zeal --client-certificate=zeal.crt --client-key=zeal.key

	Step 8: Set new Context
	kubectl config set-context zeal-context --cluster do-blr1-kplabs-k8s --user=zeal

	Step 9: Use Context to Verify
	kubectl --context=zeal-context get pods		  

     44. Understanding Authorization
	Kubernetes provides multiple authorization modules like
	AlwaysAllow
	AlwaysDeny
	Attribute-Based Access Control(ABAC)
	Role-based access Control(RBAC)
	Node
	WebHook
	A role contains rules that represent a set of permissions
	A role binding grants the permissions defined in a role to a user or a
	set of users
	role.yaml
	apiVersion: rbac.authorization.k8s.io/v1
	kind: Role
	metadata:
		namespace: default
		name: pod-reader
	rules:
	- apiGroups: [""]
	  resources: ["pods"]
	  verbs: ["get","watch","list"]

	rolebinding.yaml
	apiversion: rbac.authorization.k8s.io/v1
	kind: RoleBinding
	metadata:
	  name: read-pods
	  namespace: default
	subjects:
	- kind: User
	  name: zeal
	  apiGroup: rbac.authorization.K8s.io
	roleRef:
	  kind: Role
	  name: pod-reader
	  apiGroup: rbac.authorization.k8s.io

	kubectl apply -f role.yaml
	kubectl apply -f rolebinding.yaml
	kubectl run nginx --image=nginx
	kubectl --context=zeal-context get pods
	kubectl --context=zeal-context run nginx2 --image nginx ( give error as
	as role.yaml does not have create permission.)

     45. API Groups, Resources and Verbs
	API Groups make it easier to extend the Kubernetes API.
	Currently there are serveral groups available : 
	The Core group, often referred to as the legacy group, is at
	the REST path /api/v1
	The named groups are at REST path /apis/$GROUP_NAME/$VERSION
	We have flexibility to enable/disable a specific API Groups in
	API Server.
	Within a specific API Group, there are multiple resources 
	available.
	e.g Pods, namespaces, nodes.
	Verbs are generally the type of requests that can be sent to a
	resource.
	e.g Get, list, create, update, patch, watch and others.
	kubectl --context=zeal-contect run --generator=run-pod/v1 
	nginx2 --image=nginx ( used to create Pod as default run 
	without generator considers deployment)
     46 Namespaces - Access Control
	kubectl get role
	kubectl --contect=zeal-context get pods (uses default namespac
	kubectl --context=zeal-contect get pods --namespace teama
	role.yaml
        apiVersion: rbac.authorization.k8s.io/v1
        kind: Role
        metadata:
                namespace: teama
                name: pod-reader
        rules:
        - apiGroups: [""]
          resources: ["pods"]
          verbs: ["get","watch","list"]
	 
	rolebinding.yaml
        apiversion: rbac.authorization.k8s.io/v1
        kind: RoleBinding
        metadata:
          name: read-pods
          namespace: teama
        subjects:
        - kind: User
          name: zeal
          apiGroup: rbac.authorization.K8s.io
        roleRef:
          kind: Role
          name: pod-reader
          apiGroup: rbac.authorization.k8s.io	
	
	A role can only be used to grant access to resources within a 
	single namespace.
	There can be multiple role with the same name in tow different
	namespaces.
     47 ClusterRole and ClusterRoleBinding
	A ClusterRole can be used to grant the same permissions as a
	Role, but because they are cluster-scoped, they can also be 
	used to grant access to:
	- cluster-scoped resources (like nodes)
	- namespaced resources(like pods) across all namespaces.
	apiversion: rbac.authorization.k8s.io/v1
	kind: ClusterRole
	metadata:
		name: cluster-pod-reader
	rules:
	- apiGroups: [""]
	  resources: ["pods"]
	  verbs: ["get", "watch", "list"]

	clusterrolebinding.yaml
        apiversion: rbac.authorization.k8s.io/v1
        kind: ClusterRoleBinding
        metadata:
          name: read-secrets-global
        subjects:
        - kind: User
          name: zeal
          apiGroup: rbac.authorization.K8s.io
        roleRef:
          kind: ClusterRole
          name: cluster-pod-reader
          apiGroup: rbac.authorization.k8s.io

	Note: Above we dont specific namespace as its at cluster level.
	Lets delete the role create in 45/46 above.
	delete role pod-reader
	delete role pod-reader --namespace teama 
	delete rolebinding read-pods --namespace teama
	delete rolebidning read-pods
	
	kubectl apply -f clusterrole.yaml
	kubectl apply -f clusterrolebinding.yaml
	
	A RoleBinding may also reference a ClusterRole to grant the permissions
	to namespaced resources defined in the ClusterRole within the
	RoleBinding's namespace
	
	This allows administrator to have set of central policy which can be
	attached via RoleBinding so its applicable at a per-namespace level.
	
     48 Network Policies
	By default, pods are non-isolated; they accept traffic from any source.
	A network policy is a specification of how groups of pods are allowed to
	communicate with each other and other network endpoints.
	e.g below policy will block inbound and outbound traffic for label db.
	apiVersion: networking.k8s.io/v1
	kind: NetworkPolicy
	metadata:
		name: default-deny-pod
		namespace: default
	spec:
		podSelector:
		  matchLabels:
		    role:db
		policyTypes:
		- Ingress
		- Egress 
	
	
	To bock specific Ingress and Egress blocks apply following rules
	ingress:
  	- from:
    		- ipBlock:
        		cidr: 172.17.0.0/16
        		except:
        		- 172.17.1.0/24
    		- namespaceSelector:
        		matchLabels:
          			project: myproject
    		- podSelector:
        		matchLabels:
          			role: frontend
    		ports:
    		- protocol: TCP
      		  port: 6379
  	egress:
  	- to:
    		- ipBlock:
        		cidr: 10.0.0.0/24
    		ports:
    		- protocol: TCP
      		port: 5978

     49 Kubeconfig
	Kubectl command uses kubeconfig file to find the information about the
	cluster, username,authentication mechanisms and others.
	Kubeconfig file can also have details associated with multiple
	kubernetes clusters.
	Cluster field has details related to the URL of your Kubernetes Cluster
	and its associated information.
	user filed contains authentication specific information like username,
	passwords.
	There can be different type of authentication mechanisms (user/pass, 
	certificates, tokens etc)
	Context groups information related to cluster, user and namespace.
	
	Kubectl config view
	
	Create KubeConfig from scratch

	1. Add cluster details.
	kubectl config --kubeconfig=base-config set-cluster development --server=https://1.2.3.4

	2. Add user details
	kubectl config --kubeconfig=base-config set-credentials experimenter --username=dev --password=some-password

	3. Setting Contexts
	kubectl config --kubeconfig=base-config set-context dev-frontend --cluster=development --namespace=frontend --user=experimenter

	4. Repeating above steps for second cluster
	kubectl config --kubeconfig=base-config set-cluster production --server=https://4.5.6.7

	Next Steps:
	1. View Kubeconfig
	kubectl config --kubeconfig=base-config view

	2. Get current conext information:
	kubectl config --kubeconfig=base-config get-contexts

	3. Switch Conexts:
	kubectl config --kubeconfig=base-config use-context dev-frontend

    50 Kubernetes Secrets
	Hard coding of Credentials issues:
	1. Anyone habing access to the container repository can easily fetch the
	credentials.
	2. Developer needs to have credentails of production systems.
	3. Update of credentails will lead to a new docker image being built.
	Solution :  Keep secrets in Central store, next APP fetches the
	credentials from central store, and next is authentication with secrets
	say with a DB.
	
	solutions available : AWS secrets manager, AWS Systems Manager Parameter
	Store, hashicorp vault.

	A kubernetes secret is an object that contains a small amount of
	sensitive data such as password, a token or a key.
	- Allows customers to store secrets centrally to reduce risk of expsoure
	- Stored in etcd database.

	kubectl create secret [TYPE][NAME][DATA]
	Elaborating Type:
	i) Generic:
	   File (--from-file)
	   directory
	   literal value
	ii) Docker Registry
	iii) TLS

	kubectl get secret
	kubectl create secret generic firstsecret
	--from-literal=dbpass=mypassword123

	kubectl describe secret firstsecret
	kubectl get secret firstsecret -o yaml
	
	apiVersion: v1
	kind: Secret
	metadata:
		name: thirdsecret
	type: Opaque
	data:
		username: ZGJhZG1pbg==
		password: bX1wYXNzd29yZDEyMw==

	
	apiversion: v1
	kind: Secret
	metadata:
		name: stringdata
	type: Opaque
	stringData:
		config.yaml: |-
			username: dbadmin
			password: mypassword

     51 Mounting Secrets in Containers
	2 approaches to acheive this
	1. Volumes
	2. Environment Variable.
	
	apiVersion: v1
	kind: Pod
	metadata:
		name: secretmount
	spec:
		containers:
		- name: secretmount
		  image: nginx
		  volumeMounts:
		  - name: foo
		    mountPath "/etc/foo"
		    readOnly: true
		volumes:
		- name: foo
		  secret:
			secretName: firstsecret
        
	apiVersion: v1
	kind: Pod
	metadata:
		name: secret-env
	spec:
		conatiners:
		- name: secret-env
		  image: nginx
		  env:
			- name: SECRET_USERNAME
			  valueFrom:
			    secretKeyRef:
				name: firstsecret
				key: dbpass
		 restartPolicy: Never
	(https://docs.google.com/document/d/1bfWCFQgJ6IhOZbSaliMRaMDIW8c3DX0KyVz96rRtv4E/edit?usp=sharing)

	52 Docker Volumes
	  By default all files created inside a container are stored on a
	  writable conatiner layer. 
	  Challenges with files in Conatiner Writable layer
	  1) The data doesn't persist when the conatiner no longer eists, and it 
	  can be difficuly to get the data out of the container if another
	  process needs it
	  2) Writing into a conatiner writable layer requires a storage driver
	  to manage the filesystem. The storage driver provided a union
	  filesystem using the linux kernel. This extar abstraction reduces
	  performance as compared to using data volumes, which write directly to
	  the host filesystem.
	  
	  Docker has tow options for containers to store files in the host
	  machine, so that the files are persisted even after the conatiner
	  stops: volumes, and bind mounts.
	  if you're running Docker on linux you can also used a tmpfs mount.
	  
	  docker volume ls
	  docker volume create myvolume
	  docker conatiner run -dt --name busybox -v myvolume:/etc busybox sh
	  docker volume inspect myvolume
	  cd /var/lib/docker/volumes/myvolume/_data
	  docker volume rm myvolume
	  
	  A given volume can be mounted into multiple containers simultanesously
	  when no running conatiner is using a volume, the volume is still
	  available to Docker and is not removed automatically.
	  
	  when you mount a volume, it may be named or anonymous. Anonymous
	  volumes are not given an explicit name when they are first mounted
	  into a container, so Docker gives them a ramdom name that is guranteed
	  to be unique within a given Docker host.

	53 Volumes in Kubernetes
	   Challenges : On-disk files in a container are ephemeral
	   When there are multiple conatiners who wants to share data, it
	   becomes a challenge.
	   One of the benefits of Kubernetes is that it supports multiple types 
	   of volumes : EBS, cider, glusterfs, local , nfs etc
	 
	   apiVersion: v1
	   kind: Pod
	   metadata:
		named: demopod-volume
	   spec:
		conatiners:
		- image: nginx
		  name: test-container
		  volumeMounts:
		  - mountPath: /data
		    name: first-volume
		  volumes:
		  - name: first-volume
		    hostpath:
			path: /mydata
			type: Directory
	   
	54  PersistentVolume and PersistentVolumeClaim
	    Generally developers creates the YAML files associated with pods
	    that they want to deploy.
	    In a scenario of directly referencing to volumes, developer need to
	    be aware of configurations.
	    
	    A persistentVolume(PV) is a piece of storage in the cluster that has 
	    been provisioned by an administrator or dynamically provisioned
	    using Storage Classes
	    Every Volume which is created can be of different type.
	    This can be taken care by the Storage Administrator/ Ops Team
	
	    A persistentVolumeClaim is a request for the storage by a user.
	    within the claim, user need to specify the size of the volume 
	    along with the access mode.
	    
	    Steps:
	    1. Storage Administrator takes care of creating PV
	    2. Developer can raise a "Claim" ( I want a specific type of PV)
	    3. Reference that claim within the PodSpec file.
	
	    apiversion: v1
	    kind: PersistentVolume
	    metadata:
		name: block-pv
	    spec:
		storageClassName: manual
		capacity:
		  storage: 10Gi
		accessModes:
		  - ReadWriteOnce
		hostpath:
		  path: /tmp/data

	        
	    apiversion: v1
            kind: PersistentVolumeClaim
            metadata:
                name: pvc
            spec:
                storageClassName: manual
                accessModes:
                  - ReadWriteOnce
                resources:
                  requests:
			storage: 10Gi

	    kind: Pod
	    apiVersion: v1
	    metadata:
		name: kplabs-pvc
	    spec:
		containers:
		- name: my-frontend
		  image: nginx
		  volumeMounts:
		  - mountPath: "/data"
		    name: my-volume
		volumes:
		  - name: my-volume
		    persistentVolumeClaim
			claimName: pvc

     55 Static vs Dynamic Provisioning
	2 ways PVs may be provisioned : statically or dynamically
	Static - A cluster administrator creates a number of PVs
	Dynamic - When none of the static PVs the administrator created matches
	a users PersistentVolumeClaim, the cluster may try to dynamically
	provision a volume specialy for the PVC.
	
	Kubectl get pv
	(https://www.digitalocean.com/docs/kubernetes/how-to/add-volumes/)
	
     56 ConfigMaps
	Sample Use-Case
	Dev Environment app.env=dev app.mem=2048m app.properties=dev.env.url
	Prod Environment app.env=prod app.mem=8096m app.properties=prod.env.url

	kubectl create configmap [NAME][DATA-SOURCE]
	Data-Source can be File, directory and literal value
	kubectl get configmap
	kubectl create configmap dev-config --from-literal=app.mem=2048m
	kubcetl create configmap dev-properties --from-file=dev.properties
	
	apiVersion: v1
	kind: Pod
	metadata:
	   name: configmap-pod
	spec:
	  containers:
	    - name: test-container
	      image: nginx
	      volumeMounts:
	      - name: config-volume
		mountpath: /etc/config
	  volumes:
	    - name: config-volume
	      configMap:
		name: dev-properties
	  restartPolicy: Never

     57 Security Context
	When you run a container, it runs with the UID 0 ( Administrative
	Privilege)
	In-case of container breakouts, attacker can ger root privileges to your
	entire system.
	It is recommended to run a container with limited privilge used instead
	of the ROOT user.
	Three important permission Aspects
	runAsUser - Specifies the user of the running process in containers
	runAsGroup - Specified the primary group for all process within
	containers.
	fsGroup - Applies the settings to the volumes. Volumes which support
	ownership management are modified to be owned and writable by the GID
	specified in fsGroup.
	
	apiVersion: v1
	kind: Pod
	metadata:
		name: pod-context
	spec:
		securityContext:
		  runAsUser: 1000
		  runAsGroup: 3000
		containers:
		- name: sec-ctx-demo
		  image: busybox
		  command: ["sh", "-c", "sleep 1h" ]

	
	apiVersion: v1
	kind: Pod
	metadata:
	  name: security-context-demo
	spec:
	  securityContext:
	    runsAsUser: 1000
	    runAsGroup: 3000
	    fsGroup: 2000
	  volumes:
	  - name: sec-ctx-vol
	    emptyDir: {}
	  containers:
	  - name: sec-ctx-demo
	    image: busybox
	    command: ["sh", "-c" , "sleep 1h" ]
	    volumeMounts:
	    - name: sec-ctx-vol
	      mpounthpath: /data/demo
	
     58 Storage Volume Expansion
	It can happen that your persistent volume has become full and you need
	to expand the storage for additional capacity
	kubectl get storageclass
	kubectl storageClass do-block-storage
	(check if allowedVolumeExpansion is set to True)
	next resize the PVC
	next restart the POD
	
	(https://docs.google.com/document/d/1BWhLJSJUXRZWpu2YkhXJsY_fF0DN-4OzHDUD51p72eE/edit?usp=sharing)
     59 Configuring Kubernetes cluster with Kubeadm
	Kubeadm allows us to quickly provision a secure Kubernetes cluster.
	https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm
	Create a Master and Slave Ubuntu Instance.
	Install Docker by following below steps
	  apt-get update 
	  apt-get -y install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
	  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
	  add-apt-repository "deb [arch=amd64] https://download.com/linux/ubuntu $(lsb_release -cs) stable
	  apt-get update && apt-get -y install docker-ce docker-ce-cli
	  systemctl start docker
	Configuration: (master node)
	  cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
	  net.bridge.bridge-nf-call-ip6tables = 1
	  net.bridge.bridge-nf-call-iptables = 1
	  EOF
	  sudo sysctl --system
	Installation:
	  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
	  cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
	  dev https://apt.kubernetes.io/ kubernetes-zenial main
	  EOF
	  apt-get update
	  apt-get install -y kubelet kubeadm kubectl 
	  apt-mark hold kubelet kubeadm kubectl
	  
	Initialization
	  kubeadm init -pod-network-cidr=10.244.0.0/16
	Tp start using your cluster you need to run the following as a regular user:
	mkdir -p $HOME/.kube
	sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
	sudo chown $(id -u):$(id -g) $HOME/.kube/config

	below command needs to be run only on worker node.This will form the
cluster however the nodes will not be in ready state.
	kubeadm join 139.59.13.209:6443 --token x3xsb9.17inx25d7ekx6t1z \
	     --discovery-token-ca-cert-hash
	sha256:510b26b3658ecdab85427c3c92b3a5ace1b7abb5e55bd75302e70d873684ea1b
	
	next once the cluster is formed by above command run below command
	kubectl apply -f
	https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
	Now both nodes will be ready state.	
	
	
     60 Upgrading Kubeadm Clusters
	Following are the high-level steps required to perfrom kubeadm upgrade.
	1. Find the latest version to upgrade to.
	2. Unhold the kubeadm binary
	   apt-mark unhold kubelet
	3. Download the latest kubeadm binary(apt-get install)
	   apt-get install -y kubeadm=1.19.1-00
	4. Drain the control plane node.
	   kubectl drain <cp-node-nam> --ignore-daemonsets
	5  Perform the Upgrade Plan
	   sudo kubeadm upgrade plan
	6  Choose version to upgrade and run appropriate command
	   kubeadm upgrade apply v1.19.3
	   kubectl uncordon kubeadm-master
	7  Upgrade the kubelet and kubectl binaries
	   apt-mark unhold kubelet kubectl && 
	   apt-get update && apt-get install -y kubelet=1.19.1-00
kubectl=1.19.1.-00
	   apt-mark hold kubelet kubectl
	   systemctl daemon-reload
	   systemctl restart kubelet
	   
     61 Kubernetes from Scratch
	  Create a master and slave ubuntu server
          Server - Contains binarie for master server components like API 
	  server , controller manager, scheduler and others.
	  Node - Contains binaries required by worker nodes kubelet, i
	  kube-proxy
	  Client - Binaries required by client, Kubectl
	  https://kubernetes.io/docs/setup/release/notes
	  Note : etcd is a seperate repo as its not managed by Kubernetes
	  
	  There are multiple components which will be communicating with each
other
	  When communication is in plain-text, it is prone to lot of attacks.
	  hence it is importnat to have secure communication between multiple
components. This can be achieved with the help of certificates.
	  certificate Authority is an entity which issues digital certificates.
	 key part is that both the receiver and the sender trusts the CA.
	 1. Creating a private key for certificate Authority
	   openssl genrsa -out ca.key 2048
	 2. Creating CSR
	   openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
	 3. Self-Sign the CSR
	   openssl x509 -req -in ca.csr -signkey ca.key -CAcreateserial -out
ca.crt -days 1000
	 
	   To view certificate
	   openssl x509 -in ca.crt -text -noout
	   
           Configuring etcd
           etcd is a distrubted reliable key-value store.
	   etcd reliably stored the configuration data of the kubernetes
cluster, representing the state of the cluster at any given point of time.
	   Pre-Requisite:
	   setenforce 0
	   sed -i 's/^SELINUX=encforcing$/SELINUX=permissive/'
/etc/selinux/config
	   step1 : Configure the Certificates
	   openssl genrsa -out etcd.key 2048
	   cd /root/certificates
	   cat > etcd.cnf <<EOF
	   [req]
	   req_extensions = v3_req
	   distinguishred_name = req_distinguished_name
	   [req_distinguished_name]
	   [v3_req]
	   basicContraints = CA:FALSE
	   keyUsage = nonRepudiation, digitalSignature, keyEncipherment
	   subjectAltName = @alt_names
	   [alt_names]
	   IP.1 = SERVER-IP-HERE
	   IP.2 = 127.0.0.1
	   EOF

	   openssl req -new -key etcd.key -sub "/CN=etcd" -out etcd.csr -config etcd.cnf
	   openssl x509 -req -in etcd.csr -CA ca.crt -CAkey ca.key
-CAcreateserial -out etcd.crt -extensions v3_req
	   step2: copy the Certificates and key to /etc/etcd
	    mkdir /etc/etcd
	    cp etcd.crt etcd.key ca.crt /etc/etcd
	   step 3: Copy the ETCD and ETCDCTL Binaries to the path
	   cd /root/binaries/kubernetes/server/bin/etcd-v3.4.10-linux-amd64/
	   cp etcd etcdctl /usr/local/bin/
	   step 4: Configure the systemd File
	   Add IP Address to the Environment Varoable of Server_IP
	   SERVER_IP=YOUR-IP-ADDRESS-HERE
	   
	   create a service file
	   CAT <<EOF | sudo tee /etc/systemd/system/etcd.service
	   [Unit]
	   Description=etcd
	   Documentation=https://github.com/coreos

	   [Service]
	   ExecStart=/usr/local/bind/etcd \\
	    --name master-1 \\
	    --cert-file=/etc/etcd/etcd.crt \\
	    --key-file=etc/etcd/etcd.key \\
	    --peer-cert-file=/etc/etcd/etcd.crt \\
	    --peer-key-file=/etc/etcd/etcd.key \\
	    --trusted-ca-file=/etc/etcd/ca.crt \\
	    --peer-trusted-ca-file=/etc/etcd/ca.crt \\
	    --peer-client-cert-auth \\
	    --client-cert-auth \\
	    --initial-advertise-peer-urls https://${SERVER_IP}:2380 \\
	    --listen-peer-urls https://${SERVER_IP}:2380 \\
	    --listen-client-urls https://{SERVER_IP}:2379,
              https://127.0.0.1:2379 \\
	    --advertise-client-urls https://${SERVER_IP}:2379 \\
	    --initial-cluster-token etcd-cluster-0 \\
	    --initial-cluster master-1=https://${SERVER_IP}:2380 \\
	    --initial-cluster-state new \\
	    --data-dir=/var/lib/etcd
	   Restart=on-failure
	   RestartSec=5
	
	   [Install]
	   WantedBy=multi-user.target
	   EOF	

	   systemctl start etcd
	   systemctl status etcd
	   systemctl enable etcd
	   
	   Verification Commands:
	   when we try with etcdctl --endpoints=https://127.0.0.1:2379 get foo,it gives unkown certificate authority

	   ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379
--cacert=/etc/etcd/ca.crt --cert=/etc/etcd/etcd.crt --key=/etc/etcd/etcd.key put course "kplabs cka course is awesome"
	   
	   step5 : Configuring API server
	   API server acts as a gateway to the Kubernetes Cluster
	   When you intercat with you Kubernetes cluster using the kubectl command-line interface, you are actually communicating with the master API server component.

	   kubectl get serviceaccount
	   kubectl get serviceaccount -o yaml
	   kubectl get secret default-token-xkhxd - o yaml
	   kubcetl create serviceaccount demo
	   kubectl get serviceaccount demo -o yaml
	   kubectl get secert <name from above commnd> -o yaml
	   
	   TokenController runs as part of kube-controller-manager. It
acts asynchronously. It watches ServiceAccount creation and creates a corresponding ServiceAccount token Secret to allow API access.
watches ServiceAccount deletion and deletes all corresponding ServiceAccount token Secrets.
watches ServiceAccount token Secret addition, and ensures the referenced ServiceAccount exists, and adds a token to the Secret if needed.
watches Secret deletion and removes a reference from the corresponding
ServiceAccount if needed.
	   
	   Pre-requisite: Move the Kube-apiserver binary to the /usr/bim directory
	   cd /root/binaries/kubernetes/server/bin
	   cp kube-apiserver /usr/local/bin
	
	   Step 1: Generate Configuration File for CSR Creation 
	   cd /root/certificates
           cat <<EOF | sudo tee api.conf
           [req]
           req_extensions = v3_req
           distinguishred_name = req_distinguished_name
           [req_distinguished_name]
           [v3_req]
           basicContraints = CA:FALSE
           keyUsage = nonRepudiation, digitalSignature, keyEncipherment
           subjectAltName = @alt_names
           [alt_names]
           DNS.1 = kubernetes
           DNS.2 = kubernetes.default
	   DNS.3 = kubernetes.default.svc
	   DNS.4 = kubernetes.default.svc.cluster.local
	   IP.1 = 127.0.0.1
	   IP.2 = ${SERVER_IP}
	   IP.3 = 10.32.0.1
	   EOF

	   step2 : Generate certificates for API Server
	   openssl genrsa -out kube-api.key 2048
	   openssl req -new -key kube-api.key -subj "/CN=kube-apiserver" - out kube-api.csr -config api.conf
	   openssl x509 -req -in kube-api.csr -CA ca.crt -CAkey ca.key
-CAcreateserial -out kupe-api.crt -extension v3_req -extfile api.conf -days 1000
	  step3 : Generate Certificate for Service Account 
	   openssl genrsa -out service-account.key 2048
	   openssl req -new -key service-account.key -subj
"/CN=service-accounts" - out service-account.csr
	   openssl x509 -req -in service-account.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out service-account.crt - days 100
	   
	  step4: copy the certificate files to /var/lib/kubernetes directory
	  mkdir /var/lib/kubernetes
	  cp etcd.crt etcd.key ca.crt kupe-api.key kube-api.crt
service-account.crt service-account.key /var/lib/kubernetes
	  
	 step5: Creating Encryption key and Configuratiom
	 ENCRYPTION_KEY= $(head -c 32 /dev/urandom | base64)

	 cat > encryption-at-rest.yaml << EOF
	 kind: EncryptionConfig
	 apiVersion: v1
	 resources:
	 - resources:
	    - secrets
	   providers:
	    - aescbc
	        keys:
	         - name: key1
	           secret: ${ENCRYPTION_KEY}
            - identity: {}
          EOF
	  
         cp encryption-at-rest.yaml /var/lib/kubernetes
	 
	step 6 : Creating Systemd service file:
	
	systemd file:

	cat << EOF | sudo tee /etc/systemd/system/kube-apiserver.service
	[Unit]
	Description=Kubernetes API Server
	Documentation=https://github.com/kubernetes/kubernetes

	[Service]
	ExecStart=/usr/local/bin/kube-apiserver \
	--advertise-address=139.59.72.179 \
	--allow-priviledged=true \	
	--authorization-mode=Node,RBAC \
	--client-ca-file=/var/lib/kubernetes/ca.crt \
	--enable-admission-pligins=NamespaceLifecycle,NodeRestriction,LimitRanger,ServiceAccount,DefaultStorage
	--enable-bootstrap-token-auth=true \
	--etcd-cafile=/var/lib/kubernetes/ca.crt \
	--etcd-certfile=/var/lib/kubernetes/etcd.crt \
	--etcd-keyfile=/var/lib/kubernetes/etcd.key \
	--etcd-servers=https://127.0.0.1:2379 \
	--kubelet-certificate-authority=/var/lib/kubernetes/ca.crt \
	--kubelet-client-certificate=/var/lib/kubernetes/kube-api.crt \
	--kubecelt-client-key=/var/lib/kubernetes/kube-api.key \
	--kubelet-https=true \
	--service-account-key-file=/var/lib/kubernetes/service-account.crt \
	--service-cluster-ip-range=10.32.0.0/24 \
	--tls-cert-file=/var/lib/kubernetes/kube-api.crt \
	--tls-private-key-file=/var/lib/kubernetes/ca.crt \
	--service-node-port-range=30000-32767 \
	--service-node-port-range=30000-32767 \
	--audit-log-maxage=30 \
	--audit-log-maxbackup=3 \
	--audit-log-maxsize=100 \
	--auidt-log-path=/var/log/kube-api-audit.log \
	--bind-address=0.0.0.0 \
	--event-ttl=1h \
	--encryption-provider-config=/var/lib/kubernetes/encryption-at-rest.yaml \
	--v=2
	Restart=on-failure
	RestartSec=5

	[Install]
	WantedBy=multi-user.target
	EOF

	Step7 : Start the kube-api service:
	systemctl start kube-apiserver
	systemctl status kube-apiserver
	journalctl -l -u kube-apiserver ( to debug startup issues)
        systemctl daemon-reload
	systemctl start kube-apiserver
	systemctl status kube-apiserver
	systemctl enable kube-apiserver
	netstat -ntlp ( to see all services running)

	Configuring Controller Manager
	Controller manager interacts with the API server to move the current status to deisred status. Examples Controllers: replication Controller, Service Account & Token Controllers.

	Step1 : generate certificates
	openssl genrsa -out kube-controller-manager.key 2048
           openssl req -new -key kube-controller-manager.key -subj
"/CN=system:kube-controller-manager" - out kube-controller-manager.csr
           openssl x509 -req -in kube-controller-manager.csr -CA ca.crt -CAkey
ca.key -CAcreateserial -out kube-controller-manager.crt -days 1000

	step2: Generating KubeConfig
	
	kubectl config set-cluster kubernetes-from-scratch
	--certificate-authority=ca.crt \
	--embed-certs=true \
	--server=https://127.0.0.1:6442 \
        --kubeconfig=kube-controller-manager.kubeconfig

	kubectl config set-credentials system:kube-controller-manager \
	--client-certificate=kube-controller-manager.crt \
	--client-key=kube-controller-manager.key \
	--embed-certs=true \
	--kubeconfig=kube-controller-manager.kubeconfig

	kubectl config set-context default \
	--cluster=kubernetes-from-scratch \
	--user=system:kube-controller-manager \
	--kubeconfig=kube-controller-manager.kubeconfig

	kubectl config use-context default
-kubeconfig=kube-controller-manager.kubeconfig

	step 3: Copying the files to kubernetes directory
	cp kube-controller-manager.crt kube-controller-manager.key
kube-controller-manager.kubeconfig /var/lib/kubernetes/

	step4: Configuring SystemD service file:
	cat <<EOF | sudo tee /etc/systemd/system/kube-controller-manager.service
	[Unit]
	Description=Kubernetes Controller Manager
	Documentation=https://github.com/kubernetes/kubernetes

	[Service]
	ExecStart=/usr/local/bin/kube-controller-manager \\
	--address=0.0.0.0 \\
	--service-cluster-ip-range=10.32.0.0./24 \\
	--cluster-cidr=10.200.0.0/16 \\
	--kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
	--authentication-kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
	--authirization-kubeconfig=/var/lib/kubernetes/kube-controller-manager.kubeconfig \\
	--leader-elect=true \\
	--cluster-signing-cert-file=/var/lib/kubernetes/ca.crt \\
	--cluster-signing-key-file=/var/lib/kubernetes/ca.key \\
	--root-ca-file=/var/lib/kubernetes/ca.crt \\
	--service-account-private-key-file=/var/lib/kubernetes/service-account.key \\
	--use-service-account-credentials=true \\
	--v=2
	Restart=on-failure
	RestartSec=5
	
	[Install]
	WantedBy=multi-user.target
	EOF

	start Controller Manager:
	cp /root/binaries/kubernetes/server/bin/kube-controller-manager
/usr/local/bin
	systemctl start kube-controller-manager
	systemctl status kube-controller-manager
	systemctl enable kube-controller-manager

	
       Configuring Scheduler
       A scheduler watches for newly created Pods that have no Node assigned
	Every Pod that the scheduler discovers, the scheduler becomes
responsible for finding the best Node for that Pod to run on.
		

i	1. generate Certificates
	openssl genrsa -out kube-scheduler.key 2048
        openssl req -new -key kube-scheduler.key -subj
"/CN=system:kube-scheduler" -out kube-scheduler.csr
        openssl x509 -req -in kube-scheduler.csr -CA ca.crt -CAkey ca.key
-CAcreateserial -out kube-scheduler.crt -days 1000
	
	2. generate Kubeconfig file:
	
	{
	  kubectl config set-cluster kubernetes-from-scratch \
	   --certificate-authority=ca.crt \
	   --embed-certs=true \
	   --server=https://127.0.0.1:6443 \
	   --kubeconfig=kube-scheduler.kubeconfig

	  kubectl config set-credentails system:kube-scheduler \
	   --client-certificate=kube-scheduler.crt \
	   --client-key=kube-scheduler.key \
	   --embed-certs=true \
	   --kubeconfig=kube-scheduler.kubeconfig 

	  kubectl config set-contrext default \
	   --cluster=kubernetes-from-scratch \
	   --user=system:kube-scheduler \
	   --kubeconfig=kube-scheduler.kubeconfig
	
	  kubectl config user-context defualt
--kubeconfig=kube-scheduler.kubeconfig

	 }
	3 Copy the scheduler kubeconfig
	
	cp kube-scheduler.kubeconfig /var/lib/kubernetes/

	4. Configuring SystemD service:
	
	  cat<< EOF | sudo tee /etc/systemd/system/kube-scheduler.service
	  [Unit]
	  Decsription=Kubernetes Scheduler
	  Documentation=https://github.com/kubernetes/kubernetes

	  [Service]
	  ExecStart=/usr/local/bin/kube-scheduler \\
	   --kubeconfig=/var/lib/kubernetes/kube-scheduler.kubeconfig \\
	   --authentication-kubeconfig=/var/lib/kubernetes/kube-scheduler.kubeconfig
\\
	   --authorization-kubeconfig=var/lib/kubernetes/kube-scheduler.kubeconfig
\\
	   --bind-address=127.0.0.1 \\
	   --leader-elect=true
	  Restart=on-failure
	  RestartSec=5 

	  [Install]
	  WantedBy=multi-user.target
	  EOF

	  Step 5: verification

	  cp /root/binaries/kubernetes/server/bin/kube-scheduler /usr/local/bin
	  systemctl start kube-scheduler
	  systemctl status kube-scheduler
	  systemctl enable kube-scheduler
	
	  Validating Cluster Status
	 ------------------------------------------
	  kubectl get componentstatus
	  
	  Step 1: Generate Certificate for Administrator user
	 
	  cd /root/certificates
	  openssl genrsa -out admin.key 2048
	  openssl req -new -key admin.key -subj "/CN=admin/O=system:masters"
-out admin.csr
	  openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key
-CAcreateserial -out admin.crt -days 1000

	   Step 2: Create KubeConfig file

	  Note: replace the IP address from the below snippet in line 5 with
your IP address
	  
	  {
	    kubectl config set-cluster kubernetes-from-scratch \
	    --certificate-authority=ca.crt \
	    --embed-certs=true \
	    --server=https://134.209.159.37:6443 \
	    --kubeconfig=admin.kubeconfig

	    kubectl config set-credentials admin \
	    --client-certificate=admin.crt \
	    --client-key=admin.key
	    --embed-certs=true \
	    --kubeconfig=admin.kubeconfig 

	   
	    kubectl config set-context default \
	    --cluster=kubernetes-from-scratch \
	    --user=admin \
            --kubeconfig=admin.kubeconfig
	   
	   kubectl config user-context default --kubeconfig=admin.kubeconfig 
          }

	 Step 3: verify Kubernetes Objects Creation

	 mkdir ~/.kube && cp /root/certificates/admin.kubeconfig ~/.kube/config
	kubectl create namespace kplabs
	kubectl get namespace kplabs -o yaml
	kubectl get serviceaccount --namespace kplabs
	kubectl get serviceaccount default -o yaml -n kplabs
	kubectl get secret --namespace kplabs
	kubectl get componentstatus --kubeconfig=admin.kubeconfig
	
	Worker Node Configuration
	-----------------------------------------------
	There are two important components to configure as part of the worker node. kubelet and kube-proxy
	
	Pre-Requisites
	-------------------
	setenforce 0
	sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
	yum -y install docker && systemctl start docker && systemctl enable
docker
	yum -y install socat conntrack ipset
	sysctl -w net.ipv4.conf.all.forwading=1

	cd /root/binaries/kubernetes/node/bin/
	cp kube-proxy kubectl kubelet /usr/local/bin

	step 1: Generate kubelet certificate fr Worrker Node.
	
	Note:

	replace the IP Address and Hostname filed in the below configurations according to environment. 
	Run this in the Kubernetes Master Node
	cd /root/certificates

	cat > openssl-kplabs-cka-worker.cnf <<EOF
	[req]
	req_extensions = v3_req
	distinguished_name = req_distinguished_name
	[req_distinguished_name]
	[ v3_req ]
	basicConstraints - CA:FALSE
	keyUsage = nonRepudiation, digitalSignature, keyEncipherment
	subjectAltName = @alt_names
	[alt_names]
	DNS.1 = kplabs-cka-worker
	IP.1 = WORKER-NODE-IP
	EOF

	openssl genrsa -out kplabs-cka-worker.key 2048
	openssl req -new -key kplabs-cka-worker.key -subj
"/CN=system:node:kplabs-cka-worker/O=system:nodes" -out kplabs-cka-worker.csr -config openssl-kplabs-cka-worker.cnf
	openssl x509 -req -in kplabs-cka-worker.csr -CA ca.crt -CAkey ca.key
-CAcreateserial -out kplabs-cka-worker.crt -extensions v3_req -extfile
openssl-kplabs-cka-worker.cnf -days 1000
	
	Step 2: Generate kube-proxy certificate:
	
	openssl genrsa -out kube-proxy.key 2048
	openssl req -new -key kube-proxy.key -subj "/CN=system:kube-proxy" -out kube-proxy.csr
	openssl x509 -req -in kube-proxy.csr -CA ca.crt -CAkey ca.key
-CAcreateserial -out kube-proxy.crt -days 1000

	Step 3 : Copy certificates to Worker Node:

	This can either be manual approach or via SCP. certificates:
	kubelet, kube-proxy and CA certificate.

	in-case you want to autoamte it, then following configuration can be
used. In the demo, we had made used 
	in-case you want to trasnfer file from master worket node, then you can make use of the folowing app

	Worker Node:
	nano /etc/ssh/sshd_config
	PsswordAuthentication yes
	systemctl restart sshd
	useradd zeal
	passwd zeal5872#

	Master Node:
	scp kube-proxy.crt kube-proxy.key kplabs-cka-worker.crt
kplabs-cka-worker.key ca.crt zeal@IP-WORKER-NODES:/tmp
	
	worker Node:
	mv kube-proxy.crt kube-proxy.key kplabs-cka-worker.crt
kplabs-cka-worker.key ca,crt /root/certificates
	
	step4: Move certificates to specific Location
	cd /root/certificates
	mkdir /var/lib/kubernetes
	cp ca.crt /var/lib/kubernetes
	mkdir /var/lib/kubelet
	mv kplabs-cka-worker.crt kplabs-cka-worker.key kube-proxy.crt
kube-rpoxy.key /var/lib/kubelet/

	step 5 Generate Kubelet Configuration YAML File:

	cat <<EOF | sudo tee /var/lib/kubelet/kubelet-config.yaml
	kind: KubeletConfiguration
	apiVersion: kubelet.config.k8s.io/v1beta1
	authentication:
	  anonymous:
	    enabled: false
	  webhook:
	    enabled: true
	  x509:
	    clientCAFile: "/var/lib/kubernetes/ca.crt"
	authorization:
	   mode: Webhook
	clusterDomain: "cluster.local"
	clusterDNS:
	- "10.32.0.10"
	runtimeRequestTimeout: "15m"
	EOF

	step6 : Generate systemd service file for kubelet:
	
	cat <<EOF | sudo tee /etc/systemd/system/kubelet.service
	[Unit]
	Description=Kubernetes Kubelet
	Documentation=https://github.com/kubernetes/kubernetes
	After=docker.service
	Requires=docker.service

	[Service]
	ExecStart=/usr/local/bin/kubelet \\
	 --config=/var/lib/kubelet/kubelet-config.yaml \\
	 --image-pull-progress-deadline=2m \\
	 --kubeconfig=/var/lib/kubelet/kubeconfig \\
	 --tls-cert-file=/var/lib/kubelet/${HOSTNAME}.crt \\
	 --tls-private-key-file=/var/lib/kubelet/${HOSTNAME}.key \\
	 --network-plugin=cni \\
	 --regster-node=true \\
	 --v=2 \\
	 --cgroup-driver=systemd \\
	 --runtime-cgroups=/systemd/system.slice \\
	 --kubelet-cgroups=/systemd/system.slice
	Restart=on-failure
	RestartSec=5

	[Install]
	WantedBy=multi-user.target
	EOF

	Step 7 : Generate the Kubeconfig file for Kubelet

	cd /var/lib/kubelet
	cp /var/lib/kubernetes/ca.crt .
	SERVER_IP = IP-OF-API-SERVER

	{
	  kubectl config set-cluster kubernetes-from-scratch \
	   --certificate-authjority=ca.crt \
	   --embed-certs=true \
	   --server=https://${SERVER_IP}:6443 \
	   --kubeconfig=kplabs-cka-worker.kubeconfig

	  kubectl config set-credentails system:node:kplabs-cka-worker \
	   --client-certificate=kplabs-cka-worker.crt \
	   --client-key=kplabs-cka-worker.key \
	   --embed-certs=true \
	   --kubeconfig=kplabs-cka-worker.kubeconfig
	
	  kubectl config set-context default \
	   --cluster=kubernetes-from-scratch \
	   --user=system:node:kplabs-cka-worker \
	   --kubeconfig=kplabs-cka-worker.kubeconfig

	 kubectl config user-context default
--kubeconfig-kplabs-cka-worker.kubeconfig
	
	mv kplabs-cka-worker.kubeconfig kubeconfig

	part 2 Kube-Proxy

	Step 1: Copy Kube Proxy certificate to Directory:
	  mkdir /var/lib/kube-proxy

	Step 2: generate KubeConfig file:

	{
	   Kubectl config set-cluster kubernetes-from-scratch \
	     --certificate-authority=ca.crt \
	     --embed-certs=true \
	     --server=https://${SERVER_IP}:6443 \
	     --kubeconfig=kube-proxy.kubeconfig
		
	   kubectl config set-credentials system:kube-proxy \
	    --client-certificate=kube-proxy.crt \
	    --client-key=kube-proxy.key \
	    --embed-certs=true \
	    --kubeconfig=kube-proxy.kubeconfig

	   kubectl config set-context default \
	     --cluster=kubernetes-from-scratch \
	     --user=system:kube-proxy \
	     --kubeconfig=kube-proxy.kubeconfig

	  kubectl config use-context default -kubeconfig=kube-proxy.kubeconfig
	}
	mv kube-proxy.kubeconfig /var/lib/kube-proxy/kubeconfig


	Step 3: generate kube-proxy configuration file:
		
	   cat <<EOF | sudo tee /var/lib/kube-proxy/kube-proxy-config.yaml
	   kind: KubeProxyConfiguration
	   apiversion: kubeproxy.config.k8s.io/v1alpha1
	   clientConnection:
	     kubeconfig: "/var/lib/kube-proxy/kubeconfig"
	  mode: "iptables"
	  clusterCIDR: "10.200.0.0.16"
	  EOF

	Step 4: Create kube-proxy service file:
	  cat <<EOF | sudo tee /etc/systemd/system/kube-proxy.service
	  [Unit]
	  Description=Kubernetes Kube Proxy
	  Documentation=https://github.com/kubernetes/kubernetes

	  [Service]
	  ExecStart=/usr/local/bin/kube-proxy \\
	    --config=/var/lib/kube-proxy/kube-proxy-config.yaml
	 Restart=on-failure
	 RestartSec=5

	  [Install]
	  WantedBy=multi-user.target
	  EOF

	  Step 5
	
	  systemctl start kubelet
	  systemctl start kube-proxy
	  systemctl enable kubelet
	  systemctl enable kube-proxy
	  
	  Configuring Networking
	  ----------------------------------
	  note: step1 to 3 will be on worker node step4 will be on Master node.
	  step 1 : Download CNI Plugins:
https://github.com/containernetworking/plugins/releases/download/v0.8.6/cni-plugins-linux-amd64-v0.8.6.gz

	  step2: configure Base Directories:
	  mkdir -p \
	  /etc/cni/net.d \
	  /opt/cni/bin \
	  /var/run/kubernetes 

	  step3: Move CNI tar File and Extract it.
	  
	  mv cni-plugins-linux-amd64-v0.8.6.tgz /opt/cni/bin
	  cd /opt/cni/bin
	  tar -xzvf cni-plugins-linux-amd64-v0.8.6.tgz

	  step4: Configuring weave ( Run this step on Master Node)
	  kubectl apply -f
"https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 |
tr -d '\n')&env.IPALLOC_RANGE=10.200.0.0/16"
	  
	  RBAC for API to kubelet Communication
	  -------------------------------------------
	  cat<<EOF | kubectl apply -f -
	  apiVersion: rbac.authorization.k8s.io/v1beta1
	  kind: ClusterRole
	  metadata:
	    annotations:
	     rbac.authorization.kubernetes.io/autoupdate: "true"
	    labels:
	     kubernetes.io/bootstrapping: rbac-defaults
	    name: system:kube-apiserver-to-kubelet
	  rules:
	    -apiGroups:
	     - ""
	     resources:
	      - nodes/proxy
	      - nodes/stats
	      - nodes/log
	      - nodes/spec
	      - nodes/metrics
	     verbs:
	      - "*"
	   EOF
	   cat <<EOF | kubectl apply -f -
	   apiVersion: rbac.authorization.k8s.io/v1beta1
	   kind: ClusterRoleBinding
	   metadata:
	     name: system:kube-apiserver
	     namespace: ""
	   roleRef:
	     apiGroup: rbac.authorization.k8s.io
	     kind: ClusterRole
	     name: system:kube-apiserver-to-kubelet
	   subjects:
	     - apiGroup: rbac.authorization.k8s.io
	       kind: User
	       name: kube-apiserver
	   EOF
           
	   Configuring DNS in Cluster
	   ---------------------------------
	   kubectl cluster-info
	   wget
https://raw.githubusercontent.com/zealvora/certified-kubernetes-administrator/master/Domain%20-%20Installations%2CConfiguration%2CValidation/coredns.yaml
	   kubectl get pods -n kube-system
	   Kubelet Preferred Address Types
	   ------------------------------------
	   on kubectl-exec, kube-apiservefr initiate the connection with the
kubelet running on the host. In order to communicate, kube-apiserver uses ways
like IP address, hostnames of the nodes 
           Backup & Restore etcd
	   -------------------------------------
	   backing up an etcd cluster can be accomplished in tow ways:
	   - etcd built-in snapshot
	   - volume snapshot
	  
      62 Kubernetes Events
	 Kubernetes Events are created when other resources have state changes,
errors , or other messages that should be broadcast to the system
	It proviedes insight into what is happening insite a cluster such as
what decsiins were made by the scheduler or why some pods were evicted from the
node.
	kubectl get events
	All the events are stored in the master server.
	To avoid filling up master's disk, a retention policy is enforced:
eventars are removed one hour after the last occurrence.
	To provide longer history and aggregation capabilities, a third part
solution should be installed to capture events.
	Events are namespaced.
	Hence if you want even a pod in "kplabs-namespace" then you will have to
explicitly specify the --namespace kplabs-namespace.
	To see events from all namespaces, you can use the -all-namespaces
argument.
      63 Monitoring Cluster Components
	 One of the important functionalities of a kubelet is to retrieve
metrics aggregate and expose them through the kubelet summary API
	This is achieved with CAdvisor
	kube-state-metrics is a simple service that listens to the kubernetes
API server and generates metrics about the state of the objects.
	It is not focused on the helath of the individul kubernetes components,
but rather on the health of the various objects inside, such as deployment,
nodes and pods.
	git clone https://github.com/kubernetes-incubator/metrics-server.git
	cd metric-server/
	kubectl apply -f 1.8+/
	vi metric-server-deployment.yaml
	 command:
    		- /metrics-server
    		- --kubelet-insecure-tls
    		- --kubelet-preferred-address-types=InternalI

       64 Docker logging drivers
	  Unix and Linux commands typically open three I/O stream when they run
called STDIN, STDOUT and STDERR
	  docker container run -d --name mybusybox busybox ping google.com
	  docker ps
	  docker logs mybusybox ( to see stdout ad\nd stderr )
	  docker info | grep -i "logging driver"
	  if you dont associated a driver ir will asign to default json driver.
	  docker inspect mybusybox ( and check for Logpath to get location of
where files are getting stored)
	  docker container run -d --name mycustom --log-driver none busybox ping
google.com
	  docker logs mycustom
	  Error response from daemon: configured logging driver does not support
reading
	  There are lot of logging driver options available in Docker, some of
them include:
	  json-file
	  none
	  syslog
	  local
	  journald
	  splunk
	  awslogs
	  The docker logs command is not available for drivers other than
json-file and journald.
      65 Monitoring Application Logs
	 kind: Pod
	 apiVersion: v1
	 metadata:
	    name: pod01
	 spec:
	   containers:
	     - name: ping-container
	       image: busybix
	       command: ["ping"]
	       agrs: ["google.com"]
	 
	  kubectl apply -f pod-logging.yaml
	  kubectl get pods
	  kubectl lofs pod01
	  
	     kind: Pod
         apiVersion: v1
         metadata:
            name: pod01
         spec:
           containers:
             - name: ping-container-domain
               image: busybix
               command: ["ping"]
               agrs: ["google.com"]
	     - name: ping-container-ip
	       image: busybox
	       command: ["ping"]
	       args: ["8.8.8.8"]

          kubectl apply -f multi-contaimner-pod-logging.yaml
	  kubectl get pods
 	  kubectl logs pod02 ping-conatiner-domain  
          kubectl logs pod02 ping-container-ip
	  
        69 Monitoring Cluster Component Logs
	   If we are making use of systemd, then we can view the component level
logs with journalctl
	   It provides insight into what is happening inside a cluster, such as
what decisions were made by scheduler or why some pods were evicted from the
node.
	   /var/logs/kube-apiserver.log,
kube-scheduler.log,kube-controller-manager.log, kubelet.log kube-proxy.log
	   For the systemd based system we may need to use journalctl instead.
	   journalctl -f -u kube-apiserver
	   journalctl --seince "2019-09-17 14:10:10" --until "2019-09-17
15:00:00" -u kube-apiserver
	   
	70: Troubleshooting
	    Issue : Application failure : NodePort Service with 2 Pod1 and Pod2 not registered.
	   `	 
	   kubectl get service
	   kubectl describe service kplabs-service
	   kubectl get pods --namespace teama
	   (check the selector )
	   kubectl decsribe pods nginx-pod-first --namespace teama
	   (did not have the required label)
	   kubectl apply -f application-failure.yaml
	   (still the namespace of service and pods is diffrent)

	   issue2: Conttrol Plane failure
	   Verify if all the cluster level components are healthy
	   	kubectl get componentstatus
	   verify and dumo cluster info
	   	kubectl cluster-info dump
	   check the logs of individual components
	 	journalctl -l kube-apiserver
	   
	   issue3: version skew policy support
	   kube-apiserver - in HA clusters, the newest and oldest kube-apiserver
instance must be within one minor versions ie either if newest is v1.15 then
other supported instance is either v1.15 or v1.14
	   kubelet must not be newer than kube-apiserver and may be up to two
minor versions older e.g if newest api-server is v1.15 the kubelet version can be
v1.15, 1.14 or 1.13
	   kube-controller,kube-schduler and cloud-controller manager - Must not
be newer than the kube-apiserver. Expected to match the kube-apiserver minor
version. can be upto one monir version older (specifically during upgrades)
	    Draining worker nodes
	    kubectl drain $NODENAME --ignore-daemonsets --d;ete-local-data
	    This will gracefully terminate all the pods on the node while
marking the nodes as unschedulable. For pods with a replica set, the pod will be
replaced by a new pod which will be schedukled to a new node. Static Pods will
be terminated.
	    Taint Based Evictions
	    The worker nodes are automatically tainted in Kubernetes based on
specific node conditions:
		node.kubernetes.io/not-ready
		node.kubernetes.io/unreachable
		node.kubernetes.io/out-of-disk
		node.kubernetes.io/memory-pressure
		node.kubernetes.io/disk-pressure
		node.kubernetes.io/network-unavailable
		node.kubernetes.io/unschedulable
		
