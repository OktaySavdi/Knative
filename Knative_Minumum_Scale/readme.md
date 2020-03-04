# Minumum Scaling (knative minumum scale example)

We will make an example with minumum scale within the scope of knative

In real world scenarios your service might need to handle sudden spikes in requests. Knative starts each service with a default of **1** replica. As described above, this will eventually be scaled to zero as described above. If your app needs to stay particularly responsive under any circumstances and/or has a long startup time, it might be beneficial to always keep a minimum number of pods around. This can be done via an the annotation **autoscaling.knative.dev/minScale**.


The autoscale pods is computed using the formula:

```bash
totalPodsToScale = inflightRequests / concurrencyTarget
```

With this current setting of  **concurrencyTarget=10**  and  **inflightRequests=50**  , you will see Knative automatically scales the greeter services to  `**50/10 = 5 pods**`.


## Start the knative service

The service can be deployed using the command:

    kubectl apply -f minumumscale.yaml -n knativetutorial

Check pod or Service

    kubectl get pod -n knativetutorial
    kubectl get ksvc -n knativetutorial
    
![image](https://user-images.githubusercontent.com/3519706/75860922-a855c300-5e0d-11ea-8dd4-aabbb55a2731.png)

## Generate load

**Install Siege**
```bash
wget -c https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/s/siege-4.0.2-2.el7.x86_64.rpm https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/l/libjoedog-0.1.2-1.el7.x86_64.rpm -P installation
rpm -ivh installation/*.rpm
siege --version
```

```bash
istio_ingress="$(kubectl get svc istio-ingressgateway --namespace istio-system --output 'jsonpath={.spec.ports[?(@.port==80)].nodePort}')"
```

**Siege Load**
```json
siege -c 50 -r 10 -H "Host:prime-generator.knativetutorial.knative.<Worker_IP>.nip.io" "http://<Worker_IP>:$istio_ingress/?sleep=3&upto=10000&memload=100"

siege -c 50 -r 10 -H "Host:prime-generator.knativetutorial.knative.10.10.10.10.nip.io" "http://10.10.10.10:$istio_ingress/?sleep=3&upto=10000&memload=100"
```
![image](https://user-images.githubusercontent.com/3519706/75862375-e653e680-5e0f-11ea-85aa-5037de9bcdc3.png)
![image](https://user-images.githubusercontent.com/3519706/75862332-cfad8f80-5e0f-11ea-8403-b405c227bc7a.png)

## Monitoring and Control

**Control istio Config**

![image](https://user-images.githubusercontent.com/3519706/75861548-9de7f900-5e0e-11ea-8b4c-1c4615c9482a.png)

**Control Graph**

![image](https://user-images.githubusercontent.com/3519706/75871097-148bf300-5e1d-11ea-91da-d5ffb61f047d.png)

## **CLI on Server**

**Before Scale**

![image](https://user-images.githubusercontent.com/3519706/75870582-346ee700-5e1c-11ea-82be-5378ae3a8c63.png)

**After Scale**

![image](https://user-images.githubusercontent.com/3519706/75870696-6718df80-5e1c-11ea-8595-f5220dd87d84.png)

Reference : [Redhat](https://redhat-developer-demos.github.io/knative-tutorial/knative-tutorial-basics/0.7.x/04-scaling.html)






