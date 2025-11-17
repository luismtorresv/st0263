# WordPress en AWS EKS con Amazon EFS

Referencia oficial: https://aws.amazon.com/blogs/storage/running-wordpress-on-amazon-eks-with-amazon-efs-intelligent-tiering/

## 1. Crear el clúster EKS

- Cree un clúster llamado `myeks` con un node group de 2 nodos.

## 2. Configurar el shell para acceder al clúster

```bash
aws eks update-kubeconfig --region us-east-1 --name myeks
kubectl get nodes
```

Ejemplo:

```bash
$ kubectl get nodes
NAME                            STATUS   ROLES    AGE   VERSION
ip-172-31-32-239.ec2.internal   Ready    <none>   24m   v1.32.3-eks-473151a
ip-172-31-81-246.ec2.internal   Ready    <none>   24m   v1.32.3-eks-473151a
```

## 3. Crear el sistema de archivos EFS

- Cree un recurso EFS y anote su ID (por ejemplo, `fs-0d2b5ff834bfe5f61`).
- Actualice ese ID en `02wordpress-deployment.yaml`.

## 4. Instalar Helm (si aplica)

En Linux:

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

## 5. Agregar el repositorio del driver EFS

```bash
helm repo add aws-efs-csi-driver https://kubernetes-sigs.github.io/aws-efs-csi-driver/
```

## 6. Instalar el driver EFS en EKS

```bash
kubectl apply -f private-ecr-driver.yaml
helm repo update
helm install aws-efs-csi-driver aws-efs-csi-driver/aws-efs-csi-driver \
  --namespace kube-system \
  --set controller.serviceAccount.create=false \
  --set controller.serviceAccount.name=efs-csi-controller-sa
kubectl get pods -n kube-system | grep efs
```

## 7. Desplegar MySQL y WordPress

> Se utiliza `01mysql-deployment.yaml` en lugar del manifiesto sugerido en la referencia oficial.

```bash
kubectl apply -f 01mysql-deployment.yaml
kubectl apply -f 02wordpress-deployment.yaml
```

### Monitoreo

```bash
kubectl get pods --watch
kubectl get all -o wide
```

### Acceder a un pod

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

### Eliminar WordPress

```bash
kubectl delete -k ./
```
