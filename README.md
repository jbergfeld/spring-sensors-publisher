# Demo application to generate sample data for the Spring Sensors application.

This application acts as a sensor that generates and sends sensor data via asynchronous messaging.
The data is consumed and displayed on a dashboard by an application available [here](https://github.com/tanzu-end-to-end/spring-sensors-sensor).

[Spring Cloud Stream](https://spring.io/projects/spring-cloud-stream), a framework built on top of Spring Boot and Spring Integration, is used as a flexible messaging abstraction. 
Spring Cloud Stream supports a variety of binder implementations. In this case, we are using the one for RabbitMQ.

So as a prerequisite to run this application, you need a RabbitMQ cluster running in the same Kubernetes namespace (e.g. provisioned via the [RabbitMQ Cluster Operator for Kubernetes](https://www.rabbitmq.com/kubernetes/operator/operator-overview.html)).
```
kubectl apply -f - << EOF
apiVersion: rabbitmq.com/v1beta1
kind: RabbitmqCluster
metadata:
  name: rmq-1
EOF
```

To build and store the publisher container: (ensure JAVA_HOME points to an installed JDK and a Docker daemon is running)

```
./mvnw spring-boot:build-image -Dspring-boot.build-image.imageName=docker.io/library/spring-sensors-publisher:latest
```

```
docker tag spring-sensors-publisher:latest REGISTRY/PROJECT/spring-sensors-publisher:latest
```

```
docker push REGISTRY/PROJECT/spring-sensors-publisher:latest
```

Where REGISTRY is your container registry and PROJECT exists with the registry.

Deploy the conatiner into your current cluster context:

```
kubectl apply -f kubernetes-deployment.yaml
```

Note: replace the REGISTRY and PROJECT placeholders in the manifest with your respective values.

