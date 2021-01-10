# openstack-helm-repo-mn
openstack-helm-repo-mn

~~~
mkdir {git,openstack-helm}
cd openstack-helm
git clone https://github.com/openstack/openstack-helm.git
git clone https://github.com/openstack/openstack-helm-infra.git


helm serve & 
cd  openstack-helm-infra
make helm-toolkit
make ingress
make {openvswitch,mariadb,libvirt,rabbitmq,memcached}
cd ../openstack-helm
make {keystone,horizon,glance,cinder,nova,placement,neutron,heat} 

 cd ~/git/
 git clone https://github.com/munnaeebd/openstack-helm-repo-mn.git

cp  ~/openstack-helm/openstack-helm/*.tgz ~/git/openstack-helm-repo-mn/
cp  ~/openstack-helm/openstack-helm-infra/*.tgz ~/git/openstack-helm-repo-mn/

cd ~/git/openstack-helm-repo-mn/

tar -zxf  ingress-0.2.1.tgz
do the same for all

rm -f *.tgz
git add .
git commit -m "Adding openstack helm chart to repo"
git config --global user.email "munnaeebd@gmail.com"
git config --global user.name "munnaeebd"
git add .
git commit -m "Adding openstack helm chart to repo"
git push


~~~

~~~
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
chmod 700 get_helm.sh
./get_helm.sh
helm init

kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
helm init --upgrade --service-account tiller
~~~

~~~
helm install --name nginx-ingress  --namespace kube-system stable/nginx-ingress --set controller.service.type=NodePort

kubectl -n kube-system edit deploy nginx-ingress-controller
...
spec:
      containers:
      - args:
        - /nginx-ingress-controller
        - --default-backend-service=kube-system/nginx-ingress-default-backend
        - --election-id=ingress-controller-leader
        - --ingress-class=nginx
        - --enable-ssl-passthrough # Add this flag
        - --configmap=kube-system/nginx-ingress-controller
...
~~~


~~~
root@master:~# cat ceph-admin-keyring.yaml
apiVersion: v1
kind: Secret
metadata:
  name: "pvc-ceph-client-key"
  namespace: openstack
type: kubernetes.io/rbd
data:
  key: QVFEazMvSmZNZzBCT3hBQTJCZVV5RmZyZTdTVXgzS1hZN01qb2c9PQ==
  
  ~~~
  
  ~~~
  root@master:~# cat cindar-configmp.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ceph-etc
  namespace: openstack
data:
  ceph.conf: |
    [global]
    mon_host = 192.168.101.166:6789,192.168.101.169:6789,192.168.101.165:6789
  ~~~
  
  ~~~
  root@master:~# cat rabbit-svc.yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/instance: rabbitmq
    manager: argocd-application-controller
  name: rabbitmq-nodeport-svc
  namespace: openstack
spec:
  ports:
  - name: http
    port: 15672
    protocol: TCP
    targetPort: 15672
  selector:
    application: rabbitmq
    component: server
    release_group: rabbitmq
  sessionAffinity: None
  type: NodePort

  ~~~



