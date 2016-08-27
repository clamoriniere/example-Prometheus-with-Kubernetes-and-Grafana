# Prometheus with Kubernetes and Grafana

aim of the repos it to show how we can use prometheus with kubernetes and grafana.
the following step will help you to create a kubernetes cluster with [minikube](https://github.com/kubernetes/minikube)
and deploy [prometheus.io](https://prometheus.io) in this cluster. Then deploy and configure
a [Grafana](https://grafana.net) dashboard connected to the prometheus service.

## Kubernetes

I used minikube to setup a one node cluster

````
$ minikube start
````

```
$ minikube get nodes
````

## Install Prometheus from coreos tutorial

link: (Coreos tutorial)[https://coreos.com/blog/prometheus-and-kubernetes-up-and-running.html]

```
$ kubectl create -f prometheus-configmap-1.yaml
configmap "prometheus" created
```

```
$ kubectl create -f prometheus-deployment.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:30900) to serve traffic.

See http://releases.k8s.io/release-1.2/docs/user-guide/services-firewalls.md for more details.
service "prometheus" created
deployment "prometheus" created
```

```
$ kubectl replace -f prometheus-configmap-2.yaml
```

```
$ curl -XPOST http://172.17.4.201:30900/-/reload
```

```
$ kubectl create -f node-exporter.yaml 
daemonset "node-exporter" created
```

```
open http://$(minikube ip):30900
```

## Install grafana

```
$ kubectl create -f Grafana-service.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:30603) to serve traffic.

See http://releases.k8s.io/release-1.2/docs/user-guide/services-firewalls.md for more details.
service "prometheus" created
```

```
$ kubectl create -f Grafana-deployment.yaml
deployment "grafana" created
```

```
open http://$(minikube ip):30603
```

- Connect to grafana: user/pass= dmin/adim
- Add dashboad to grafana:
    * add prometheus data source: "name=prometheus", type="prometheus", "host=http://$(minikube ip):30900"
    * add the dashboard from the ./dashboard folder

dashboards come from [https://github.com/jimmidyson/prometheus-grafana-dashboards](https://github.com/jimmidyson/prometheus-grafana-dashboards)
and adapted to work with the latest prometheus version.

Your are done :)