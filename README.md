# apama-prometheus-grafana

A demo for Apama with Prometheus and Grafana

## Using this repository

You can use this sample to deploy a Prometheus server and a Grafana server together with an Apama which is being monitored and a second Apama being used as a load generator to demonstrate the metrics through Grafana. To deploy it using Docker Stack run:

    docker stack deploy -c docker-compose.yaml prometheus

This will start up all the servers, deploy the application and configure the monitoring. To access it first you need to know which port on your Docker host has been mapped to the Grafana server:

    docker service inspect prometheus_grafana | grep PublishedPort

Browse to this port on your Docker host using a web browser and login with the default Grafana credentials admin / admin.

Once you're done to shut down the stack run:

    docker stack rm prometheus

## Using this repository with Kubernetes

To instead deploy with Kubernetes, you must run the following:

    kubectl create namespace prometheus
	 kubectl --namespace prometheus create configmap grafana-conf-dashboards --from-file=grafana-config/dashboards/
	 kubectl --namespace prometheus create configmap grafana-conf-datasources --from-file=grafana-config/datasources/
    kubectl --namespace prometheus create configmap grafana-dashboards --from-file=grafana-dashboards/
    kubectl --namespace prometheus create configmap prometheus-config --from-file=prometheus/
    kubectl --namespace prometheus create configmap apama-config --from-file=apama/
    kubectl --namespace prometheus create configmap sender-config --from-file=sender/
	 kubectl --namespace prometheus create -f kubernetes.yaml 

You can get the exposed port using this command (look for NodePort):

	 kubectl --namespace prometheus describe service grafana

And to shut everything down run:

	 kubectl --namespace prometheus delete -f kubernetes.yaml 
	 kubectl --namespace prometheus delete configmap grafana-conf-dashboards
	 kubectl --namespace prometheus delete configmap grafana-conf-datasources
    kubectl --namespace prometheus delete configmap grafana-dashboards
    kubectl --namespace prometheus delete configmap prometheus-config
    kubectl --namespace prometheus delete configmap apama-config
    kubectl --namespace prometheus delete configmap sender-config
    kubectl delete namespace prometheus

## Using Grafana

Select Home and then Apama Monitoring. Here you will see a standard Apama Grafana dashboard showing the state of the running process. You'll see four graphs:

 * Input Queue shows both the the rate of events being received each second (which should show the varying input load from the sender process) and the outstanding input queue size (which will normally be 0, indicating that the application is keeping up)
 * Output Queue shows the output rate and queue size as above. This correlator only receives, so doesn't show anything in this graph.
 * Memory Usage shows the physical and virtual memory used by the correlator process.
 * Correlator Objects shows the number of Contexts, Monitor instances and Listeners in the correlator process. This will be constant for this application because it does not dynamically create/destroy those

You may want to select a non-default time range either in the top right, or by dragging a range on the graph since your new processes won't have very many data points. The refresh button in the top right must be used to get the latest information.
