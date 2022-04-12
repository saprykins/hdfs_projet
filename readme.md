# Projet Hadoop  
## Etapes  

Créer le fichier “les_articles.csv”

Créer le répertoire “projet_les_articles”
```
hdfs dfs -mkdir -p /education/cs_2022_spring_1/$USER/projet_les_articles
```
Telecharger le fichier “les_articles.csv” dans le repertoire “projet_les_articles”
```
hdfs dfs -put les_articles.csv /education/cs_2022_spring_1/$USER/projet_les_articles
```
Vérifier si le fichier a ete sauvegarde:
```
hdfs dfs -head /education/cs_2022_spring_1/$USER/projet_les_articles/les_articles.csv
```
Utiliser “hive”
```
hive
```
Créer le tableau externe:
```
CREATE EXTERNAL TABLE IF NOT EXISTS cs_2022_spring_1.les_articles_ext(
  pdf_link STRING,
  title STRING,
  published_on_date STRING,
  summary INT,
  comment STRING,
  journal_ref STRING,
  authors INT
)
COMMENT 'les articles about AI'
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
STORED AS TEXTFILE
LOCATION '/education/cs_2022_spring_1/s.saprykin-cs/projet_les_articles/'
TBLPROPERTIES ('skip.header.line.count'='1');
```
Vérifier que le tableau externe n’est pas vide: 
```
select * from cs_2022_spring_1.les_articles_ext limit 10;
```
Créer le tableau ORC:
```
CREATE TABLE IF NOT EXISTS cs_2022_spring_1.les_articles (
  pdf_link STRING,
  title STRING,
  published_on_date STRING,
  summary INT,
  comment STRING,
  journal_ref STRING,
  authors INT
)
STORED AS ORC;
```
Vérifier que le tableau ORC a ete cree:
```
select * from cs_2022_spring_1.les_articles;
```
Transferer les donnees du fichier externe au tableau ORC:
```
INSERT OVERWRITE TABLE cs_2022_spring_1.les_articles
SELECT
  pdf_link,
  title,
  published_on_date,
  summary,
  comment,
  journal_ref,
  authors
FROM cs_2022_spring_1.les_articles_ext;
```
Vérifier que le tableau ORC n’est pas vide:
```
select * from cs_2022_spring_1.les_articles;
```
Vérifier les commandes:
```
SELECT count(*) AS nb
FROM cs_2022_spring_1.les_articles
WHERE les_articles.authors > 2;
```
Configurations de kerberos:
```
sudo apt-get install gcc python-dev libkrb5-dev
```
```
pip install pywinrm[kerberos]
```
```
pip install request_kerberos
```

Créer le fichier kerberos:
```
touch /etc/krb5.conf
```
Mettre dans ce fichier le texte suivant:
```
[libdefaults]
 default_ccache_name = FILE:/tmp/krb5cc_%{uid}
 default_realm = AU.ADALTAS.CLOUD
 dns_lookup_realm = false
 dns_lookup_kdc = true
 rdns = false
 ticket_lifetime = 24h
 forwardable = true
 udp_preference_limit = 0

[realms]
 AU.ADALTAS.CLOUD = {
  kdc = ipa1.au.adaltas.cloud:88
  master_kdc = ipa1.au.adaltas.cloud:88
  admin_server = ipa1.au.adaltas.cloud:749
  default_domain = au.adaltas.cloud
 }

[domain_realm]
 .au.adaltas.cloud = AU.ADALTAS.CLOUD
 au.adaltas.cloud = AU.ADALTAS.CLOUD
 ipa1.au.adaltas.cloud = AU.ADALTAS.CLOUD
```
Configurer kerberos:
```
sudo apt install krb5-user
```
```
kinit s.saprykin-cs
```
Mettre le mot de passe
```
***
```
Mettre la commande suivante dans la console: 
```
klist
```
Dans le navigateur Firefox sur la page “About:config” les configurations suivantes:
```
network.negotiate-auth.delegation-uris = .au.adaltas.cloud
network.negotiate-auth.trusted-uris = .au.adaltas.cloud
```