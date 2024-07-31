
URL
===

Certification Tips
====================

Commands
------------
k label pod -n sun -l type=runner protected=true

k -n sun annotate pod -l protected=true protected="do not delete this pod"
docker build -t name1:tag1 -t name1:tag2 -t name2 .

1. Use kubectl run command to create pods/deployments/repicaset
2. Create an NGINX Pod
    kubectl run nginx --image=nginx
3. Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
    kubectl run nginx --image=nginx --dry-run=client -o yaml
4. Create a deployment
    kubectl create deployment --image=nginx nginx
5. Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
    kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
6. Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
    kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml

    Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.

    kubectl create -f nginx-deployment.yaml

7. In k8s version 1.19+, we can specify the --replicas option to create a deployment with 4 replicas.
    kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml
08. External URL to install addons

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml    
git clone https://github.com/kodekloudhub/kubernetes-metrics-server.git

journalctl -u kubelet

systemctl status kubelet -n 1000 -o verbose
kubectl cluster-info

alias k=kubectl
alias ks='k -n kube-system'
alias kdr='k run --dry-run=client -o yaml'
export now='--force --grace-period=0'
export do='--dry-run=client -o yaml'
complete -F __start_kubectl k

01. Introduction
=================
    a. Into kuberneres
    b.


02. Core Concepts
=================
    1. Archicture
        1. Master Node(Control Plane)
            1. API Server(kube-apiserver - 6443)
                > Authenticate user
                > vaidate request
                > retrive data 
                > update ETCD
                > Interface with scheduler
                > Interface with kubelet
                > API Server need to know the information about other kubernetes components
                > Kubeadm configures all components as pods
                > The manifest located at: /etc/kubernets/manifests/kube-apiserever.yaml
                > /etc/systemd/system/kuber-apiserver.service(Non Kubeadm)
                > ps -aux | grep kube-apiserver(Non Kubeadm)
            2. Scheduler(10259)
                1. Only responsible to deciding which pod goes to which node.
                2. Identify the reosuce requriements for a pod and select best node to place the pod.
                3. Steps to Select best node
                    > Filter node
                    > Rank Node
                4. Kube-Scheduler can be downloaded as a package and install as a service
                5. Kubeadm deploys Kube-Scheduler manger as pod
                6. The manifest located at: /etc/kubernets/manifests/kube-scheduler.yaml
                7. /etc/systemd/system/kube-scheduler.service(Non Kubeadm)
                8. ps -aux | grep kube-scheduler(Non Kubeadm)

            3. ETCD(2379)
                1. install etcd
                2. ./etcd
                3. Default port 2379
                4. etcdctl - cli to mange ETCD
                5. Any achange in cluster is updated in ETCD
                6. stores data in directory /registery
                7. etcdctl commands
                    etcdctl snapshot save 
                    etcdctl endpoint health
                    etcdctl get
                    etcdctl put
                8. Kubeadm deploys kube-etcd as pod
                9. The manifest located at: /etc/kubernets/manifests/kube-etcd.yaml
                10. /etc/systemd/system/kube-etcd.service(Non Kubeadm)
                11. ps -aux | grep kube-etcd(Non Kubeadm)                 

            4. Kubelet(10250)
                1. Master in worker node and runs on each node
                2. Regester node with Kubernetes cluster
                3. Create PODs
                4. Monitor Nodes an PODS
                5. Kubelet can be downloaded as a package and install as a service
                6. Kubeadm does not install kubelet on a node.
                7. /etc/systemd/system/kubelet.service(Non Kubeadm)
                8. ps -aux | grep kubelet(Non Kubeadm)

            5. Controller(Kube-Contoller-Manager)
                1. watch status
                2. take action
                3. Continously monitor kubernetes status and take back the state in to desired state
                4. Node Controller
                    > Checks the status of node for every 5 secs(Monitor Interval)
                    > If a node not responed for 40 secs , then make the node as down (Node Monitor Grace Prriod)
                    > Wait for 5 mins to remove the pod and move it to another node(POD Eviction Timeout)
                5. Replicaset Controller
                    > Monotor all replicaset and ensure all pods are in desired state
                6. There are other controllers persent in kubernetes 
                    > Deployment Controller
                    > Namespace Controller
                    > EndPoint Controller
                    > Job-Controller
                    > PV Protection Controller
                    > Service account controller
                    > Statefulset controller
                7. Kube-Contoller-Manager can be downloaded as a package and install as a service
                    > User can modify default configuration values
                    > provides an option to enable/disable different controllers mentioned in 4-6
                8. Kubeadm deploys kubecontroller manger as pod
                9. The manifest located at: /etc/kubernets/manifests/kube-controller-manager.yaml
                10. /etc/systemd/system/kube-controller-manager.service(Non Kubeadm)
                11.  ps -aux | grep kube-controller-manager(Non Kubeadm)


            6. Network(kube-proxy)
                1. Pod networking solution is provided by kube-proxy
                2. run on each node
                3. Looks for new services and create a rule to forward the request to the new services
                4. kube-proxy can be downloaded as a package and install as a service
                    > User can modify default configuration values
                5. Kubeadm deploys kube-proxy as pod - Deployed as demonsets
                6. The manifest located at: /etc/kubernets/manifests/kube-proxy.yaml
                7. /etc/systemd/system/kube-proxy.service(Non Kubeadm)
                8. ps -aux | grep kube-proxy(Non Kubeadm)
        2. worker Node
            1. Kubelet
            2. Network(kube-proxy)

    2. Pods
        1. Pod is a single instace of an application
        2. 1x1 mapping with containers. Means single pod contains single contianer
        3. But POD suppoters multiple containers also. 
            > ex: Application conianer, helper container in a single pod
            > shares same namespace, storage, network, etc.

    3. Manifest file 
        1. YAML for different kind
        '''
        apiVersion: v1
        kind: Pod
        metadata
            name: 
            labels:
                app: myapp
                type: front-end
        spec:
            containers:
                - name: nginx-container
                    image: nginx
        '''

    4. Practical - Kubectl
        1. kubectl get pods : Get pods
        2. kubectl run nginx --image=nginx : run pod
        3. kubectl describe pods <pod-name>
        4. kubectl get pods -o wide 
        5. kubectl run redis --image=redis123 --dry-run-client -o yaml > sample.yaml
        6. kubectl edit pod redis : To edit the running pod

    5. Replication Controller
        1. High Avaialbility
        2. Load Banancing & Scaling
        3. Relication controller & ReplicaSetde
            > works accross nodes(ACS Domain)
            > Relication controller - Old
                - creates and manges replication of pod
            > ReplicaSet - New
                - creates and manges replication of pod
                - Groups replicas based on Labels
                - Montor the replicas based on Labels, if one pod goesdown on a label then replciaset starts another pod
                - MatchLabels will be used to monitor the pods
                - Scale - Scale the replicas manully using command
                    > Update the yaml file field 'replicas' to a new number
                - Autoscaling will be done by HPA
                - Labels and selectors
                    - Labels are specified to label pods
                    - Selectors is an atteibute in rc/rs to identify pods to monitor and control

        4. Commands
            1. kubectl create -f replicaset.yaml
            2. kubectl get replicaset 
            3. kubectl delete replicaset <replocaset-name>
            4. kubectl replace -f replicaset.yaml
            5. kubectl scale --replicas=6 -f replicaset.yaml
            6. kubectl scale --replicas=6 replicaset(type) myapp-replicaset(name)
        5. Sample YAML
          apiVersion: apps/v1
          kind: ReplicaSet
          metadata
             name: my-replicaset
             labels
                name: my-replicaset
          spec:
             replciase: 4
             selector:
                matchLables:
                    name: my-pod
             template:
                metadata:
                  name: my-pod
                  labels:
                    name: my-pod
                spec:
                   containers:
                      - name: my-pod
                        image: nginx
                    

    6. Deployments
        1. Replica set is to ensure all pods are in desired state, if any pod goes down then it will start and make it in running state
        2. Deployment is a super set of replciaset, that deployment provides following features 
            a. Update the application with different stratigies.
                1. Rolling update(Default)
                2. Recreate
            b. ensure pods are in desired state
        3. Rolling updates are useully done in a normal production env
            > This can lead to issue, if any upgrade failes
        4. Deployments provides a provision to update products/instance seamlessly
        5. Definition  file same as replicaset excpet kind
        5. Commands
            1. kubectl get all

    7. Namespaces
        1. 'Default' namespace is created when k8s started
            > for default
        2. 'kube-system' namespace is created when k8s started
            > for K8s system
        3. 'kube-public' namespace is created when k8s started
            > for public 
        4. to access resouce from another name space 
            ( <podname>.<namespce>.svc.cluster.local)
            <podname>.
            <namespce>.
            svc. - service or subdomain
            cluster.local --> domain
            apiVersion: v1
            kind: Namespace
            metadata:
                name: dev
        5. kuebctl create namespace dev
        6. kubectl config set-context $(kubectl config curret-context) --namespace=dev
        7. ResouceQuota kind is used to limit the resource usege on a namespace
            apiVersion: v1
            kind: ResourceQuota
            metadata:
                name: compute-quota
                namespace: dev
            spec:
                hard:
                    pods: "10"
                    requests.cpu: "4"
                    requests.memory: 5Gi
                    limits.cpu: "10"
                    limits.memory: 10Gi
        8. Policy and ResouceQuota is used to restic howmany pods can be created on each name space

    8. Services
        1. Enables communication b/w diff components or external
        2. Enalbles loose coupling b/w micro services
        3. Service is an object in K8s.
            1. Node Port service- Service listens on port and forward he request to the pods in the node
            2. Cluster servie IP Service
            3. Load balaner Servvice
        4. Node Port Service
            1. Listens on a node port and forward the request to the port running on the pod in cluster
            2. Defaut node port can be in range of 30008-32767
                apiVerion: v1
                kind: Service
                metadata:
                    name: myapp-service
                spec:
                    type: NodePort
                    ports:
                        - targetPort: 80
                        port: 80
                        nodePort: 30008
                    selector:
                        #Label from pod
                        app: myapp
                        type: front-end
            3. Services uses random algo to redirect the req to multiple replica pods
        5. Cluster IP Service
            1. IP address of Pods are not static
            2. different type(frontend, backend, database) of pods can communicate with each other seamlessly using Service IP
                apiVerion: v1
                kind: Service
                metadata:
                    name: back-end
                spec:
                    type: ClusterIP
                    ports:
                        - targetPort: 80
                        port: 80
                    selector:
                        #Label from pod
                        app: myapp
                        type: back-end        
        5. Loadbalancer Service
            1. Works with cloud native loadbalaners(ex: Google Cloud Platform)
                apiVerion: v1
                kind: Service
                metadata:
                    name: back-end
                spec:
                    type: LoadBalancer
                    ports:
                        - targetPort: 80
                        port: 80
                        nodePort: 30008
                    selector:
                        #Label from pod
                        app: myapp
                        type: back-end   
            
    9. Imparative vs declarative
        1. Imparative ( IaC)
            > Specifies 'what' and 'how' to do 
            > Specifies steps to config
            > Ansile , Cheff, Puppet 
            > K8s commands are imparative(run , create, edit, etc.)
            > K8s manifest files
        2. Declarative
            > Specifies What to do only
            > kubectl apply command is declarative
            > apply commands detects the changes in the input file and apply
    10. Kubectl apply
        1. compares the last applied configuration and input configuration to detect the changes and apply it on the system.
        2. https://kubernetes.io/docs/tasks/manage-kubernetes-objects/declarative-config/
        3. Local file, Last applied configuration, live k8s configuration        
            1. Local file - is the file contains the configuration details on the disk
            2. Last applied configuration- is the configuration detail applied last( Contained by k8s live configuatted as annontations)
            3. live k8s configuration - Live k8S configruation


