# Deploying Gitea on Kubernetes

This repository contains a set of Kubernetes manifests for deploying [Gitea](https://about.gitea.com/).

The configuration is designed to separate application configuration, metadata, and the actual media files for better data management.

<br>

## Prerequisites

1. A working Kubernetes cluster (e.g., MicroK8s, K3s, k0s). This guide assumes a bare-metal cluster.
2. An Ingress Controller installed and configured. This setup is tested with Traefik.
3. Storage Configuration: This setup separates configuration data from media data.
4. User and Group IDs (PUID/PGID): To avoid file permission issues on your storage volumes, the Gitea container should run with the same user and group ID as the owner of your media and configuration files.

<br>

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/simon-verbois/gitea-k8s
cd gitea-k8s
```

### 2. Rename the files

```bash
for file in *.yaml.template; do mv "$file" "${file%.template}"; done
```

### 3. Custom the configuration files

You have to adapt the yaml file, follow the hint for each file

### 4. Deploy Gitea

Apply all YAML manifests in a single command from the project root:

```bash
kubectl apply -f .
```

This will create the namespace, PVCs, secret, configmap, deployment, service, and ingress.

### 5. Access Gitea

After a few minutes, while the image is downloaded and the pod starts, you should be able to access your Gitea instance via the URL you configured in the ingress (e.g., `https://gitea.your-domain.com`).

<br>

## Maintenance

### Updating the Gitea Image

The deployment uses the `gitea/gitea:latest` image. To update to the latest version, you can trigger a rolling update of the deployment, which will force Kubernetes to pull the newest image.

```bash
kubectl rollout restart deployment/gitea-deployment -n gitea
```

You can monitor the progress of the update with:

```bash
kubectl rollout status deployment/gitea-deployment -n gitea
```

<br>

## Uninstallation

To remove all the resources created by these manifests, run the following command:

```bash
kubectl delete -f .
```

**Note:** This will also delete the `gitea` namespace. The `PersistentVolumeClaim` will be deleted, but the actual data on your storage volume might remain, depending on your `StorageClass` reclaim policy.

<br>

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.