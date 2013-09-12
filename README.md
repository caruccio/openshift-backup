openshift-backup
================

Utilize este script para fazer backups incrementais de suas aplicações OpenShift na Getup.

Configurar
==========

Crie um token de autenticação, somente leitura, com validade de 1 ano:

```
MESES=12
rhc authorization add --scopes read --note backup --expires-in $((60*60*24*30*$MESES))
Adding authorization ... done

backup
------
  Token:      6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde
  Scopes:     read
  Created:    10:04 AM
  Expires In: about 12 months
```

Nota: o período de expiração pode variar dependendo de onde você hospede seus apps.

Copie o valor do token (`6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde`)
para o arquivo `/etc/rhc-auth-token`.

```
echo 6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde > /etc/rhc-auth-token
```

Execute o script para testar antes de colocá-lo na cron.

```
chmod +x backup-app
./backup-app --all --dir=/tmp/backup-teste
```

Instalar
========

Copie o script para algum local do PATH.

```
cp -i backup-app /usr/sbin/
chmod +x /usr/sbin/backup-app
```

Adicione o comando na cron para executar diariamente.

```
sudo su -c 'echo "/usr/sbin/backup-app --all" > /etc/cron.daily/openshift-backup-app'
sudo su -c 'chmod +x /etc/cron.daily/openshift-backup-app'
```

