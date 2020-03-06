# What is Knative?

We will distribute load with loadbalancing within the scope of istio-example

Knative (pronounced kay-nay-tiv) extends [Kubernetes](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/) to provide a set of middleware components that are essential to build modern, source-centric, and container-based applications that can run anywhere

The serverless cloud computing model can lead to increased developer productivity and reduced operational costs.

# Knative Audience

-   Knative is designed for different personas:

![image](https://user-images.githubusercontent.com/3519706/75780449-6c1e5600-5d6c-11ea-9988-4a9ec1973556.png)


**Build**

-   A flexible approach to building source code into containers.

**Serving**

-   Enables rapid deployment and automatic scaling of containers through a request-driven model for serving workloads based on demand.

**Eventing**

-   An infrastructure for consuming and producing events to stimulate applications. Applications can be triggered by a variety of sources, such as events from your own applications, cloud services from multiple providers


## Configuration Knative

The Knative application runs via istio-ingressgateway. Since Knative uses this as a domain when using this gateway, it is first necessary to define a domain as described on the Knative site.

> [https://knative.dev/docs/serving/using-a-custom-domain/](https://knative.dev/docs/serving/using-a-custom-domain/)

For this, the istio-ingressgateway is registered as an ingress controller with an ingress controller to be a wildcard domain. Sample registration is as follows.
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: knative-domain
  namespace: istio-system
spec:
  rules:
  - host: "*.knative.<WORKER-IP>.nip.io"
    http:
      paths:
      - path: /
        backend:
          serviceName: istio-ingressgateway
          servicePort: 80
```
After the definition of Ingress is made, it is time to edit the domain for knative. For this, config-domain configmap is entered and edited with the following command.
```json
kubectl edit cm config-domain --namespace knative-serving
```
```yaml
apiVersion: v1
data:
  _example: |
    ################################
    #                              #
    #    EXAMPLE CONFIGURATION     #
    #                              #
    ################################
    # ...
    example.com: |
kind: ConfigMap
```
You can completely delete the "_example:" block in YAML or leave the optional comment to re-read it. What we need to do here is to define the wildcard domain we created. It is easily defined and saved as follows.

```yaml
apiVersion: v1
data:
  knative.<WORKER-IP>.nip.io: ""
kind: ConfigMap
[...]
```

# Knative Example

 - As an example, we will use the example given on the knative site. In
  the namespace given
 - istio-injection = enabled label, the following    knative example is
   run with YAML kubectl apply -f.
```json
kubectl label namespace <namespace> istio-injection=enabled
```
```yaml
apiVersion: serving.knative.dev/v1 # Current version of Knative
kind: Service
metadata:
  name: helloworld-go # The name of the app
spec:
  template:
    spec:
      containers:
        - image: gcr.io/knative-samples/helloworld-go # The URL to the image of the app
          env:
            - name: TARGET # The environment variable printed out by the sample app
              value: "Go Sample v1"
```
In this example, "string" written in "value" section will appear as output.

    kubectl apply -n <namespace> --filename knative-example.yaml

Örnek uygulanır ve çıktıya bakılır.
```json
[root@master ~]# kubectl get ksvc helloworld-go
NAME            URL                                                                LATESTCREATED         LATESTREADY           READY   REASON
helloworld-go   http://helloworld-go.<namespace>.knative.<worker-ip>.nip.io        helloworld-go-zpm6r   helloworld-go-zpm6r   True
```

Curl is thrown to the URL address and the process is completed.
```json
[root@master ~]# curl http://helloworld-go.<namespace>.knative.<worker-ip>.nip.io
Hello Go Sample v1!
```
# Tips

Some tips and tricks that you might find handy

The service can be deployed using the following command:
```json
kubectl get deployments -n knativetutorial
```
Invoke Service
```json
IP_ADDRESS="$(kubectl get svc istio-ingressgateway --namespace istio-system --output 'jsonpath={.spec.ports[?(@.port==80)].nodePort}')"

curl -H 'Host:greeter.knativetutorial.knative.<Worker_IP>.nip.io' <Worker_IP>:$IP_ADDRESS

curl -H 'Host:greeter.knativetutorial.knative.10.10.10.10.nip.io' 10.10.10.10:$IP_ADDRESS
```
Service
```json
kubectl --namespace knativetutorial  get services.serving.knative.dev greeter
```
Configuration
```json
kubectl --namespace knativetutorial get configurations.serving.knative.dev greeter
```
Routes
```json
kubectl --namespace knativetutorial get routes.serving.knative.dev greeter
```
Revisions
```json
kubectl --namespace knativetutorial get rev \
--selector=serving.knative.dev/service=greeter \
--sort-by="{.metadata.creationTimestamp}"
```
Watching Logs
```json
kubectl logs -n knativetutorial -f <pod-name> -c user-container
```
Cronjobsources
```json
kubectl -n knativetutorial get cronjobsources.sources.eventing.knative.dev
```
Channel
```json
kubectl -n knativetutorial get channels.eventing.knative.dev
```
Subscription
```json
kubectl --namespace knativetutorial get subscriptions.eventing.knative.dev event-greeter-subscriber
```
Knative eventing provides a Broker named default when a special label is added to a namespace.
```json
kubectl label namespace knativetutorial knative-eventing-injection=enabled
```
Broker
```json
kubectl -n knativetutorial get brokers.eventing.knative.dev
```
Triggers
```json
kubectl --namespace knativetutorial  get triggers.eventing.knative.dev event-greeter-trigger
```
```json
# Get the logs of the first istio-ingressgateway pod
# Shows what happens with incoming requests and possible errors
```

## We did 4 examples on Knative

 **1. knative event source example** 
 
 **2. knative auto scale example**
 
 **3. knative minumum scale**
 
 **4. knative service-pinned**
