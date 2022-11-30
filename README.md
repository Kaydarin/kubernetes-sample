# Setup Kubernetes on AWS

## Download CLIs:-

1. Kubernetes kubectl : https://kubernetes.io/docs/tasks/tools/
    - After installation, check version with command: `kubectl version --short --client`
2. AWS CLI : https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html
    - Create access key in AWS Console dashboard by selecting a user from Users: https://console.aws.amazon.com/iam/home?#/security_credentials
        - Bear in mind about the selected user, make sure that it is the user that will do all the EKS stuff.
        If for example, you created IAM user for the EKS related stuff but you configure your access key for your root user instead, you will have permission issue later on.
    - After installation, setup the CLI with command : `aws configure`
    - An output is shown and insert your credentials:
        ```
        AWS Access Key ID [None]: YOUR_CREATED_ACCESS_KEY_ID_FROM_ABOVE
        AWS Secret Access Key [None]: YOUR_CREATED_ACCESS_KEY_SECRET_FROM_ABOVE
        Default region name [None]: YOUR_SELECTED_REGION
        Default output format [None]: json
        ```

## Amazon ECR:-

1. Create IAM User with AdministratorAccess policy : https://docs.aws.amazon.com/AmazonECR/latest/userguide/get-set-up-for-amazon-ecr.html
2. Login created IAM User with below command in your terminal :-
    - `aws ecr get-login-password --region YOUR_SELECTED_REGION | docker login --username AWS --password-stdin YOUR_AWS_ACCOUNT_ID.dkr.ecr.YOUR_SELECTED_REGION.amazonaws.com`
3. Create repository with below command in your terminal :-
    - `aws ecr create-repository --repository-name YOUR_REPO_NAME --image-scanning-configuration scanOnPush=true --region YOUR_SELECTED_REGION`
4. Push Docker Images :- https://docs.aws.amazon.com/AmazonECR/latest/userguide/getting-started-cli.html
    - Get local images: `docker images`
    - Tag image : 
        ```
        docker tag YOUR_IMAGE_NAME:YOUR_IMAGE_TAG_NAME YOUR_AWS_ACCOUNT_ID.dkr.ecr.YOUR_SELECTED_REGION.amazonaws.com/YOUR_REPO_NAME:YOUR_REPO_TAG_NAME
        ```
    - Push the tagged image :
        ```
        docker push YOUR_AWS_ACCOUNT_ID.dkr.ecr.YOUR_SELECTED_REGION.amazonaws.com/YOUR_REPO_NAME:YOUR_REPO_TAG_NAME
        ```

## Amazon EKS:-

1. Setup Guide: https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html
2. ECR permission already configure when using AWS Cloudformation VPC, So you can edit your kubernetes `deployment.yaml` with your image: `YOUR_AWS_ACCOUNT_ID.dkr.ecr.YOUR_SELECTED_REGION.amazonaws.com/YOUR_REPO_NAME:YOUR_REPO_TAG_NAME`
3. Can start using kubectl commands:-
    - get all namespaces : `kubectl get namespaces`
    - get all info for my-namespace: `kubectl get all -n my-namespace`
    - delete my-namespace namespace: `kubectl delete ns my-namespace
    - delete all my-namespace pods: `kubectl delete --all pods --namespace=my-namespace`
    - access my-namespace's my-pods-name pod bash terminal: `kubectl exec --stdin --tty my-pods-name --namespace=my-namespace -- /bin/bash`
    - get all deployment by namespaces: `kubectl get deploy -A`
    - delete my-namespace's my-depyloy-name: `kubectl delete deploy my-deploy-name -n my-namespace` 
    - delete my-namespace's pods & services: `kubectl -n my-namespace delete pod,svc --all`
    - `kubectl describe pods`
    - `kubectl describe services`
    - `kubectl get events`
    - get all pods installed on which nodes: `kubectl get pod -o=custom-columns=NODE:.spec.nodeName,NAME:.metadata.name --all-namespaces`

## Extra resources:-

1. https://medium.com/google-cloud/kubernetes-101-pods-nodes-containers-and-clusters-c1509e409e16
2. https://aws.amazon.com/premiumsupport/knowledge-center/eks-kubernetes-services-cluster/
3. https://www.bluematador.com/blog/safely-removing-pods-from-a-kubernetes-node
4. https://lorenzo.aiello.family/running-laravel-on`-kubernetes/
5. https://medium.com/faun/deploy-a-laravel-app-to-amazon-eks-in-5-minutes-a94a41436157
6. https://docs.docker.com/engine/reference/commandline/tag/
7. https://kubernetes-on-aws.readthedocs.io/en/latest/user-guide/tls-termination.html
8. https://stackoverflow.com/questions/47369351/kubectl-apply-vs-kubectl-create
9. https://stackoverflow.com/questions/61171487/what-is-the-difference-between-namespaces-and-contexts-in-kubernetes
10. https://www.whitesourcesoftware.com/free-developer-tools/blog/docker-expose-port/
11. https://cert-manager.io/docs/