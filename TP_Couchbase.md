# EXO 1

## 1. Modelisation d'un fichier JSON 
```
{
    "harry_potter": {
        "identifiant": "harry_potter",
        "email":"hpotter@couchbase.com",
        "type": ["sorcier", "sang-mele"]
        "login" : "hpotter",
        "mdp" : "poudlard" ,
        "interests": ["Quidditch","Ginny Weasley","Horcruxe"];
    },
    "hermione_granger": {
        "identifiant": "hermione_granger",
        "email":"hgranger@couchbase.com",
        "type": ["sorcier", "moldu"]
        "login" : "hgranger",
        "mdp" : "poudlard" ,
        "interests": ["Pattenrond","Ron Weasley","Horcruxe"];
    },
    ron_weasley": {
        "identifiant": "ron_weasley",
        "email":"rweasley@couchbase.com",
        "type": ["sorcier", "sang-pur"]
        "login" : "rweasley",
        "mdp" : "poudlard" ,
        "interests": ["Quidditch","Hermione Granger","Horcruxe"];
    }
}
```

## 2. Definition de la cle du document UUID (uuid: the unique identifier for the bucket)

### a. commande CLI donc rajouter dans variable d'environnement windows 
```shell
   C:\Program Files\couchbase\server\bin, 
   C:\Program Files\couchbase\server\bin\install
   C:\Program Files\couchbase\server\bin\tools
```

### b. dans le terminal, taper
```shell
> cbstats localhost:8091 uuid 
```

## 3. creation et connexion au cluster "user"
### a. creer le bucket "user" : 2 manieres de faire
-   i.  par l'interface couchbase
-   ii. par script : https://developer.couchbase.com/documentation/server/5.0/sdk/python/managing-clusters.html
```
from couchbase.admin import Admin

adm = Admin('ctith', '******', host='localhost', port=8091)
adm.bucket_create('user')   #+ d'option sur le lien
```

### b. se connecter au cluser "user"
```
from couchbase.cluster import Cluster
from couchbase.cluster import PasswordAuthenticator

cluster = Cluster('couchbase://localhost:8091')
authenticator = PasswordAuthenticator('ctith', '******')
cluster.authenticate(authenticator)
cb = cluster.open_bucket('user')
```

### 4. Connexion au pool via la commande OpenBucket

### 5. Verifier la connexion



# EXO 2 
## 1. Ecriture d'un script/programme de stockage conditionnel du document dans Couchbase via les commandes Bucket upsert / insert / replace

### inserer utilisateur : identifiant, email, type, login, mdp, interests
```
cb.upsert('harry_potter', {'identifiant': 'harry_potter', 'email':'hpotter@couchbase.com', 'type': 'sorcier', 'login' : 'hpotter', 'mdp' : 'poudlard' , 'interests': ['Quidditch', 'Ginny Weasley', 'Horcruxe']})
cb.upsert('hermione_granger', {'identifiant': 'hermione_granger', 'email':'hgranger@couchbase.com', 'type': 'sorcier', 'login' : 'hgranger', 'mdp' : 'poudlard' , 'interests': ['Pattenrond', 'Ron Weasley']})
cb.upsert('ron_weasley', {'identifiant': 'ron_weasley', 'email':'rweasley@couchbase.com', 'type': 'sorcier', 'login' : 'rweasley', 'mdp' : 'poudlard' , 'interests': ['Quidditch', 'Hermione Granger']})

"""cb.get('harry_potter').value"""
cb.n1ql_query('CREATE PRIMARY INDEX ON bucket-name').execute()
```

### recupere les utilisateurs qui ont le quidditch pour centre d'interet => donc harry potter et ron weasley
```
from couchbase.n1ql import N1QLQuery
row_iter = cb.n1ql_query(N1QLQuery('SELECT identifiant FROM bucket-name WHERE interests ="Quidditch"'))
for row in row_iter: print row
```
