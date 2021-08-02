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
		
