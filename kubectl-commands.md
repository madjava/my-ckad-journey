kubectl commands
===

## Table of contents

* [Creating a resource](#creating-a-resource)
* [Namespace](#namespace)
* [Pods](#pods)
* [Deployment](#deployment)
* [Service](#service)
* [Nodes](#nodes)
* [Monitoring](#monitoring)
* [Rollout and Versioning](#rollout-and-versioning)
* [Jobs and CronJobs](#jobs-and-cronjobs)
* [ConfigMap](#configmap)
* [Service Account](#service-account)
* [Network Policy](#network-policy)
* [Storage](#storage)
* [Volumes](#volumes)
* [Resource Quota](#resource-quota)
* [Temporary containers](#temporary-containers)


I'd be assuming my favourite alias is already set

```bash
alias k=kubectl
```

> In some cases you would need to update the yaml manifest definition further so for some example below you will see the `--dry-run=client -o yaml` added. It's not entirely required, some tasks you can run immediately without needing a yaml file to update. Read the question carefully and decide what's the quickest best route.

### Creating a resource
- `k apply -f manifest-file.yaml`<br>
  Creates the resource defined in the manifest file

- `k apply -n <name-space> -f ingress.yaml`<br>
  Creates the resource defined in the manifest file but in the `name-space` namespace.

### Namespace

- `k get ns --no-headers | wc -l`<br>
   If you ever need to know how many namespaces there currently are

- `k create ns dev`<br>
   Quickly create a namespace called `dev`.  

- `k config set-context $(k config current-context) --namespace=dev`<br>
   Quickly switch to `dev` namespace. Actually didn't have to do this during my exam but had it committed to memory just incase. I always used the `-n` flag where namespace was involved.

### Pods

- `k get po --no-headers | wc -l`<br>
   If you need to know the total number of pods. You can add the `-n` flag if looking at a particular namespace

- `k get po -A`<br>
   Gets all Pods in all namespaces. Very handy if you need to find a faulty pod<br>

   `k get po -n <name-space>`<br>
   Gets all Pods in a particular namespace. By looking at the `READY` column or `STATUS` you can tell if something is not right.

   `k get po -o wide`<br>
   As above but with more details about the pods e.g. it's `IP`, `NODE` also stats like `STATUS` and `READY` info can tell you what node the pod resides, its ip etc.

- `k run mypod --image=nginx --namespace=dev --dry-run=client -o yaml`<br>
   or<br>
  `k run mypod --image=nginx -n=dev --dry-run=client -o yaml`<br>
   Create a namespace specific manifest file for a pod

- `k explain pod --recursive | grep envFrom -A3`<br>
  Quickly get manifest information about the `envFrom` property for a Pod spec.<br>

  **Note:** you can use this for any other K8s object. e.g<br>
  `k explain cronjob --recursive | grep spec -A5`.<br>
  To view more lines add a higher number to the `-A` flag<br>

- `k -n <ns> exec -it <pod-name> -- cat /log/app.log`<br>
  View the contents of the file `/log/app.log` of the pod `pod-name` in the `ns` namespace.<br>

- `k -n <ns> logs <pod-name>`<br>
  View the logs of `pod-name`

- `k -n <ns> logs <pod-name> -c nginx-container`<br>
  View the logs of `pod-name` for the container called `nginx-container`. Handy when you have a multi-container pod.<br>

  If you have a multi-container pod and can't remember the exact container name just run the first variation. K8s would complain and list the names of the containers that exists in the pod and ask you to pick one.<br>

  I've found this a faster way than looking at the manifest file or running the `describe` command though there are other quick ways as well such as using `describe` in combination with `grep`. This was good enough for me.

- `k get po -l env=prod,tier=frontend`<br>
   A pod selector based on multiple labels

- `k get po -l 'team in (dev, prod)' --show-label`<br>
   Similar to above, but in this case, it lists pods that have a label called `team` with `dev` and `prod` as values and shows the labels as well in the output  

- `k get po --show-labels`<br>
  Lists pods with their labels. Can be used with the `-A` combination when doing a larger search and or in combination with the namespace option `-n <ns>` to limit results.

- `k label po <pod-name> env-`<br>
  Removes a label called `env` on the pod called `pod-name`

- `k get all -n <name-space> -l key=value`<br>
   Get all resources in a particular namespace limited by the label<br>

   `k get all -n <name-space>`<br>
   Dumps all resource detail to your terminal.<br>
   You can add the `--no-header` flag if you want to do some counting by piping to `grep`.

- `k run backend --image=nginx --serviceaccount=<sa-name>`<br>
   When you need to create a pod with a giving service account name. You can combine this with the `--dry-run=client -o yaml` to generate a manifest file to further update if required. You at are sure this bit is covered.

- `k run app --image=busybox --dry-run=client -o yaml  -- /bin/sh -c 'while true; do echo "$(date) | $(du -sh ~)" >> /var/logs/diskusage.log; sleep 5; done;'`<br>
  Super quick command to create a manifest file set with default command to run. Depending on the requirement you may not even need the `--dry-run=client -o yaml` and can create to container straight away, understanding the expectation helps.

  Also if you need to pass this definition to a file for further updating which might likely be the case then the `> manifest-file.yaml` comes at the end.<br> e.g.<br>
  `k run app --image=busybox --dry-run=client -o yaml  -- /bin/sh -c 'while true; do echo "$(date) | $(du -sh ~)" >> /var/logs/somefile.txt; sleep 5; done;' > manifest-file.yaml`<br>

  Don't add it after the `--dry-run=client -o yaml` as your fingers (brain) may be used to already.

- `k exec mypod -c <pod-container> -it -- /bin/sh`<br>
  Log into a particular container called `pod-container` on the `mypod` pod

- `k exec <pod-name> -it -- env`<br>
   Quickly list the pods environment variables. Fast way to check the existence of a value or verify that your config mapping or env variables have been set correctly.

- `k run mypod --image=ngnix --labels=env=prod,team=dev --dry-run=client -o yaml`<br>
   Creates a pod manifest file with two labels `env` and `team` set.

- `k set image po/mypod pod-container=nginx:latest`<br>
   Quickly update a pods image.

- `k annotate po.mypod contact='Felix',commit='34df3245' --overwrite=true`<br>
  Annotates a pod called `mypod` with the provided values and overwrite if already exists<br>
  **Notice** how you can use the `po/mypod`, `po.mypod` or `po mypod` variation from the examples above

- `k run nginx --image=nginx --restart=Never --env=var1=val1`<br>
  Creates a pod with environment variable set. Saves you having to edit a yaml file or wad through documentation. Depending on the task this may event be sufficient.

- `k run nginx arg1 arg2 --image=nginx --restart=Never --labels=app=v1 --dry-run=client -o yaml`<br>
  Creates a pod with two arguments and labels. Timesaver and you can output to a definition file and edit further.

- `k run nginx --image=nginx --restart=Never --requests='cpu=100m,memory=256Mi' --limits='cpu=200m,memory=512Mi'`<br>
 Creates a pod with resource requirement already set. Let wading around through documentation. You dan output using `--dry-run=client -o yaml` to edit further if required.

- `k delete po/<my-pod> --grace-period=0 --force`<br>
   If you need to delete a pod always add the `--grace-period=0` flag else you'd have to wait a few precious seconds for the default wait time. There are a few aliases to this out there i had seen but i was comfortable typing this all out.<br>This actually came in handy for me during the exam as i had created a pod in the wrong namespace, when i realised i quickly removed it with this command and recreated the pod in the expected namespace.

### Deployment

- `k run myapp --image=nginx --port=80 --expose`<br>
  Creates a service and a pod called `myapp` on port 80

- `k create deploy <deployment-name> --image=nginx --replicas=3 --dry-run=client -o yaml`<br>
  Quickly create a deployment called `mydeploy` with a pod running the `nginx` image and 3 replicas. You may need to update the manifest file further or this could suffice depending on the task at hand.

- `k get deploy <deployment-name> -o yaml`<br>
 Gets the deployment details in yaml format. You can redirect to a file if need be if you want to edit the deployment. **Note** Getting K8s object details is now really verbose due to additions of a `managedFields` property, output would not be as neat as you may see in some tutorials.

- `k edit deploy/<deployment-name>`<br>
 Quick way to edit an existing deployment straight from the terminal. Once you save and exit the editor in the terminal K8s would apply your changes.

- `k describe deploy <deployment-name> | grep -i containers -A2`<br>
A quick way to list out a deployments containers. The `-i` flag for the `grep` command is for case insensitivity and `-A2` says give me just two lines, which should include the container, it's name and it's image.

- `k set image deploy/<deployment-name> nginx-container=nginx:latest`<br>
 Quickly update a deployments image on the fly for the container called `nginx-container`.

- `k expose deploy mydeploy --name=<service-name> --target-port=8080 --type=NodePort --port=8080 --dry-run=client -o yaml`<br>
 Exposes a deployment called `mydeploy` by creating a service called `service-name` of type `NodePort`. Can be outputted to a file or run straight in the terminal depending on the task at hand. Omit the `--dry-run=client -o yaml` section if you want to run immediately.

- `k expose deploy -n <name-space> mydeploy --type=NodePort --port=80 --name=service-name --dry-run -o yaml > manifest-file.yaml`<br>
Same as above but namespace specific. Note the `--dry-run -o yaml > manifest-file.yaml` is optional depending on the task at hand.

- `k scale deploy/<mydeploy> --replicas=5`<br>
  Quick command to add more replicas for a deployment called `mydeploy`. No need to update the deployment manifest file then apply it if you are faced with this sort of task.

- `k autoscale deployment foo --min=2 --max=10`<br>
   Quickly create an autoscale for a deployment.<br>
   Have a look at this command for options `k autoscale -h`. It might just save you some time.

### Service

- `k create svc nodeport my-service --tcp=8080:80 --dry-run=client -o yaml`<br>
   Creates a service of type NodePort with `port` set to `8080` and `targetPort` set to `80`.<br>
   **Tip:** Run `k create svc -h` for some help if unsure

- `k create svc clusterip redis --tcp=6379:6379 --dry-run=client -o yaml`<br>
   Same as above but for a ClusterIP type service. When omitted this is the default service type.

- `k expose po <pod-name> --port=6379 --name <service-name> --dry-run=client -o yaml`<br>
Creates a service called `service-name` and exposes the pod called `pod-name` on the specified port. This particular command would set both the service `port` and `targetPort` to `6379`. Might be what the task requires, if not you can quickly run this command `k expose -h` for a plethora of examples to pick from.

- `k expose po <pod-name> --port=6379 --target-port=6379 --name=service-name`<br>
Same as above but this time we want to specify a port and a targetPort which is the port the pods are listening on.

- `k run mypod --image=nginx --port=80 --expose --dry-run=client -o yaml`<br>
 Quickly create a service and pod exposed by the service. Nice if the pod does not already exist and you are asked to create a pod and then create a service to expose the pod. This one-liner would be massive time saver in such a situation.

- `k expose deploy mydeploy --port=80 --target-port=8000  --dry-run=client -o yaml`<br>
Quick command which creates a service for the `mydeploy` deployment

### Nodes

- `k taint nodes <node-name> key=value:NoSchedule`<br>
   Quickly taint a node with the `NoSchedule` option. No need to wad through documentation to figure this out.

- `k describe node <node-name> | grep -i taint`<br>
   Quickly find the taints that exists on a node called `node-name`.

- `k taint node <node-name> <taint-name>-`
   Quickly removes a taint called `taine-name` on the node called `node-name`.

- `k label nodes <node-name> <label-key>=<label-value>`
   Quickly add a label to a node called `node-name`.

- `k get nodes <node-name> --show-labels`<br>
   Lists the node called `node-name` with its labels. Omitting the node name would list all nodes labels in the current namespace.

### Monitoring

- `k top node`<br>
  If you need to findout resource consumption for all nodes. e.g. to determine from the list of node which one is using memory or cpu, the colums section from this output contains the info you would need.

- `k top node <node-name>`<br>
   Same as above but specific to the specified node.

- `k top pod`<br>
  Similar to above but for examining pod resource usage. This is useful if you have a list of running pod and want to see which is using the most resource. It can be combined with the `-n` option to be more namespace specific.

- `k top pod <pod-name>`<br>
  Same as above but specific to the specified pod.


### Rollout and Versioning

- `k rollout undo deploy <deployment-name>`<br>
   Quickly undo the most recent change to the deployment called `deployment-name`.

- `k rollout history deploy <deploy-name>`<br>
   List current deployment revisions for the `deploy-name` deployment.

- `k rollout undo deploy/<deployment-name> --to-revision=<revision-number>`<br>
  Quickly undo a recent deployment change to a specific previous deployment revision

- `k rollout status deploy <deploy-name>`<br>
  Gets you the current rollout status info for the deployment called `deploy-name`.

- `k rollout history deploy <deploy-name>`<br>
  Gets you the rollout history for the deployment.

- `k set image deploy <deploy-name> <container-name>=<container-image>`<br>
  Updates a deployments image by setting `container-name` image another image or version

### Jobs and CronJobs
- `k create cronjob my-cron-job  --schedule="*/1 * * * *" --image=busybox --dry-run=client -o yaml`<br>
  Quickly create a cronjob that is defined to run every minute using the busybox image`

- `k create cronjob my-cron-job --image=busybox --schedule="*/1 * * * *" --dry-run=client -o yaml -- /bin/sh -c 'echo "Current date: $(date)"'`<br>
Same as above but in this example, we specify the command for the container as part of options in the terminal. If the required command based on the exam question is simple enough you can execute this straight away without the `--dry-run=client -o yaml` option as in this example.

- `k create job my-job --image=busybox --dry-run=client -o yaml`<br>
  Quickly create a job definition using the busybox image

**Note** Have a play with these commands for some insightful info
- `create cronjob -h` and <br>
- `create job -h`


### ConfigMap
- `k create cm <cm-name> --from-literal=key=value`<br>
  Quickly creates a configmap called `cm-name` with values `key=value`
  No need to waste time looking through the documentation, this is your go-to and would save you time 🤓.

  **Note:** You can add many `--from-literal=key=value` just make sure there's a space between them

- `k create cm <cm-name> -n <name-space>`<br>
  Same as above but in the namespace specified

- `k create configmap db-config --from-env-file=config.txt`<br>
  If creating from a file.

### Service Account
- `k create sa <sa-name> -n <name-space>`<br>
  Quickly creates a service account called `sa-name` in the namespace called `name-space`.

### Network Policy
- `k describe netpol <policy-name>`<br>
Get quick info about the networkpolicy. Comes in handy when troubleshooting connectivity issues with pods.

### Storage
- `k get sc`<br>
   Display available storage information

### Volumes
- `k get pv`<br>
  Displays available persistentvolumes

- `k get pvc`<br>
  Displays available persistentvolume claims available.

**Note:** `ps`'s and `pvc`'s don't have imperative commands that can be used to quickly create them at this time. This is one of those places where you need to be armed with your K8s reference documentation and make sure you have relevant sections bookmarked for quick access. Another area without direct imperative options at the moment is around ingress and egress, you'd have to use the documentation.

🧨 Make sure when using the documentation examples your `storageClassName` names match. If asked to specify a particular storage class name in your `pv` manifest file then make sure that matches the definition in the `pvc` also. This fact can get lost or omitted while copy-pasting from one place to another.  

### Resource Quota
- `k create quota myrq --hard=cpu=1,memory=1G,pods=2 --dry-run=client -o yaml`<br>
   Quickly creates a ResourceQuota called `myrq` with the provided resource requirements.
   You can run `k create quota -h` for more options when faced with this kind of question or if documentation does not cut it for you.


### Temporary containers

- `k run tmp --image=busybox --restart=Never -it --rm -- wget -O- 10.109.67.191:80`<br>
   You can quickly create temporary containers as in this example command that creates a pod using the busybox image then `wget` data from another pod in the cluster. The `-it` gives you a shell the `--rm` removes the container once you exit the shell. This is usually useful when you want to get data from pods that are part of a ClusterIP service or test if pods are running.<br>
   Keep this knowledge in your tool set, you may need it.


Play with these commands over and over again and use them in your practice tests when the need presents itself, they would become second nature very quickly. I found that once you've covered the K8s CKAD exam [Domains & Competencies](https://training.linuxfoundation.org/certification/certified-kubernetes-application-developer-ckad/) scope, there would be no strange surprises in the questions, you'd just need to learn to move fast, manage your time and quickly get the info you need for the task at hand.

I hope these come in handy and most importantly come back to memory if/when you need them during your exam.

You can continue reading up on some [CKAD exam preparation tips](README.md) and resources that i found helpful [here](README.md).

---
Wishing you success in your exams 🙏🏽
