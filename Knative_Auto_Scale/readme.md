# Auto Scaling (knative auto scaling example)

We will make an example with auto scale within the scope of knative

By default Knative Serving allows 100 concurrent requests into a pod. This is defined by the  `container-concurrency-target-default`  setting in the configmap  **config-autoscaler** in the  **knative-serving**  namespace.

For this exercise let us make our service handle only  **10**  concurrent requests. This will cause Knative autoscaler to scale to more pods as soon as we run more than 10 requests in parallel against the revision.


The autoscale pods is computed using the formula:

```bash
totalPodsToScale = inflightRequests / concurrencyTarget
```

With this current setting of  **concurrencyTarget=10**  and  **inflightRequests=50**  , you will see Knative automatically scales the greeter services to  `**50/10 = 5 pods**`.


## Start the knative service

The service can be deployed using the command:

    kubectl apply -f autoscale.yaml -n knativetutorial

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

![2020-03-04 11_49_33-Kiali Console](https://user-images.githubusercontent.com/3519706/75861639-bce68b00-5e0e-11ea-81ee-49a04eb3652f.png)

## **CLI on Server**

**Before Auto Scale**

![image](https://user-images.githubusercontent.com/3519706/75862754-76922b80-5e10-11ea-89f8-51042505a2c7.png)

**After Auto Scale**

![2020-03-04 11_34_48-mRemoteNG - confCons24092019 xml - master-10 57 165 51](https://user-images.githubusercontent.com/3519706/75861683-cc65d400-5e0e-11ea-8696-93883f65f6b6.png)

Reference : [Redhat](https://redhat-developer-demos.github.io/knative-tutorial/knative-tutorial-basics/0.7.x/04-scaling.html)