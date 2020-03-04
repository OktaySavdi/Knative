
# Event Source (knative event source example)

We will make an example with event source within the scope of knative

The event source listens to external events e.g. a kafka topic or for a file on a FTP server. It is responsible to drain the received event(s) along with its data to a configured [sink](https://en.wikipedia.org/wiki/Sink_(computing))

## Start the knative service

The service can be deployed using the command:

    kubectl apply -f Knative_Event_Source/ -n knativetutorial

Check pod or Service

    kubectl get cronjobsources.sources.eventing.knative.dev -n knativetutorial
    kubectl get pods -n knativetutorial
    
![image](https://user-images.githubusercontent.com/3519706/75873537-1bb50000-5e21-11ea-935d-1c73f54e1940.png)

Reference : [Redhat](https://redhat-developer-demos.github.io/knative-tutorial/knative-tutorial-basics/0.7.x/05-eventing/eventing-src-svc.html)