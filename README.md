# microservice-demo

Hi, new arrival and dedicated to be compatible with cloud native/SRE related knowledge. Setup this repo to keep going with my target.

## Architecture for long term

1. [Local build Kubernetes cluster](./Kubernetes%20Cluster%20Setup.md)
2. [sample microservice using istio](./bookinfo/README.md)
3. [minimal gradle-springboot application](https://github.com/Wang-kk714/gradle-spring-boot)
    code refactoring bookinfo/reviews service using this applcaition
4. Intergrated with gRPC
5. [container registry & helm repo using harbor](https://goharbor.io/)
6. CICD pipeline - implement based on [tekton-pipeline](https://github.com/Wang-kk714/tekton-pipeline)
7. Inrtegrated with Opentelemetry
8. Log collector - EFK(Elastic search & Fluentd/Fluent Bit & Kibana)

### TODO recently(starting from June, 26th - ETA one month)
1. deploy a singleton bookinfo/reviews service refactoring with my minimal gradle springboot application on local cluster.
2. gradle knowledge
3. practice my CKA examination...