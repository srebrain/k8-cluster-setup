### k8-cluster-setup
Setup a Highly Available and Scalable Kubernetes cluster on GKE

### Set-Project-and-compute/region-for-the-cluster-and-update-the-components

gcloud config set project kube-practice-219218
gcloud config set compute/region us-central1
gcloud components update

### Setup a regional cluster spread across 3 Zones and each zone running 2 nodes for HA

gcloud container clusters create my-regional-cluster \
--num-nodes 2 \
--region us-central1 \
--disk-size=15GB \
--disk-type=pd-standard \
--enable-autoscaling --min-nodes 1 --max-nodes 3 \
--enable-autorepair 

### Create a deployment 

kubectl create -f deployment.yaml

### Setup horizontal POD autoscaler and Point it to our deployment 

kubectl create -f HorizontalPodAutoscaler.yaml

### Expose the deployment(k8-demo) via a Service of type Load balancer for external connectivity

kubectl expose deployment nginx --port=80 --target-port=8080 --type=LoadBalancer

### Spun up a new POD (it's not managed by our deployment (k8-demo) to generate load 

kubectl run -it --rm --restart=Never loadgenerator --image=busybox -- sh -c "while true; do wget -O - -q http://Service_External_IP; done"

### Enable Cluster Autoscaler for our "My-Regional-Cluster"

gcloud container clusters update demo-regional-cluster  --enable-autoscaling --min-nodes=3 --max-nodes=30 --region us-central1
