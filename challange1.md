
## Challange Lab 1 : 
#### Create and Manage Cloud Resources: Challenge Lab
https://www.cloudskillsboost.google/focuses/10258?parent=catalog


> **Task 1 : Create a project jumphost instance**
In this task we just need to create a VM instance of the details mentioned in the Lab Page.
To create the VM you can use the GCP Web UI, but I will be using the CLI.
1. Open the Google Cloud Shell
2. Use command : `gcloud compute instances create <your-instance-name> --zone=<your-zone-from-the-lab-page> --machine-type=f1-micro --image-family=debian-11 --image-project=debian-cloud`
3. Check the Progress

> **Task 2 : Create a Kubernetes service cluster**
Here we need to create the K8s CLuster with the details specified in on the Lab Page.
The CLI way of doing it is:
1. Open the Google Cloud Shell
2. Use Command to create the cluster : `gcloud container clusters create project-cluster --zone <your-zone-from-the-lab-page>`
3. Use command to activate k8s cluster in gcloud shell : `gcloud container clusters get-credentials project-cluster --zone <your-zone-from-the-lab-page>`
4. Use the command to create the deployment : `kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:2.0`
5. Expose the Deployment : `kubectl expose deployment hello-server --type=LoadBalancer --port <port-from-lab-page>`
6. Check the Progress

> **Task 3: Set up an HTTP load balancer**
In this we will setup the Google HTTP LoadBalancer service to expose our app to the outside world.
1. Create the startup script by running this command
```
cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
```
2. Create an instance template
```
gcloud compute instance-templates create web-server-template \
          --metadata-from-file startup-script=startup.sh \
          --network nucleus-vpc \
          --machine-type f1-micro \
          --region <your-region-from-lab>
```
3. Create 2 instance : 
```
gcloud compute instance-groups managed create web-server-group \
          --base-instance-name web-server \
          --size 2 \
          --template web-server-template \
          --region <your-region-from-lab>
```
4. Create firewall rule : 
```
gcloud compute firewall-rules create <name-of-firewall-rule-from-lab-page> \
          --allow tcp:80 \
          --network nucleus-vpc
```
5. Create a health check : `gcloud compute http-health-checks create http-basic-check`
6. Configure managed group:
```
gcloud compute instance-groups managed \
          set-named-ports web-server-group \
          --named-ports http:80 \
          --region <your-region-from-lab>
```
7. Create a backend service : 
```
gcloud compute backend-services create web-server-backend \
          --protocol HTTP \
          --http-health-checks http-basic-check \
          --global
```
8. Adding instance group to backend service
```
gcloud compute backend-services add-backend web-server-backend \
          --instance-group web-server-group \
          --instance-group-region <your-region-from-lab> \
          --global
```
9. Create an URL-Map : 
```
gcloud compute url-maps create web-server-map \
          --default-service web-server-backend
```
10. Create an HTTP proxy to route to URL Map: 
```
gcloud compute target-http-proxies create http-lb-proxy \
          --url-map web-server-map
```
11. Create a forwarding rule:
```
gcloud compute forwarding-rules create http-content-rule \
        --global \
        --target-http-proxy http-lb-proxy \
        --ports 80
```
12. Check the Progress

> P.S : If the progress is not updated ssh into the one of the webserver and check if nginx is running `sudo systemctl status nginx`