03. Scheduler & Configuration
=============================
    1. How scheduler works?
        1. What to schedule?
        2. where to schedule?
        3. If schedulers are not running then pods will be in pending state, as it is not assiged to any node.
        
    2. Manual Scheduling 
        1. Node name can be assinged manually in pod spec
        '''
            apiVerion: v1
            kind: Pod
            metadata:
            name: myapp-pod
            labels:
                app: myapp
            spec:
                nodeName: node02
                containers:
                    - name: nginx-container
                    image: nginx
        '''
        2. To modify node, use Binding object to bind to a node
        '''
        apiVersion: v1
        kind: Binding
        metadata:
            name: nginx

    3. Labels and selectors
        1. Labels used to group objects
            ''' 
                apiVerion: v1
                kind: Pod
                metadata:
                name: myapp-pod
                labels:
                    app: webserver
                    function: frontend
                spec:
                nodeName: node02
                containers:
                    - name: nginx-container
                    image: nginx
            '''
            2. selector are used to filter the pods/objects based on the lables
                kubectl get pods --selector app=webserver  (or) kubectl get pods -l app=webserver --no-headers
                kubectl get pods --selector app=webserver,db=redis
                kubectl get pods --show-lables
            3. Annotations are used to specify any other details abuot the pod/company/etc
    4. Taints and Tolerent
        if there is at least one un-ignored taint with effect NoSchedule then Kubernetes will not schedule the pod onto that node
        if there is no un-ignored taint with effect NoSchedule but there is at least one un-ignored taint with effect PreferNoSchedule then Kubernetes will try to not schedule the pod onto the node
        if there is at least one un-ignored taint with effect NoExecute then the pod will be evicted from the node (if it is already running on the node), and will not be scheduled onto the node (if it is not yet running on the node).    
        1. Taints and tolerance are to restrict the pods in a node
        2. Taints
            1. No unwanted pods be placed on tainted node
            2. kubectl taint nodes node-name key=value:<taint-effect>
                <taint-effect> 
                    - NoSchedule - Pods will not be scheduled
                    - PreferNoSchedule - will try to no schedule a Pod, but not guranteed
                    - NoExecute - will not schedule any new pods and existing pods does not tolerate the taint will be evicted
                    ex: kubectl taint nodes node1 app=blue:NoSchedule
            3. NoExecute
        3. Tolerent
            1. Pods can be tolerated to be placed on a tainted node
                '''
                apiVerion: v1
                kind: Pod
                metadata:
                    name: myapp-pod
                    labels:
                        app: myapp
                spec:
                    containers:
                        - name: nginx-container
                        image: nginx
                    tolerations:
                        - key: "app"
                          operator: "Equal"
                          value: "blue"
                          effect: "NoSchedule"
                
                '''
                2. kubectl explain pod --recursive | less
            

        3. Node Affinity is aslo used to restrict pods on a node
        4. A taint is set on master node by default
            1. kubectl describe node kubemaster | grep 'Taint'
    5. Node Selctors
        1. Used to select the node based on the selector specification defined in the node lables
            '''        
            apiVerion: v1
            kind: Pod
            metadata:
                name: myapp-pod
                labels:
                    app: myapp
            spec:
                containers:
                - name: nginx-container
                    image: nginx
                nodeSelector:
                    size : Large
            '''
            2. kubectl label nodes <node-name> <label-key>=<label-value>
                kubectl label nodes node01 size=Large

    6. Node Affinity
        1. Ensure pods are hosted on a particular nodes
        2. Helps user to provide advanced complex conditions to select nodes
            '''        
            apiVerion: v1
            kind: Pod
            metadata:
                name: myapp-pod
                labels:
                    app: myapp
            spec:
                containers:
                    - name: nginx-container
                      image: nginx       

                affinity:
                    nodeAffinity:
                        requiredDuringSchedulingIgnoredDuringExecution:
                            nodeSelectorTerms:
                                - matchExpressions:
                                    - key: size
                                    operator: Exists
                                    - values: value
            '''
        3. Affinity type
            1. requiredDuringSchedulingIgnoreDuringExecution
            2. preferredDuringSchedulingIgnoreDuringExection
            3. requiredDuringSchedulingRequiredDuringExecution - Not released

    7. Taints and Tolerent vs NodeAffinity
        a. Taints and Tolerent Ensures that tainted nodes accecpts only tolerent pods, however the untained nodes also can accept the tolerent pods.This is not expected
        b. NodeAffinity ensures the expected pod to be placed on the right node, however other pods also can be placed on the labeled node.
        c. Combininig both will ensure right pods goes not right nodes

    8. Resource Requirements and Limits 
        a. Scheduler decides the where to place right pod
        b. Default resource assign by kubernets for each pod is 1 vCPU, 512Mi or default CPU request of 0.5 and memory of 256Mi"?
        c. CPU
            1. Spcifies the CPU of a pod
            2. Minimum vale can be 1m(Milli)
        d. Memory
            1. Specifies the Memory of a pod
            2. can be specified as Mebi byte
            3. G - Giga bytes. 1 Giga Byte = 1000 Mega Byte, 1 Meage byte = 1000 Kilo byte, 1 Kilo bytes = 1000bytes
            4. Gi - Gibi bytes :1 Gibi byte = 1024 Mibi Byte, 1 Mebi byte = 1024 Kibi byte, 1 Kibi byte = 1024 bytes
        e. commands
            kubectl create quota qtest --hard pods=3,cpu=100,memory=500Mi --namespace limited
            k set resources deploy nginx --requests cpu=100m,memory=5Mi --limits cpu=200m,memory=20Mi -n limited
    9. DaemonSets
        a. DaemonSets are like replicaset, but in each node one instance of pod will be runnig always.
        b. Daemonset does the following
            1. If a new nodes are added  then creates the pod
            2. If node is deleted then pod also will be deleted 
        c. Example: Logging, monitor agent
        d. kube-proxy, weave-net can be deplyed as DaemonSet
        e. definition is same as replicaset
        f. kubectl get daemonset
    
    10. Static Pods
        a. Kublet can manage a node independenly
        b. kubelet can be configured to read a pod specfication from a specific directory(ex: /ect/kubernetes/manifests)
        c. Kubelet monirots this diectory and create pods.This is called static pods
        d. If file is remvoed from the dir, then the pods will be remvoed.
        e. Only pods can be created and other kinds(Deployment, replicaset) needs Mater node
        f. kubelet.service(configpath file)/kubeconfig.yaml contains a feild "StaticPod" to specify path for static pods manifest files
        g. Static pods viewed using "docker ps" command
        h. static pods also listed in master kubectl ( can be readonly)
        i. static pods are used to create controlplace ( kubeadm tool uses static pods concepts)
        j. Static pods are deplyed with pod name with  node as suffix
    11. Multiple Schedulers
        a. Kubernetes can have multiple scheduler 
        b. CustomScheduler can be deployed in kubernetes master node
        c. Scheduler name will be different for each scheduler
        d. default-scheduler is the name for default
        e. leader-elect will ensure multiple master to select one scheduler
        f. schedulerName filed in pod spec will select the scheduler
        g. kubectl get events to vetify the scheduler events
    12. Configure kubernetes scheduler
        a. kube-scheduler.service
        b. kubescheduler.yaml



04. MultiContainer Pod
=========================
    a. At time we may need two services work togather
        - 
    b. Multi Container Pods
        - Created togather and distoryed togather
        - Shares Storage and networking space
    c. Design Patterns Implements MultiContainer Pods
        a. Sidecar pattern
            1. Collect Logs and forward them into a central log server
        b. Adapter Pattern
            1. Multiple application generates different format of logs
            2. Adapter container convertes the different format of logging into common format and send it to cenrtal log server
        c. Ambassador
            1. An application communicates with different type of database on different stages
            2. This logic should be written in a seperate container called Ambassador
             ex: App connects to  database for development, testing and production
            3. The application (main container) talks to local host and Ambassador container forward the request to different databases

05. Observability
===================
    a. Readiness Probe
    ------------------
        - Service will route the traffic to the Ready Pods
        - POD Status
            1. Pending state
            2. ContainerCreating
            3. Running
        - POD Conditions
            1. PodScheduled
            2. Pod Initialied
            3. ContainerReady
            4. Ready
        - Ready Condition
            1. Means the container is in ready state. But the actual application my take few seconds to become in ready
        - Readiness Probe In pods can be done in 3 different ways
            1. HTTP Test
            2. TCP Test
            3. Exec Command
        apiVersion: v1
        kind: Pod
        metadata:
           name: ready-pod
        readinessProbe:
           httpGet:
                path: api/ready
                port: 8080
        readinessProbe:
           tcpSocket:
                port: 8080
        readinessProbe:
           exec:
                command: 
                  - cat
                  - /app/is_ready
            initialDelaySeconds: 10s - Intial check
            periodSeconds: 5s - Frequency
            failureThreadhold: 8 - Used as retry times
    b. Liveness Probe
    --------------------
        - Docker creates the container and if appliaction goes down then contianer will also exit
        - Kubernets restarts the failed contianers
        - In Some cases if application goes unresponsive, kubernetes still continue to send reqwuest to the application and result in failure
        - Liveness Probes helps Kubernets to ensure the appliaction is healthy and is capable of receiving requests
        - 
        - Liveness Probe In pods can be done in 3 different ways
            1. HTTP Test
            2. TCP Test
            3. Exec Command
        apiVersion: v1
        kind: Pod
        metadata:
           name: ready-pod
        livenessProbe:
           httpGet:
                path: api/healthy
                port: 8080
        livenessProbe:
           tcpSocket:
                port: 8080
        livenessProbe:
           exec:
                command: 
                  - cat
                  - /app/is_ready
            initialDelaySeconds: 10s - Intial check
            periodSeconds: 5s - Frequency
            failureThreadhold: 8 - Used as retry times
    c. Container Logging
    -------------------

06. POD Design
=================
    1. Jobs
        a. Different type of workloads that contianer will run for loger period of time until users/admins stops it
            - webservers
            - databases 
        b. There are other type of workloads that runs for short period of time and exits
            - Batch processes
            - Analytics
            - Reporting
            - The result of each job can be image processing output, analytics

        c. Short running contianers deployed on Kubernetes will be restarts again and again untill a threadhold is reached
        Job YAML
        apiVersion: batch/v1
        kind: Job
        metadata:
           name: math-add-job
        spec:
            completions: 3
            parallelism: 3 - used to run the jobs in parallel
            template:
              spec:
                 containers:
                    - name: math-add-job
                      image: ubuntu
                      command: ['expr', '3', '+', '2']
                 restartPloicy: Never
        d. Commands:
            - k get pods
            - k delete job <job-name>
            - k get jobs 
        e. completions param
            - Used to run the pods at these many sucessful times, 
            - If any failure then that instance wont be considered
        f. parallelism
            - Used to run the pods in parallel
            - At a time it creates these many number of pods,  If any instance of a pod fails then it recreates until sucessful completion of a pod
        g. backofflimit
    2. Jobs            
        - Simillar to crontab in linux
        - Run a pod in a scheduled time as the specification mentioend in cron

        CronJob YAML
        apiVersion: batch/v1
        kind: CronJob
        metadata:
           name: reporting-cron-job
        spec:
            schedule: "*/1 * * * *"
            parallelism: 3 - used to run the jobs in parallel
            jobTemplate:
                spec:
                    completions: 3
                    parallelism: 3 - used to run the jobs in parallel
                    template:
                    spec:
                        containers:
                          - name: reporting-tool
                            image:  reporting-tool
                        restartPloicy: Never
        - Commands:
            - k create -f <yaml>
            - k get cronjobs

07. Networking
===============
    a. Services
    -------------
        - Services Enable communcation b/w application and users
        - NodePort:
            - Listen to a port on a Node and expsoe the application to outside world
            - TargetPort of a NodePort service is container/pod port
            - Port is service port
            - Node Port is portnumber of node
        - ClusterIP
            - 
        - LoadBalancer


08. State Persistance
======================
    1. Volumes
        - Persisit the data on storage volumes.
        - There different type of sotrage volumes
            - hostPath
                - Directory
                - File
            - MultiStorage
                - Azure
                - AWS
                - NFS, etc.
    2. Persistance Volumes
        - Cluster wide pool of storage volumes created by K8S
        - Pods can carveout and use it using PVC
        - Use hostPath or any other storage implemented
    3. PVC
        - Bound with persistane volume to use the storage
        - Use Labels and selector to select a pariticular volume
    
    4. StatefulSet
        - Simillar to deployments
        - Starts the pods in sequence 
        - Pod names will be pre-defined and derived from statefulset name
            ex: statefulset: mysql
                pods: mysql-0, mysql-1, mysql-2
        - scaling will happen in ordered way.
        - scaledown also will happen in ordered way. Last one(reverse order) taken down first and so on
        - Delete stateful set delete the pod in reverse order
        -
        yaml
        apiVersion: apps/v1
        kind: StatefulSet
        metadata:
        name: web
        spec:
            selector:
                matchLabels:
                app: nginx # has to match .spec.template.metadata.labels
            serviceName: "nginx"
            replicas: 3 # by default is 1
            template:
                metadata:
                labels:
                    app: nginx # has to match .spec.selector.matchLabels
                spec:
                terminationGracePeriodSeconds: 10
                containers:
                - name: nginx
                    image: k8s.gcr.io/nginx-slim:0.8
                    ports:
                    - containerPort: 80
                    name: web
                    volumeMounts:
                    - name: www
                    mountPath: /usr/share/nginx/html
            volumeClaimTemplates:
            - metadata:
                name: www
                spec:
                accessModes: [ "ReadWriteOnce" ]
                storageClassName: "my-storage-class"
                resources:
                    requests:
                    storage: 1Gi
        5. Headless Serivce
            - In Statfulset scenario, the master pod does write and other pod does read
            - How do we ensure this scenario in service.
            - Headeless service provides a way to rout traffic to only yo master pod
            - Headeless service expose each pod with DNS name
                pod-name.headelss-name.svc.cluster.local
            YAML
                apiVersion: v1
                kind: Service
                metadata:
                name: nginx
                labels:
                    app: nginx
                spec:
                ports:
                - port: 80
                    name: web
                clusterIP: None ----> Makes the service as headless service
                selector:
                    app: nginx
            - Pod definition will have 2 more secrions to connect with headless service
                - each pod sepc will have the following
                    > subdomain
                    > hostname
        6. Storage in statefulesets
            - Existing PV or PVC used in deployment/statfulset will lead to using the same volume by multiple pod.
            - If a scenario where each pod needs to use different PV and PVC, then volumeClaimTemplate on pod configuariton will ensure the same.
            - Each pod will create a different PVC and associate with different PV
            - If a pod goes down then the new pod will be assisiated with the exitin PVC


    
09. Updates by Sep2021
=========================
    1. Docker Images
    ------------------
        - Docker files are used to build the docker image
        - Docker image is built in a layerd architecture
        DockerFile
        ----------
        FROM Ubuntu
        RUN apt-get update
        RUN apt-get install python
        RUN pip install flask
        RUN pip install flask-mysql
        COPY . /opt/source-code
        ENTRY_POINT FLASK_APP=/opt/source-code/app.py flask run
        - docker build Dockerfile -t madhusgv/my-custom-app
        - docker push madhusgv/my-custom-app
        - FROM instruction should be an os or another docker image
        - Layered achitecture helps to resume from the failure step

    2. Authentication
    -------------------
    
    3. Role, Role bindings
    ----------------------

    4. RBAC
    -----------
        - kubectl auth can-i create pod
    5. Admission Controllers
    -------------------------
        - Validate the Pod manifest and restict creating pod is done by Admission controllers
        - AlwaysPullIages
        - DefaultStorageClass
        - EventRateLimit
        - NamespaceExists
        - NamespaceAutoProvision
        - etc.
        - Use kube-apisserver option --enable-admision-plugins, --disable-admission-plugins
        - ps -ef | grep kube-apiserver | grep admission-plugins
    6. Mutation Admision Controllers
    ---------------------------------
        - Validating admission controller
            a. Validates the objects and perform actions
                ex: Namespacelifecycle controller validate the namespace exists on the system and perform the actions
        - Mutating admission controller
            a. Check and modify/assign default values to the objects
                ex: assign default storageClassName to a PVC
        - Some admission controller can do both
            - Mutate -  First execution 
            - Validate -  Second execution 
        - Rejection in any admission controller will result in error to user
        - First Mutate and validate
        - Custom Admission Controllers
            1. Mutating Admission webhook            
            2. Vaildating Admission webhook
            3. How to deploy & configure admission webhook
                - Deploy custom webhook server(GO, python, etc.) as a deployment
                    - Receive json request validate and respond in a json format
                - Configuring admission controller
                    YAML
                    apiVersion: admissionregistration.k8s.io/v1
                    kind: VaidatingWebhookConfiguration
                    metadata:
                        name: "pod-policy-webhook.com"
                    
                    webhooks:
                        name: "pod-policy-webhook.com"
                        clientConfig:
                            service:
                            namespace: "example-namespace"
                            name: "example-service"
                            caBundle:""
                        rules: 


    7. API Versions
    -----------------
        1 v1 - GA stable version
        2 beta, v1beta1
            - Enabled by default
            - Has end to end testing
            - May have minor bugs
            - May be moved to GA later
        3 alpha, v1alpha1 
            - Not enabled,
            - Not realiable 
            - May have bugs
        4. Perferred version is the one default for each API
            - For example v1 is preferred version for Deploy
        5. Storage version is the one which is stored in the etcd
        6. The preferred and storage version are mostaly same, but they can be different
        7. Query the API to get the Preferred version
        8. Query the  Etcd to get the storage version
        9. Enable/Disable of API Groups
            - --runtime-config optin in api-server enables/disables api groups
            - --runtime-config=
            
    
    8. API Deprications
    --------------------
        1. Rules
            1. API elements may only be removed by incrementing the version of the API Group
                ex; if an API exits in v1alpha1, then an API can be removed in v1alpha2 only.v1alpha1 still contains the API
            2. API Objects must be able to roundtrip between API versions in a given release without information loss, with the exceptions of whole REST resources that do not exits in some versions.
            3. An API vesion in a given track may not be deprecated until a new API version atleast as stable is released
                - An alpha/beta vesions cannt deprecate GA version
            4.a Other than the most recent API versions in each track, older API versions must be supported after their announced deprecations of no loss than.
              Miminum Support
                GA: 12 Months or 3 Releases (whichever is longer)
                Beta: 9 Months or 3 Releases (whichever is longer)
                Alpha: 0 Releases
            4.b The Preferred API Version and storage API version for a given group may not advance untill after a release has been made that supports both new version and the previous version
        2. Kubectl convert command
            a. used to convert the objects/yaml from older yaml to new yaml
            b. convert command shuold be installed seperatly
            kubectl-convert -f ingress-old.yaml --output-version networking.k8s.io/v1
        3. kubectl proxy command 
    
    9. Customer Resource 
    --------------------
        a. Define a custome resource called CRD

        apiVersions: apiextenstions.k8s.io/v1
        kind: CustomResourceDefinition
        metadata:
            name: tickets.flights.com
        spec:
           scope: Namespaced
           group: flights.com
           names:
                kind: FlightTicket
                singular: flightTicket
                plural: flightTicket
                shotnames:
                    - ft
                versions:
                    - name: v1
                      served: true
                      storage: true
                schema:
                    OpenAPIV3Schema:
                        type: Object
                        properties:
                            from: 
                              type: string
                            to:
                                type: string
                                number:
                                    type: integer
                                    minium: 5
                                    maximum: 100
    10. Custom Controllers
    ---------------------
        a. A proceess watches the kubernetes objects continously and take action. This is called controllers.
        b. A process watches a custom resource called custom controllers
        c. gihub  repo named sample controller 
            1. clone and modify based on custom controller
            2. customaize the controller.go
            3. build using "go build -o sample-controller"
            4. run using kube-config file
                "./sample-controller --kubeconfig=$HOME/.kube/config
            5. package as docker image and run as pod
    11. Operators
    --------------
       - Combining custom resource definition and custom controller forms operators.
        1. ETCD Oprator
            CRD
            ----
                a. EtcdCluster
                b. EtcdBackup
                b. EtcdRestore
            Controller
            -----------
                a. ETCD Controller - Custom Contoller
            
        2. All the operator as present in OperatorHub.io
    
    12. Deployment Stratiges
    ------------------------
        1. Blue-Green
            a. Old version : Blue
            b. New version : Green
            c. All the traffics will be on old version until all the new versoins are up
            d. How works in K8s
                1. Blue - Create pod Label as "versoin:v1"
                2. The service exposes pod with v1 label
                3. Once the Green(v2) has come up then change the label to v2

        2. Canary
            a. Route only small amount of traffic to newer version of an application is called canary update
            b. Older and newer version with same label, but create newer version with less number of pods
            c. cannt rout minimum traffic of 1%
            c. estio 
    13. Helm Intro & Install
    --------------------------
        - Pacakge Manager
        - values.yaml takes the input to helm pacakge of an proudct
        Commands:
            - helm install wordpress
            - helm upgrade wordpress
            - helm rollback wordpress
            - helm uninstall wordpress

    14. Helm Concepts
    ---------------------
        - The configuration yaml files are updated with templating 
        - The user provided values are taken from values.yaml file
        - The Templates and values file forms helm charts
        - artifacthub.io
        - helm search
            - searchs the chats in the hub
            - helm search hub wordpress
        - Instead of seraching the charts in the hub we can add repository in local and sersrch
            - Add the local repo to search and download the chars
            - ex: bitnami
            - helm search repo wordpress
        - helm repo add bitnami https://charts.bitnami.com/bitnami
        - helm repo list
        - helm install <release-name> <chart-name>
        - helm list
        - helm unintall
        - helm pull --untar bitnami/wordpress
        - helm install bravo bitnami/drupal
        







    






    

