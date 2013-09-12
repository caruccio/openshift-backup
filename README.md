openshift-backup
================

Utilize este script para fazer backups incrementais de suas aplicações OpenShift na Getup.

Baixa o snapshot de uma ou mais aplicações para um diretório raiz local. Cada aplicação é guardada
em seu sub-diretório como um repositóio git.

Por exemplo, se voce possui duas aplicações, `blog` e `site`, o comando `backup-app --all` guarda o
snapshot de ambas nos diretórios `/var/backup/openshift/blog` e `/var/backup/openshift/site`. Como
cada um desses diretório é na verdade um repositório git, fica simples pular entre versões usando um
simples `git checkout COMMMIT`!

Leia também o artigo [Backup incremental para sua aplicação](http://getupcloud.com/blog/backup-incremental-para-sua-aplicacao).

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

Nota: O período de expiração pode variar dependendo de onde você hospede seus apps. Fique atento
para o campo `Expirer In` pois o token deve ser trocado antes do término do período.

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

Veja se foi criado o diretório `/tmp/backup-teste` com um ou mais apps.

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

