# AWS EKS Cluster Setup

This repository contains configurations to set up an AWS EKS cluster using `eksctl`. Additionally, it includes configurations for AWS Load Balancer Controller and External DNS Helm charts.

## Prerequisites

Before running the scripts, ensure you have the following prerequisites installed:

- [AWS CLI](https://aws.amazon.com/cli/)
- [eksctl](https://eksctl.io/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Helm](https://helm.sh/docs/intro/install/)

### AWS IAM Policies

Before provisioning the cluster, create the requisite IAM policies for AWS Load Balancer Controller and External DNS.
```bash
aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://aws/aws-load-balancer-controller-iam-policy.json

aws iam create-policy \
--policy-name AWSExternalDnsIAMPolicy \
--policy-document file://aws/external-dns-iam-policy.json
```

## EKS Cluster Setup

Update the `cluster.yaml` file with ARNs of the IAM policies created in the previous step.
Create the EKS cluster using eksctl with the provided cluster configuration:

```bash
eksctl create cluster -f cluster.yaml
```
This command creates an EKS cluster based on the configuration specified in the `cluster.yaml` file. Adjust the configuration file to match your requirements.

Write the kubeconfig for the newly created cluster:

```bash
eksctl utils write-kubeconfig --cluster=labs-cluster
```

Deploy AWS Load Balancer Controller & External DNS helm charts:

```bash

helm repo add eks https://aws.github.io/eks-charts
helm repo add bitnami https://charts.bitnami.com/bitnami

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system -f helm/aws-load-balancer-controller.yaml
helm install external-dns bitnami/external-dns -n kube-system -f helm/external-dns.yaml 
```

## Clean up

```bash
eksctl delete cluster -f cluster.yaml --disable-nodegroup-eviction
```
