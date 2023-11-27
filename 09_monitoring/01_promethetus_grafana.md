# Monitoring

## What is Prometheus?
Prometheus is an open-source DevOps tool. It provides monitoring and real-time alerting functionality for container orchestration platforms like Kubernetes. It collects and stores the metrics from the platform as time series data. It has an out-of-box capability for monitoring the container orchestration platform. It acts as a data source for other data visualization libraries like Grafana.

## What is Grafana?
Grafana is a multi-platform, open-source online application for analytics and interactive visualization. When you connect it to supported data sources like Prometheus, it offers:

## Prometheus architecture 
![](https://semaphoreci.com/wp-content/uploads/2023/09/prometheus-architecture.png)

- Prometheus Server is the core component in the Prometheus architecture. It is where the actual monitoring job occurs.
- Alertmanager alerts users via email and other communication channels, such as Slack.
- Pushgateway can support temporary jobs. It allows users to push time series data to the Prometheus targets. It also handles metrics for short-lived jobs.  

The prometheus server can further be split into three components:
- Data Retrieval Worker scraps and collects metrics data from the container orchestration platform. It then converts the metrics into time series data. It gathers metrics from many sources, specified in its setups.
- Time Series Database stores the time series data from the data retrieval component.
- HTTP Server responds to requests and PromQL queries for the time series data. It then presents the information in a web user interface or dashboard. It can either use a third-party platform like Grafana or the inbuilt Prometheus Web UI.

## Installation

The first one on the list is the official Prometheus Helm Chart. To get this Helm chart, run this command:

`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts  
helm repo update  
helm install prometheus prometheus-community/prometheus
`

We have installed Prometheus on the Kubernetes Cluster. We can access the Prometheus server via port 80. The next step is to view the deployed Kubernetes resources. They are the pods and services the Helm Chart creates in your Kubernetes cluster.

We’ll use the prometheus-server Kubernetes service to access the Prometheus application. The prometheus-server is a ClusterIPtype. You can only access it within the Kubernetes cluster. We need to expose this Kubernetes service to access it outside the Kubernetes cluster. Exposing the prometheus-server Kubernetes service will generate a URL. We can load the URL on our browser and access the running application.

To expose the prometheus-server Kubernetes service, run this command:

`kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext`
This command will convert the ClusterIP type to the NodePort type. It makes the prometheus-server accessible outside the Kubernetes Cluster on port 9090.

Now we have exposed the prometheus-server Kubernetes service. Let’s access the Prometheus application using the following command:

`minikube service prometheus-server-ext`

We have installed Prometheus on Kubernetes using Helm. Prometheus is running inside the cluster and we can access it using a browser/URL. For the next steps of the tutorial:

First, we’ll install Grafana.
Next, we’ll integrate Prometheus and Grafana. Grafana will use Prometheus as the data source.
Finally, we’ll use Grafana to create the dashboards for monitoring and observing the Kubernetes cluster.

To install, we follow the same steps as those for installing Prometheus:

`helm install grafana grafana/grafana  
kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext
minikube service grafana-ext
`
The command generates the following URL:
![](https://semaphoreci.com/wp-content/uploads/2023/09/generated-url-grafana.png)
It might take a few moments for the URL to be available. You need to make several retries on your browser until you access the Grafana Kubernetes application using this URL. You also need to keep the terminal open and the tunnel command running in order to keep accessing the service.
![](https://semaphoreci.com/wp-content/uploads/2023/09/accessing-grafana.png)
The image above shows the Grafana Login page. To get the password for admin, run this command on a new terminal.

`kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo`

### Login into Grafana
To login into Grafana, input admin as the user name and your generated password. It will launch a Welcome to Grafana home page as shown below:
![](https://semaphoreci.com/wp-content/uploads/2023/10/grafana-add-first-source.jpg)

Now it the time to add Prometheus as the data source. To add Prometheus as the data source, follow these steps:

On the Welcome to Grafana Home page, click Add your first data source:
Select Prometheus as the data source:
![](https://semaphoreci.com/wp-content/uploads/2023/10/grafana-select-prometheus.jpg)

You will then add the URL where your Prometheus application is running. This is the first URL (internal to the cluster) shown when we ran minikube service prometheus-server-ext earlier.
![](https://semaphoreci.com/wp-content/uploads/2023/09/prometheus-url.png)

Click on “Save & test” to save your changes.

You have finished integrating Prometheus and Grafana on Kubernetes with Helm. The last step is to create a Grafana Dashboard. It will help us to visualize our Kubernetes cluster metrics.

### Grafana Dasrhboard

As mentioned earlier, you have the option of creating your dashboards from scratch. You can also import those that Grafana already offers. It this section, we will import a Grafana Dashborad.

To import a Grafana Dashboard, follow these steps:

Get the Grafana Dashboard ID from the Grafana public Dashboard library
![](https://semaphoreci.com/wp-content/uploads/2023/09/dashboard-library.png)

On this web page, search for Kubernetes:
![](https://semaphoreci.com/wp-content/uploads/2023/09/search-kubernetes.png)

Scroll until you find the Kubernetes cluster monitoring (via Prometheus) dashboard:
![](https://semaphoreci.com/wp-content/uploads/2023/09/kubernetes-cluster-monitoring.png)

Select Dashboard and copy the Dashboard ID:
![](https://semaphoreci.com/wp-content/uploads/2023/09/dashboard-id.png)

Go Back to Grafana and click Home on the top left corner:
![](https://semaphoreci.com/wp-content/uploads/2023/10/grafana-menu-dashboards.jpg)

It will dislay a menu.

- On the menu, click Dashboards
- Click New > Import
![](https://semaphoreci.com/wp-content/uploads/2023/10/grafana-dashboard-import.jpg)

Add the Grafana ID: You will add the Grafana ID that you have copied and click Load. The Grafana ID is 315.
![](https://semaphoreci.com/wp-content/uploads/2023/10/grafana-load-id.jpg)

Select a Promethues Data Source and Click Import:
![](https://semaphoreci.com/wp-content/uploads/2023/10/grafana-select-datasource.jpg)

It will the launch the Dashboard shown below:
![](https://semaphoreci.com/wp-content/uploads/2023/09/imported-dashboard1.png)

You use this dashboard to monitor and observe the Kubernetes cluster metrics. It displays the following Kubernetes cluster metrics:

Network I/O pressure.
Cluster CPU usage.
Cluster Memory usage.
Cluster filesystem usage.
Pods CPU usage.


### sources
- https://semaphoreci.com/blog/prometheus-grafana-kubernetes-helm
- https://opensource.com/article/21/6/chaos-grafana-prometheus



