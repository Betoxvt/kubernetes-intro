# Introdução ao Kubernetes

Ferramenta para gerenciar, orquestrar os containers.

- Resolve questões de escalabilidade.
- Principalmente num ambiente de cloud utilizando uma arquitetura de microserviços, é aumentar horizontávelmente (réplicas).
- Fazer o balanceamento dos microserviços entre as instâncias.
- Conseguir fazer atualização de versão sem downtime.
- Conseguir fazer rollback também.
- Kubernetes é um cluster (conjunto) de máquinas.

- Cada máquina vai exercer um papel de dois possíveis:

1. Control Plane (Master): Componente responsável por gerenciar (orquestrar) o cluster.
- API-Server: recebe toda a comunicação externa
- ETCD: banco chave-valor que armazena todas as informações do kubernetes, utilizado para back-up também
- Scheduler: gerencia onde (endereçamento) vai ser executado e criado os containers
- Controller Manager: Responsável por garantir os estados, gerenciar os controladores do kubernetes
- Criar mais de um para garantir a disponibilidade

2. Worker Node (Node): Componente responsável por executar os containers, responsável pelos workloads.
- Kubelet: Resp. por solicitar a execução dos meus containers e verificar a saude deles
- Kube Proxy: pela conexão com a internet
- Container Runtime: a conexão com o Kubelet é feito pela interface de comunicação CRI (container runtime interface)

## Criar cluster kubernetes local utilizando o kind
- Utiliza docker por default
`systemctl start docker`
- Criar um cluster (cria com apenas um nó)
`kind create cluster`
configura todo o KubeCTL
- Listar os nós do kubectl
`kubectl get nodes`
- Utiliza o kubeadm
- Listar clusters
`kind get clusters`
- Deletar um cluster
`kind delete cluster` ou `kind delete cluster --name meucluster`

### Montar um setup de estudo com kind
- Criar um kind-config.yaml
- Multi-node clusters (documentation)
`kind create cluster --name meucluster --config ./kind-config.yaml`
- Problema no fedora com muitos nodes, provavelmente no firewalld

### Acessar com o kind os serviços (K3D é alternativa mais leve)
- Fazer o mapeamento de portas
```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30000
        hostPort: 8080
  - role: worker
  - role: worker
```

### Fazer um deploy
- arquivo `deploy.yaml` por exemplo coloque um nginx
`kubectl apply -f deply.yaml`
- observar
`kubectl get pods`

## Criar utilizando o AKS (Azure)
- Com o azure-cli instalado, `az login`
- No portal Azure: Kubernetes services > cluster kubernetes (cobranças)
- Depois do deploy no portal azure, clicar em `Go to resource`, tem todas as informações
- `Connect`, copiar os comandos e colar no terminal
- Configurar o deploy.yaml
- faz o deploy pelo terminal
- checa os pods, os services


- Dica: criar uma pipeline (CI/CD) no github actions com scheduler (tipo 00:00h) para deletar todos os resource groups, evitar cobranças por esquecer ligadas as maquinas na cloud

## Montar um ambiente on premise K3S
- Consegue com raspberry pi, usa pouco recurso computacional
- Utiliza maquinas virtuais na azure ou locais também
- K3S é uma solução leve e simples para linux
- Tem tudo na documentação, bem simples.
- Caso erro de permissão para ler o k3s.yaml. mudar a permissão com `sudo chmod 777 /etc/.../k3s.yaml` claro que para ambiente de desenvolvimento sem se preocupar com as permissões, tem que ter cuidado fora disso.
- Para criar outros workernodes tem que pegar o certificado do control plane.
`ssh adminuser@40.71.126.241` - Estudar, esses ip devem ser das VM
`sudo cat /var/lib/rancher/k3s/server/node-token`
`curl -sfl https://get.k3s.io | K3S_URL=https://<ip interno (private) do control plane>:6443 K3S_TOKEN=<token obtido antes> sh -`

