# K3S
### Single Node
#### instalation
1. disable traefik
   ``curl -sfL https://get.k3s.io | sh -s - --disable=traefik``
2. create directory k3s
   ``mkdir .kube1``
4. copy k3s.yml to directory k3s
   ``sudo cp /etc/rancher/k3s/k3s.yaml .kube/config.yml``
6. add permission
   ``sudo chown $USER:$GROUP .kube/config.yml``
8. export
   ``export KUBECONFIG=~/.kube/config.yml``

### High Avaliability 
#### Preparation
1. Reqruitment
   a. 1 server load-balancer
   b. 1 DB-external(mysql)
   c. 2 or more server-nodes(masters)
   d. 2 or more server-workers
2. setup server load-balancer
   a. create an nginx load-balancer to handle trafic of server nodes
   b. make sure that every nodes(load-balancer, server nodes, worker nodes) are connected
3. setup node-master
   a. install node-master
   ``curl -sfL https://get.k3s.io | sh -s - server \
--token=YOUR-SECRET \
--datastore-endpoint='mysql://user:pass@tcp(ipaddress:3306)/dbname' \
--node-taint CriticalAddonsOnly=true:NoExecute \
--tls-san your-lb-ip-address``
   b. cek the status of server nodes(Master)
   ``sudo kubectl get nodes``
   c.ssh to one of server-node to get token that will be used for worker-nodes(agents)
   ``sudo cat /var/lib/rancher/k3s/server/node-token``
   d.you can copy context of k3s server to be used for local computer
   ``sudo cat /etc/rancher/k3s/k3s.yaml``
4. setup node-worker
   a. configure worker
   ``curl -sfL https://get.k3s.io | sh -s - agent \
--server https://your-lb-ip-address:6443 \
--token YOUR-SECRET``
