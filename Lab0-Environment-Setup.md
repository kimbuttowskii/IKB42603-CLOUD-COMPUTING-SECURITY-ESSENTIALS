# IKB42603 Lab 0 — Environment Setup Report

## Course Information

**Course:** IKB42603 Cloud Computing Security Essentials  
**Lab:** Lab 0 - Environment Setup   
**Name:** Muhammad Amirul Hakim Bin Walid  
**Student ID:** 52215124636   
**Date:** 28 July 2026  

## Objective

The objective of this setup is to prepare the local lab environment required before Lab 1. Based on the setup cheatsheet, the environment must support Docker, AWS CLI v2, kind, kubectl, OpenSSL, oathtool, LocalStack, and a local Kubernetes cluster.

All services are intended to run locally. LocalStack is used as the local AWS simulator, and kind is used to run Kubernetes inside Docker.

## Platform used

The screenshots show the commands being run in a Kali Linux terminal. This conforms to the cheatsheet's requirement to use a Bash-compatible shell: later labs use Bash syntax such as heredocs, `sha256sum`, and single quotes.

| Component | Verified version / state |
| --- | --- |
| Operating environment | Kali Linux, kernel `6.12.13-amd64` |
| Docker | `28.5.2+dfsg4` |
| AWS CLI | `2.36.9` |
| kind | `0.23.0` |
| kubectl client | `v1.33.4` (Kustomize `v5.5.0`) |
| OpenSSL | `3.4.0` |
| oathtool | `2.6.14` |
| LocalStack | Community edition `3.0.2` |
| Kubernetes | one Ready control-plane node, `v1.30.0` |

## Step 1 — Install and verify Docker

Docker is required to run containers, LocalStack, and the Kubernetes-in-Docker (kind) cluster. On Linux, the cheatsheet directs users to install Docker, optionally add the user to the `docker` group, then verify access.

```bash
docker --version
docker run --rm hello-world
```

The Docker version check returned `Docker version 28.5.2+dfsg4`. The `docker run --rm hello-world` command also completed successfully. Its output confirms that the Docker client contacted the Docker daemon, downloaded the `hello-world` image, created a container, and streamed the expected **Hello from Docker!** message. Docker is therefore installed and functioning correctly.

Evidence:  

<img width="630" height="64" alt="1 docker" src="https://github.com/user-attachments/assets/c9248691-6692-4c6e-9b81-20d50001ce44" />  

<img width="843" height="462" alt="2  docker run" src="https://github.com/user-attachments/assets/087f5af8-a249-428e-9607-0717dc7fee8e" />

## Step 2 — Install and verify AWS CLI v2

AWS CLI v2 is used in later labs to issue AWS-style commands to LocalStack; no real AWS account is needed. The guide's verification command is:

```bash
aws --version
```

The command returned `aws-cli/2.36.9 Python/3.14.6 Linux/6.12.13-amd64`, verifying that AWS CLI v2 is installed and on `PATH`.

Evidence:  

<img width="614" height="59" alt="3  aws cli" src="https://github.com/user-attachments/assets/178102db-317e-4d63-b130-9de616fb4326" />

## Step 3 — Install and verify kind and kubectl

`kind` creates a local Kubernetes cluster inside Docker, while `kubectl` administers that cluster. The guide specifies the following checks:

```bash
kind --version
kubectl version --client
```

The results confirm `kind v0.23.0` and a `kubectl` client at `v1.33.4`, with Kustomize `v5.5.0`. Both tools are therefore installed and available for Labs 1, 2, and 4.

Evidence:  

<img width="599" height="68" alt="4  kind" src="https://github.com/user-attachments/assets/3d53a9e5-c4ec-406a-9689-6b7c71e0e3be" />  

<img width="510" height="85" alt="5  kubectl" src="https://github.com/user-attachments/assets/38fdf273-e4f2-48ab-bead-5f6d80674d2e" />

## Step 4 — Install and verify helper tools

The cheatsheet identifies OpenSSL for keys, encryption, and certificates in Lab 3, and oathtool for MFA/TOTP generation in Lab 4. Their checks are:

```bash
openssl version
oathtool --version
```

`openssl version` reported `OpenSSL 3.4.0 22 Oct 2024`; `oathtool --version` reported `OATH Toolkit 2.6.14`. Both utilities are installed and ready. Trivy requires no local installation because the labs run it in a Docker container, for example:

```bash
docker run --rm aquasec/trivy image <image-name>
```

Evidence:  

<img width="569" height="64" alt="6  openssl" src="https://github.com/user-attachments/assets/60b0789a-a357-4647-9379-a66ef5fab0a4" />  

<img width="631" height="179" alt="7  oathool" src="https://github.com/user-attachments/assets/b64d71a7-918b-4a6b-a875-c16c890b4d23" />

## Step 5 — Start LocalStack and confirm its health

LocalStack simulates AWS services locally. It was started using the guide's Docker-based method:

```bash
docker run -d --name localstack -p 4566:4566 localstack/localstack:3.0
```

Docker returned a container ID, confirming that the `localstack` container was created in detached mode and port `4566` was published on the host. The guide uses the untagged `localstack/localstack` image; the evidence deliberately pins it to the compatible `3.0` tag.

