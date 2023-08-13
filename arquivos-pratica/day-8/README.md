## Criar arquivos de certificado e chave privada
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout chave-privada.key -out certificado.crt
```

Arquivos de saída:
* certificado.crt
* chave-privada.key

---

## Prática de External Secrets Operator (ESO) realizada com AWS Secrets Manager ao invés do Hashicorp Vault
Referência:
> https://cloudhero.io/getting-started-with-external-secrets-operator-on-kubernetes-using-aws-secrets-manager/

Arquivos necessários:
* aws-access-key ('access-key' no link acima)
* aws-secret-access-key ('secret-access-key' no link acima)