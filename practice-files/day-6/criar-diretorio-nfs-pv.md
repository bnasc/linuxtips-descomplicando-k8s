# Criar um diretório NFS para Persistent Volume (PV) do tipo 'hostPath'
## Referência: https://github.com/badtuxx/DescomplicandoKubernetes/tree/main/pt/day_six


## Em um dos nodes, criar um novo diretório
```shell
mkdir /mnt/nfs
```

## Instalando os pacotes necessários
```shell
sudo apt-get install nfs-kernel-server nfs-common
```

## Editando o arquivo de configuração do NFS para adicionar o diretório que será compartilhado entre os nodes
```shell
sudo vi /etc/exports

/mnt/nfs *(rw,sync,no_root_squash,no_subtree_check) #Linha a adicionar
```

## "Informando" o NFS que o diretório está pronto para ser compartilhado
```shell
sudo exportfs -arv
```

## Checando se o NFS está funcionando corretamente
```shell
showmount -e
```

Resultado esperado:
```shell
Export list for localhost:
/mnt/nfs *
```

## Criando um arquivo de teste para checar se todos os pods conseguem ler os dados compartilhadps. 

No node onde o volume foi criado, execute
```shell
echo "<h1>GIROPOPS STRIGUS GIRUS</h1>" > /mnt/data/index.html
```
