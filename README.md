
# Injection SQL 

Ce guie a pour but la détection, la mise en place et l'exploitation d'une failel de type injection SQL **Ce guide n'existe que dans un but purement éducatif**

---

## Prerequisites

1. **Docker Installé**: Veuilelz vérifiez que docker est installé sur votre système
   - Check Docker version:
     ```bash
     docker --version
     ```

2. **Connaissance SQL basique**: Comprendre le fonctionnement d'une requête SQL classique

---

## Mettre en place l'application
### Etape 1 : Lancer l'environnement
``` yaml
curl https://hackropole.fr/challenges/fcsc2022-web-chatpristi/docker-compose.public.yml -o docker-compose.yml
```
Puis lancer le docker
``` yaml
docker compose up
```
Enfin, accèder à la page
http://localhost:8000/

### Etape 2 : Tester la présence de la faille
On cherche un endroit où une potentielle faille pourrait exister, ici on essaye dans la barre de recherche en rentrant une première injection du style :

   ```bash
   ' OR 1=1 --
   ```

On obtient ainsi  : 

   ```bash
   pq: syntax error at end of inputpq: syntax error at end of input
   ```
On comprend aisément plusieurs choses, une faille existe, sinon nous n'aurions pas eu un tel retour, l'application tourne avec une base PostGreSQL et il y a un problème de syntaxe dans la requête exécutée, bien exploitée elle pourrait donc nous donner bien des entrées...

### Etape 3 : Exploitation de la faille

En procédant par élimination :

  *' est nécessaire,
  *OR 1=1 est l’essence même de l’injection ici,
  *-- est la mise en commentaire.

Il manque donc quelque chose pour rendre l’injection viable, on essaye d’ajouter une parenthèse fermante pour bien finir l’instruction de base et ainsi :

   ```bash
   ') OR 1=1 --
   ```
On voit ainsi un nouveau meme apparaitre et on trouve le flag !!! (à recopier à la main…)

   ```bash
   FCSC{1D32671928B0DFD5E0B92B57871B1D49}
   ```

# Impact

Ce genre de vulnérabilité ici dans un contexte amusant montre de grave faille. Une simpel commande de base permet de vérifier l'existence de la faille et une petite accomodation peut donner accès à des parties de la base de données qui n'était pas faite pour être visible et donc potentiellement donner une grave fuite de donnée dans des cas majeurs.

# Mitigation
Bien que potentiellement grave, les failles par injection SQL sont extrêmement basique et connu, la plus part des bases de données dans leurs versions modernes contiennent des façons de limiter voir annuler leurs impacts que csoit au travers des requêtes préparés, l'evitement de concaténation, la limitation de permission, ect.

---

**Disclaimer**: Ce guide a été fait dans un environnement controlé et dans un but purement éducatif. Il est obligatoire de ne faire ce genre d'essai qu'avec l'autorisation de l'ensemble des parties prenantes
