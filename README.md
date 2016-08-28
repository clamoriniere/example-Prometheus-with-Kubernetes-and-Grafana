# Prometheus with Openshift and Grafana

aim of the repos it to show how we can use prometheus with openshift and grafana.
the following step will help you to create a openshift cluster with [oc cluster up](https://github.com/openshift/origin/blob/master/docs/cluster_up_down.md)
and deploy [prometheus.io](https://prometheus.io) in this cluster. Then deploy and configure
a [Grafana](https://grafana.net) dashboard connected to the prometheus service.

## Kubernetes

I used ```oc``` to setup a one node cluster. procedure to install the ```oc``` cli: [link](https://github.com/openshift/origin/blob/master/docs/cluster_up_down.md)) 


```https://github.com/openshift/origin/blob/master/docs/cluster_up_down.md
$ oc cluster up
```

```
$ oc get nodes
```

You are logged in as:

```
User: developer
Password: developer
```

To login as administrator:
```
oc login -u system:admin
```



## Install Prometheus from coreos tutorial

link: (Coreos tutorial)[https://coreos.com/blog/prometheus-and-kubernetes-up-and-running.html]

```
oc create -f prometheus-serviceaccount.yaml
oc policy add-role-to-user system admin -n myproject
oc adm policy add-cluster-role-to-user cluster-reader system:serviceaccount:myproject:prometheus
```


```
$ oc create -f prometheus-configmap-1.yaml
configmap "prometheus" created
```

```
$ oc create -f prometheus-deployment.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:30900) to serve traffic.

See http://releases.k8s.io/release-1.2/docs/user-guide/services-firewalls.md for more details.
service "prometheus" created
deployment "prometheus" created
```

expose the service:
```
oc expose service prometheus --hostname=prometheus.localhost
```

```
$ oc create -f node-exporter.yaml 
daemonset "node-exporter" created
```

```
open http://prometheus.localhost
```

```
$ oc replace -f prometheus-configmap-2.yaml
```

```
$ curl -XPOST http://prometheus.localhost/-/reload
```

## Install grafana

```
$ oc create -f Grafana-service.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:30603) to serve traffic.

See http://releases.k8s.io/release-1.2/docs/user-guide/services-firewalls.md for more details.
service "prometheus" created
```

```
$ oc create -f Grafana-deployment.yaml
deployment "grafana" created
```

```
oc expose service grafana --hostname=grafana.localhost
```

```
open http://grafana.localhost
```

- Connect to grafana: user/pass= admin/adim
- Add dashboad to grafana:
    * add prometheus data source: "name=prometheus", type="prometheus", "host=http://prometheus.localhost"
    * add the dashboard from the ./dashboard folder

dashboards come from [https://github.com/jimmidyson/prometheus-grafana-dashboards](https://github.com/jimmidyson/prometheus-grafana-dashboards)
and adapted to work with the latest prometheus version.

Your are done :)