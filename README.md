# k8s-jenkins-infra
## How to launch the cluster

1. Install [kind](https://kind.sigs.k8s.io/).
2. Run the following command from the current directory:
```bash
kind create cluster --config=kind-config.yaml
```

## How to install the Helm chart

Launch the command:
```bash
helm install -f modified-values.yaml jenkins ./jenkins
```

[Jenkins](./jenkins/) directory contains unchanged Jenkins Helm chart.

[values.yaml](./jenkins/values.yaml) file keeps default chart values.

[modified-values.yaml](./modified-values.yaml) file keeps modified chart values to work with.

## How to use Jenkins

When the Helm chart is deployed, launch the command to get Jenkins admin password:
```bash
kubectl exec --namespace default -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/additional/chart-admin-password && echo
```

After that, port forward Jenkins service:
```bash
kubectl port-forward service/jenkins 8080:8080
```

Then proceed to ```localhost:8080``` and type admin credentials.

## The workflow

When the Jenkins deployment is initialized, [pipelineJob](./modified-values.yaml#26) defined with Job DSL plugin launches.

It checks updates in [hello-flask repo](https://github.com/Samofimp/hello-flask/) every 15 minutes and executes ```Jenkinsfile``` pipeline in case of any changes.

>```pipelineJob.triggers.scm``` is deprecated but no new way to define trigger is represented in the API.

## Pipeline creation

To create and execute pipeline manually, follow these steps:

1. In Jenkins dashboard choose **New Item**.
2. Select the **Pipeline** item, specify a name.
3. In the pipeline script definition paste the [following code](https://github.com/Samofimp/hello-flask/blob/master/Jenkinsfile) and save it.
4. In the side menu choose **Build Now**

