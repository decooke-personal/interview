# Goals
1. Create a Dockerfile to containerize the application.
2. Create a minimal CI/CD pipeline to demonstrate building and pushing Docker images to a container registry (e.g., Docker Hub, AWS ECR, etc.).
3. Develop a Helm chart to deploy the application on a K8s or K3s cluster.
   1. Make sure that whatever you implement in the Helm chart is `as close as possible to production-ready`.
4. Add relevant observability
5. Provide clear instructions on how to build and deploy the application, including any prerequisites.

---

## #1 Create a Dockerfile to containerize the application
    Added ./app/Dockerfile, builds on latest Amazon Corretto SDK and runtime images
    Updated ./app/pom.xml to latest Spring and Java versions, added dependicies for OTEL

## #2 Create a minimal CI/CD pipeline to demonstrate building and pushing Docker images to a container registry (e.g., Docker Hub, AWS ECR, etc.)
    See ./cicd/backend-api-build.yaml
    A GitHub Actions workflow for building and pushing the application to a GHCR packge repository

## #3 Develop a Helm chart to deploy the application on a K8s or K3s cluster
    See ./helm/backend-api

## #4 Add relevant observability
    See ./k8s-configs
    Contains files to create otel collector and instrumentation objects to support auto-instrumentation of the Java app, requires opentelemetry-operator 
        https://github.com/open-telemetry/opentelemetry-operator

## #5 Provide clear instructions on how to build and deploy the application, including any prerequisites
    Assuming a multi-tenet K8s cluster where environments are seperated by namespace and any network isolation needed has already been done.

    Prereqs:
        K8s cluster
        Helm
        Kubectl
        Secret named "ghcr" of type docker-registry containing login information to ghcr.io in target namespace
            kubectl create secret docker-registry ghcr --docker-server=ghcr.io --docker-username=<yourusername> --docker-password=<yourPAT>
        A namespace to target
            kubectl create namespace <environmentName>
    Optional for Otel:
        otel exporter endpoint - Prometheus, ElasticSearch, etc
        opentelemetry-operator - https://github.com/open-telemetry/opentelemetry-operator

    Once all prereqs are met it should be as simple as installing the helm chart into a namespace
        helm install backend-api ./helm/backend-api -n <environmentName>

    To access the backend-api service in the cluster, forward the port using kubectl
        kubectl port-forward service/backend-api 8080:8080 -n <environment>
    Then access the welcome page with a browser
        http://127.0.0.1:8080/api/welcome

    To build locally
        docker build . -t <tag>
    To run locally
        docker run -p 8080:8080 <tag>
    To access locally
        http://127.0.0.1:8080/api/welcome