# Interview task given at Labrin
#### Given services and requirements for each: 
- MySQL
  - Data should be saved so we don't lose data in case of failure.
  - Should be accessible from other pods
- PostgreSQL
  - Data should be saved so we don't lose data in case of failure.
  - Should be accessible from other pods
- Adminer
  - Canary deployment
  - 5 replicas
  - User Preferences and Settings datas should be saved so we don't lose them in case of failure
  - Should be accessible in IP_ADDRESS/adminer
- Pgadmin4
  - Canary deployment
  - 5 replicas
  - User Preferences and Settings datas should be saved so we don't lose them in case of failure
  - Should be accessible in IP_ADDRESS/pgadmin
- Kubernetes Dashboard could be used in architecture
>Droplet Name: devops-interview-task<br>
>IP Address: 157.230.104.220<br>
>Username: root<br>
>Password: 2c4cb3bac8aea7bdb6a4e32e4f

#### Challanges faced with this task and how they were solved
- Adminer:
  - As there are 5 replicas of Adminer you should be able to share session and config files between them
  >Created disk on gcloud and deployed nfs server and its service. Made Adminer use it, so all replicas were  using the same disk to save their session and config files so they were shared between all replicas.
- MySQL, PostgreSQL:
  - Made it use the same disk and nfs server to save its data
- Ingress:
  - Couldn't make pgadmin use the path `/pgadmin/` with Ingress so assigned the path `/` to pgadmin and made path `/pgadmin/` to redirect to path `/`
- Pgadmin4:
  - Faced problem that when you type login credentials and press login most of the time, you don't login and get redirected back to login page. Even if you login you face different, <i>weird</i> problems.
  > Couldn't solve that issue. Probably session problem. Researched the internet, asked several friends but couldn't find solution or why it actually happened as I mounted all the session and config paths mentioned in documentation.

## How to use:

>I've used Google Cloud for this and I'm not 100% sure it will work with other Cloud services. But with a little tweaking with nfs-server, it should work.

Clone the repo<br>
`git clone git@github.com:TeymurPC/labrin_interview_task_k8s.git`

Create a disk that will be used by nfs-server first<br>
`gcloud compute disks create --size=10GB --zone=<zone name> gce-nfs-disk`

Deploy apps:

```
cd ./labrin_interview_task_k8s
kubectl apply -f nfs-server.yaml
kubectl apply -f adminer.yaml
kubectl apply -f mysql.yaml
kubectl apply -f pgadmin.yaml
kubectl apply -f postgres.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.26.1/deploy/static/mandatory.yaml
# loadBalancerIP: 157.230.104.220 should be added to the file below to have static IP. (Also you need to have the same static IP in Google Cloud)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.26.1/deploy/static/provider/cloud-generic.yaml
kubectl apply -f ingress.yaml
```