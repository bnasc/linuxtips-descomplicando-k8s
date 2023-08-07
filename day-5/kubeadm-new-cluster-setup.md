# Instalacao Cluster Kubernetes usando kubedam
## Referência: https://github.com/badtuxx/DescomplicandoKubernetes/tree/main/pt/day_five


## Desativando o swap
```shell
sudo swapoff -a
```

## Carregando os modulos do kernel
```shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

## Configurando os parâmetros do sistema
```shell
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

## Instalando os pacotes Kubernetes
```shell
sudo apt-get update && sudo apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl

sudo apt-mark hold kubelet kubeadm kubectl
```

## Instalando o containerd
```shell
sudo apt-get update && sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update && sudo apt-get install -y containerd.io
```

## Configurando o containerd
```shell
sudo containerd config default | sudo tee /etc/containerd/config.toml

sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl status containerd
```

## Habilitando o serviço kubelet
```shell
sudo systemctl enable --now kubelet
```

## Inicializando o cluster
Substitua <O IP INTERNO QUE VAI FALAR COM OS NODES> pelo endereço IP da máquina que está atuando como control plane.
```shell
sudo kubeadm init --pod-network-cidr=10.10.0.0/16 --apiserver-advertise-address=<O IP INTERNO QUE VAI FALAR COM OS NODES>
```

## Configurar o acesso ao cluster com kubectl
```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

# Adicionando nodes ao cluster
Executar nos nodes após instalações acima.\
Apenas exemplo. Mensagem especifica para cluster exibida após inicialização com sucesso do control-plane.
```shell
sudo kubeadm join <IP INTERNO>:6443 --token <TOKEN> \
	--discovery-token-ca-cert-hash <HASH DO CERTIFICADO> 
```
