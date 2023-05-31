# Helm for beginners 

> Helm is known as the package manager for Kubernetes, focused on automating the Kubernetes applications lifecycle in a simple and consistent way.

## Basic commands

- #### search for `influxdb` in `artifacthub.io`
```
$ helm search hub influxdb
URL                                               	CHART VERSION	APP VERSION	DESCRIPTION                                       
https://artifacthub.io/packages/helm/bitnami-ak...	5.4.10       	2.5.0      	InfluxDB(TM) is an open source time-series data...
https://artifacthub.io/packages/helm/bitnami/in...	5.4.17       	2.6.1      	InfluxDB(TM) is an open source time-series data...
https://artifacthub.io/packages/helm/riftbit/in...	2.3.10       	2.0.8      	InfluxDB&trade; is an open source time-series d...
https://artifacthub.io/packages/helm/cloudnativ...	1.1.6        	1.7.3      	Scalable datastore for metrics, events, and rea...
https://artifacthub.io/packages/helm/kubegemsap...	2.2.11       	2.0.7      	InfluxDB(TM) is an open source time-series data...
https://artifacthub.io/packages/helm/romanow-he...	1.3.5        	1.8.4      	The Time Series Data Platform where developers ...
https://artifacthub.io/packages/helm/my-persona...	0.1.0        	2.0.0      	A Helm chart for influxdb2                        
https://artifacthub.io/packages/helm/influxdata...	4.12.1       	1.8.10     	Scalable datastore for metrics, events, and rea...
https://artifacthub.io/packages/helm/lsst-sqre/...	3.1.1        	1.8.10     	Scalable datastore for metrics, events, and rea...
https://artifacthub.io/packages/helm/sectionme/...	0.0.2        	v0.10.0    	Basic chart to deploy the influxdb_exporter to ...
https://artifacthub.io/packages/helm/inspur/inf...	0.0.2        	0.6.0      	An InfluxDB proxy for achieving a highly availa...
https://artifacthub.io/packages/helm/influxdata...	0.1.22       	1.10.0     	Run InfluxDB Enterprise on Kubernetes            
...
```

- #### add a repository
```
$ helm repo add influxdata https://helm.influxdata.com/
```

- #### list all available repositories
```
$ helm repo list
NAME                           	URL                                                               
nfs-subdir-external-provisioner	https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
gitlab                         	https://charts.gitlab.io                                          
influxdata                     	https://helm.influxdata.com/
```

- #### search `influxdb` in the available repositories
```
helm search repo influxdb
NAME                          	CHART VERSION	APP VERSION	DESCRIPTION                                       
influxdata/influxdb           	4.12.1       	1.8.10     	Scalable datastore for metrics, events, and rea...
influxdata/influxdb-enterprise	0.1.22       	1.10.0     	Run InfluxDB Enterprise on Kubernetes             
influxdata/influxdb2          	2.1.1        	2.3.0      	A Helm chart for InfluxDB v2                      
influxdata/kapacitor          	1.4.6        	1.6.4      	InfluxDB's native data processing engine. It ca...
influxdata/chronograf         	1.2.5        	1.9.4      	Open-source web application written in Go and R...
influxdata/telegraf           	1.8.26       	1.25.2     	Telegraf is an agent written in Go for collecti...
influxdata/telegraf-operator  	1.3.11       	v1.3.10    	A Helm chart for Kubernetes to deploy telegraf-...
```

- #### pull the repo for editing later
```
$ helm pull --untar influxdata/influxdb
$ ls influxdb/
Chart.yaml  OWNERS  README.md  ci  files   templates   values.yaml
```

- #### install the custom variable
```
$ vim value-custom.yaml # <-- edit variables
$ helm install monitoring-release -f ./influxdb/values-custom.yaml ./influxdb --namespace monitoring
```