The health endpoint was then checked:

```bash
curl http://localhost:4566/_localstack/health
```

The returned JSON reports LocalStack Community edition `3.0.2` and lists services such as S3, IAM, Lambda, DynamoDB, EC2, CloudFormation, and STS as `available`. This verifies that LocalStack started successfully and is responding on the required local endpoint.

Evidence:  

<img width="656" height="71" alt="8  localstack" src="https://github.com/user-attachments/assets/14f47948-5196-481f-ba74-c984d8ed1a98" /> 

<img width="927" height="203" alt="9  check health" src="https://github.com/user-attachments/assets/c7c6dfac-46b9-4d98-8a17-7392e26b86dc" />

## Step 6 — Create and verify the kind Kubernetes cluster

Following the cheatsheet, a cluster named `ccse` was created:

```bash
kind create cluster --name ccse
```

The output records successful node-image preparation, configuration writing, control-plane startup, CNI installation, and StorageClass installation. `kubectl` was configured to use context `kind-ccse`.

The cluster was verified with:

```bash
kubectl cluster-info --context kind-ccse
kubectl get nodes
```

The Kubernetes control plane and CoreDNS were reachable at the local API endpoint. `kubectl get nodes` shows `ccse-control-plane` in `Ready` state, with role `control-plane` and Kubernetes version `v1.30.0`. This demonstrates that the cluster is operational.

Evidence:  

<img width="782" height="252" alt="10  kubernetes cluster" src="https://github.com/user-attachments/assets/ee64b82a-4752-406d-9913-0a980bf46f5c" />  

<img width="924" height="210" alt="11  verify kubernetes cluster" src="https://github.com/user-attachments/assets/f52564fa-8b5a-4d27-9077-83a523de3521" />

## Step 7 — Configure AWS CLI for LocalStack and verify connectivity

The cheatsheet requires dummy credentials because LocalStack accepts arbitrary values. These one-time settings should be applied if not already configured:

```bash
aws configure set aws_access_key_id test
aws configure set aws_secret_access_key test
aws configure set region us-east-1
```

For each new terminal session, the LocalStack endpoint can be stored in a variable:

```bash
EP='--endpoint-url=http://localhost:4566'
```

The evidence uses that variable and performs the required STS check:

```bash
aws $EP sts get-caller-identity
```

The command returned LocalStack's expected dummy identity: user ID `AKIAIOSFODNN7EXAMPLE`, account `000000000000`, and ARN `arn:aws:iam::000000000000:root`. This confirms the CLI was directed to LocalStack, rather than attempting to contact real AWS.

Evidence:  

<img width="650" height="191" alt="12  Configure AWS CLI for LocalStack" src="https://github.com/user-attachments/assets/a7197913-652e-44e2-bf1e-0e0c7d56f844" />

## Pre-lab checklist

| Checklist item from the guide | Status | Evidence |
| --- | --- | --- |
| Docker command prints a version | Complete | [Screenshot 1](evidence/1.docker.png) |
| `docker run --rm hello-world` works | Complete | [Screenshot 2](evidence/2.%20docker%20run.png) |
| AWS CLI v2 prints a version | Complete | [Screenshot 3](evidence/3.%20aws%20cli.png) |
| kind and kubectl client commands work | Complete | [Screenshots 4–5](evidence/4.%20kind.png) |
| LocalStack starts and health endpoint responds | Complete | [Screenshots 8–9](evidence/8.%20localstack.png) |
| AWS STS works through LocalStack endpoint | Complete | [Screenshot 12](evidence/12.%20Configure%20AWS%20CLI%20for%20LocalStack.png) |
| kind cluster starts and node is Ready | Complete | [Screenshots 10–11](evidence/10.%20kubernetes%20cluster.png) |
| Bash-compatible terminal is in use | Complete (Kali terminal shown) | All screenshots |

## Routine start, status, and cleanup commands

Use the following commands in later lab sessions. These are taken from the cheatsheet and are not commands evidenced as executed in the supplied screenshots.

```bash
# Start LocalStack if it exists; otherwise create it.
docker start localstack 2>/dev/null || docker run -d --name localstack -p 4566:4566 localstack/localstack

# Set the AWS CLI endpoint for this shell.
EP='--endpoint-url=http://localhost:4566'

# Inspect the active local environment.
docker ps
kind get clusters

# Stop services when required.
docker stop localstack
docker start localstack

# Remove the LocalStack container or Kubernetes cluster when they are no longer needed.
docker rm -f localstack
kind delete cluster --name ccse
```

For disk cleanup, the guide also provides `docker system prune -f` and `kind delete clusters --all`. These remove unused local resources; run them only when the relevant lab containers and clusters are no longer needed.

## Conclusion

All supplied evidence supports a functioning local lab environment: Docker has passed both version and `hello-world` checks, the required command-line tools are installed, LocalStack is healthy at `http://localhost:4566`, AWS CLI can communicate with it, and the `ccse` kind Kubernetes cluster has a Ready control-plane node. Every item in the guide's pre-lab checklist is now evidenced.
