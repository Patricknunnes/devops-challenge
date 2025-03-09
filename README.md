# DevOps Challenge - Deploy no Minikube

## 1. Visão Geral
Este repositório contém os manifestos e instruções necessárias para provisionar um banco de dados PostgreSQL e realizar o build e deploy dos serviços **Frontend** e **Backend** no Kubernetes. Também inclui a configuração de **escalabilidade horizontal automática** baseada no uso de memória (incluí também o de cpu).

## 2. Requisitos
Antes de iniciar, certifique-se de que tem as seguintes ferramentas instaladas:
- **Minikube** ([Instalação](https://minikube.sigs.k8s.io/docs/start/))
- **kubectl** ([Instalação](https://kubernetes.io/docs/tasks/tools/install-kubectl/))
- **Docker** ([Instalação](https://docs.docker.com/get-docker/))

## 3. Configuração e deploy do Projeto

### 3.1 Iniciar o Minikube
```bash
minikube start
```

### 3.2 Setar variáveis corretamente no Secret
Realizar a alteração das variáveis manualmente no Secret e depois copiar o arquivo, removendo o example, exemplo:
Para gerar os valores em base64, use:
```bash
echo -n "valorExemplo" | base64
```

Para copiar o arquivo, use:
```bash
cp kubernetes/secret-example.yaml kubernetes/secret.yaml
```

### 3.3 Criar o Namespace e Secret
```bash
kubectl apply -f kubernetes/namespace.yaml
kubectl apply -f kubernetes/secret.yaml
```

### 3.4 Provisionar o Banco de Dados PostgreSQL
```bash
kubectl apply -f kubernetes/postgres.yaml
```

### 3.5 Criar os Deployments do Backend e Frontend
```bash
kubectl apply -f kubernetes/backend.yaml
kubectl apply -f kubernetes/frontend.yaml
```

### 3.6 Criar o HPA
```bash
kubectl apply -f kubernetes/hpa.yaml
```

### 3.7 Como verificar o Cluster em execução
```bash
kubectl get all -n devops-challenge
```

## 4. Como escalar os serviços manualmente
Se for necessário aumentar ou diminuir a quantidade de réplicas manualmente, use:

```bash
kubectl scale deployment backend --replicas=5 -n devops-challenge
kubectl scale deployment frontend --replicas=5 -n devops-challenge
```

## 5. Como verificar logs e monitoramento

### 5.1 Logs dos Pods
Para ver os logs de um pod específico:
```bash
kubectl logs <nome do pod> -n devops-challenge
```
Exemplo:
```bash
kubectl logs pod/backend-6fb5d44bcc-fl2js -n devops-challenge
```

### 5.2 Monitoramento do uso de memória
Para verificar a utilização de recursos:
```bash
kubectl top pods -n devops-challenge
```

### 5.3 Descrever um Pod (para diagnóstico)
```bash
kubectl describe pod <nome do pod> -n devops-challenge
```

## 6. Escalabilidade horizontal
O sistema está configurado para aumentar a quantidade de réplicas até 10 sempre que a utilização de memória ultrapassar 70%.

Para visualizar o `HorizontalPodAutoscaler`:
```bash
kubectl get hpa -n devops-challenge
```

Para configurar manualmente:
```bash
kubectl autoscale deployment backend --memory-percent=70 --min=1 --max=10 -n devops-challenge
kubectl autoscale deployment frontend --memory-percent=70 --min=1 --max=10 -n devops-challenge
```

## 7. Possíveis melhorias
Para enriquecer o projeto, acredito que a sugestão de melhorias seja válida. Em ambientes de produção reais, o uso de Prometheus para monitoramento de métricas e Grafana para visualização dos dados seria importante para garantir a observabilidade e o monitoramento do cluster.

Além disso, outra melhoria que podemos incluir é a integração com ferramentas de CI/CD para automatizar os deployments e garantir a continuidade da entrega.
