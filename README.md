openshift-backup
================


* English version below

----------

Utilize este script para fazer backups incrementais de suas aplicações OpenShift na Getup.

Baixa o snapshot de uma ou mais aplicações para um diretório raiz local. Cada aplicação é guardada
em seu sub-diretório como um repositóio git.

Por exemplo, se voce possui duas aplicações, `blog` e `site`, o comando `backup-app --all` guarda o
snapshot de ambas nos diretórios `/var/backup/openshift/blog` e `/var/backup/openshift/site`. Como
cada um desses diretório é na verdade um repositório git, fica simples pular entre versões usando um
simples `git checkout COMMMIT`!

Leia também o artigo [Backup incremental para sua aplicação](http://getupcloud.com/blog/backup-incremental-para-sua-aplicacao).

Configurar
----------

Crie um token de autenticação, somente leitura, com validade de 1 ano:

```
$ MESES=12
$ rhc authorization add --scopes read --note backup --expires-in $((60*60*24*30*$MESES))
Adding authorization ... done

backup
------
  Token:      6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde
  Scopes:     read
  Created:    10:04 AM
  Expires In: about 12 months
```

Nota: O período de expiração pode variar dependendo de onde você hospede seus apps. Fique atento
para o campo `Expires In` pois o token deve ser trocado antes do término do período.

Copie o valor do token (`6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde`)
para o arquivo `/etc/rhc-auth-token`.

```
$ echo 6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde > /etc/rhc-auth-token
```

Execute o script para testar antes de colocá-lo na cron.

```
$ chmod +x backup-app
$ ./backup-app --all --dir=/tmp/backup-teste
```

Veja se foi criado o diretório `/tmp/backup-teste` com um ou mais apps.

Instalar
--------

Copie o script para algum local do PATH.

```
$ sudo cp -i backup-app /usr/sbin/
$ sudo chmod +x /usr/sbin/backup-app
```

Adicione o comando na cron para executar diariamente.
Tenha em mente que operações de snapshot irão parar sua aplicação, baixá-la e por fim iniciá-la novamente. Durante este período sua aplicação estará inacessível.

```
$ sudo su -c 'echo "/usr/sbin/backup-app --all" > /etc/cron.daily/openshift-backup-app'
$ sudo su -c 'chmod +x /etc/cron.daily/openshift-backup-app'
```

-----------------------------------------------------------------------------------------------------

English
=======

Use this script to do incremental backups of your OpenShift apps.

It downloads snapshots of one or more apps to a local root dir. Each ap is stored inside it's own sub-dir as a git repo.

For example, if you have 2 apps, `blog` and `site`, then command `backup-app --all` saves both snapshots bellow dirs `/var/backup/openshift/blog` and `/var/backup/openshift/site`. Each of those dirs are in fact git repositories, thus it is really simple to jump between versions using a simple `git checkout COMMMIT`!

Configure
---------

Create an rad-only auth token, valid or 1 year. 

```
$ MONTHS=12
$ rhc authorization add --scopes read --note backup --expires-in $((60*60*24*30*$MONTHS))
Adding authorization ... done

backup
------
  Token:      6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde
  Scopes:     read
  Created:    10:04 AM
  Expires In: about 12 months
```

NOTE: This expiration period is valid for [Getup Cloud](http://getupcloud.com)'s OpenShift and may be shorter depending on which PaaS provider you subscribe. Also, pay attention to field `Expires In` because token must be recreated before this date.

Copy your oke value (`6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde`) to file `/etc/rhc-auth-token`.

```
$ echo 6a73b511f426af1aa2834470469f0a0f776b11b888f483e81f8acacc7dc02cde > /etc/rhc-auth-token
```

Test the script before adding it to cron.

```
$ chmod +x backup-app
$ ./backup-app --all --dir=/tmp/backup-teste
```

Check if dir `/tmp/backup-teste` was created with one or more apps, one per dir.

Install
-------

Copy the script some on PATH.

```
$ sudo cp -i backup-app /usr/sbin/
$ sudo chmod +x /usr/sbin/backup-app
```

Add following command to cron to execute on a daily base.
Keep in mind that snapshot operations will stop your app, download it and then start it again. On this period your app will be unavailable.

```
$ sudo su -c 'echo "/usr/sbin/backup-app --all" > /etc/cron.daily/openshift-backup-app'
$ sudo su -c 'chmod +x /etc/cron.daily/openshift-backup-app'
```

