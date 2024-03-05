# Découverte Fast-API et Github Client - Création d'une API et automatisation création d'issue github 

Ce workshop vous apprendra a crée une api avec Fast-api puis a la testé avec POSTMAN.

L'API crée vous permettra de gérer les données de la moulinette afin de créer une liste de tâches à faire pour chaque Projet EPITECH

Documentation Github Client:
https://cli.github.com/manual/

Documentation Fast-Api:
https://fastapi.tiangolo.com/tutorial/

## Étape 1 Configuration :

Il vous faudra d'abord si ce n'est pas déjà fait installer `Fast-Api` et `POSTMAN` sur votre machine:

Lien pour installer `POSTMAN` : https://www.postman.com/downloads/

``` bash
pip install "fastapi[all]"
```

Pour vérifier l'installation ajouter le script suivant dans le fichier `main.py`:

``` py
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
```

Puis exécuter la commande suivante dans votre terminal :

``` bash
uvicorn main:app --reload
```

Une fois l'API lancée, vous pouvez tester son bon fonctionnement de la manière suivante grâce à POSTMAN en utilisant le lien sur lequel votre api se host :

![image](https://github.com/jserygit/Workshop-API-Org/assets/145333959/f244956a-587e-4c89-99d0-63f53b459785)

Si tout se passe bien vous devriez obtenir cela :

![image](https://github.com/jserygit/Workshop-API-Org/assets/145333959/eb588c55-76b4-47e2-b12d-926ebba9a664)

## Étape 2 Récupérer les informations de la moulinette :

Pour cela, il va nous falloir envoyer une requête à l'api de TekMe , nous devons d'en un premier temps importé la librairie python `requests` dans notre `main.py`
Puis effectué la ligne suivante dans laquelle il vous faut remplacer `TOKEN-TEKME` par votre token que vous devrez trouver sur tekme sur la page des moulinette en faisant F12 en cliquant sur l'onglet réseau :

``` python
import requests

resultat = requests.get('https://tekme.eu/api/profile/moulinettes', headers={'Authorization': 'TOKEN-TEKME'})
```

Pour vérifier le résultat, on ajoute au path de notre api `/mouli` de la manière suivante :

```python
@app.get("/mouli")
async def root():
    return resultat.json()
```

Effectuer une requête à l'aide de postman :
![image](https://github.com/jserygit/Workshop-API-Org/assets/145333959/c11aef92-70a1-4e97-b0dc-f22396ad49ca)


## Étape 3 Traitement des données :

Il nous faut maintenant être capable de récupérer les informations d'un projet.
Pour cela, nous allons faire en sorte de pouvoir mettre le nom d'un projet dans le path de l'api afin qu'elle nous sorte les infos de celui-ci, collé le code suivant dans `main.py`:

```python
@app.get("/{item_name}")
async def read_item(item_name: str):
    passed = True
    index = 0
    os.makedirs('/tmp/mouli', exist_ok=True)
    ##création des fichiers pour les issues et le repo
    issue = open('/tmp/mouli/data.txt', "w")
    repo = open('/tmp/mouli/repo.txt', "w")

    ##on parcours jobs pour trouver le projet
    ##on regarde si le projet est celui qu'on cherche
    ##si oui on écrit l'URL du repo dans le fichier repo.txt
    ##on parcours les skills pour récupérer les tests qui ne sont pas passés
    for i in resultat.json().get('jobs'):
        if i.get('project') == item_name:
            repo.write(i.get('trace').get('githubUrl'))
            get_failed_test(issue, i, passed, index)
            issue.close()
            return 0
    return 84
```

Complété le code de `get_failed_test()` et de `write_issue()` appelé dans `get_failed_test()` :
```python
def get_failed_test(issue, i, passed, index):
    for skill in i.get('trace').get('skills'):
        ## on attribue a chaque skills un numero selon leur ordre d'apparition
        ## afin de les retrouver plus facilement
        index += 1
        ## on parcourt les tests de chaque skills pour voir si ils sont tous passés
        ## à compléter
        
            ## si un test n'est pas passé, on passe la variable passed a False
            ## à compléter

        if passed == False:
            write_issue(issue, skill, index)
        passed = True
```

```python
def write_issue(issue, skill, index):
    ## ecrit dans le fichier issue.txt le nom du skill et le numero du skill
    ## pour chaque skill qui n'a pas passé les tests
    issue.write("Skill " + str(index) + " : " + skill.get('name') + "\n")
    for test in skill.get('tests'):
        if test.get('comment') == "PASSED":
            continue
        ## ecrit dans le fichier issue.txt le nom du test et le commentaire du test
        ## dans le format suivant :
        ## "TX : Nom du test : commentaire du test"
        ## X est le numero du test
    pass
```

Faite un `cat` de `/tmp/mouli/data` pour vérifier si le parsing est correcte

Nous avons désormais notre API nous permettant de récupérer les données de la mouli
## Étape 4 Configuration de Github CLI :


Créer un Personnal Access Token depuis les `developer settings` dans les paramètres 

Lancer la commande suivante et suivait les instruction :

``` bash
gh auth login
```

une fois, cela fait prenez le temps de jeter un coup d’œil au fonctionnement de Github client.

## Étape 5 Listée les issues d'un répo :

Créer un fichier nommé `ManageIssue.sh`

`ManageIssue.sh` s’exécutera de la manière suivante :

```bash
./ManageIssue [NomDuProjet]
```

Envoyer, une requête a notre api dans `ManageIssue.sh` afin d'obtenir les données traitées plus tôt 

```sh
args=("$@")
## on récupère les différents arguments.

repo=${args[0]}
## on récupère le premier argument soit [NomDuProjet]

title=("")

i = 0

sleep 1

## on effectue une request a notre api
curl --location --request GET "http://XXX.X.X.X:XXXX/items/$repo" --header 'Content-Type: application/json'
```

On a maintenant nos données dans `/tmp/mouli/data.txt` et dans `/tmp/mouli/repo.txt`
Grace a l'URL enregistré dans `/tmp/mouli/repo.txt` vous devez a l'aide github CLI récupéré la liste des issue dans un répo. Vous devez obtenir cela:

```sh
There are no open issues in EpitechPromo20XX/B-PSU-200-MPL-2-1-minishell1-prenom.nom

```

Créer manuellement une issue dans le repo puis relancer la commande

```sh
Showing 1 of 1 open issue in EpitechPromo20XX/B-PSU-200-MPL-2-1-minishell1-prenom.nom

#68  test    less than a minute ago
```

Pour nous rendre la tache plus facile, nous allons faire en sorte de récupéré uniquement les numéros des issues sous forme de JSON grâce au flag `--json number` qu'on va venir ajouter a la commande ce qui va nous permettre d'obtenir le résultat suivant :

```JSON
[
	{
		"number": 68
	}
]
```

## Étape 6 Création d'issues pour un répo avec Github Client:

A l'aide de la commande Github CLI `gh issue create` crée une issue dans le repo de votre choix, vous devriez obtenir le message suivant :

```sh

Creating issue in EpitechPromo2028/B-PSU-200-MPL-2-1-minishell1-prenom.nom

```

## Étape 7 A vous de jouer :

Vous disposez maintenant de toutes les clés afin de pouvoir compléter le fichier `ManageIssue.sh` bien évidemment si vous préférez, vous pouvez le faire dans un autre langage 

Voici un exemple de ce que vous pouvez obtenir :

```sh
0 EpitechPromo2028/B-PSU-100-MPL-1-1-myls-julien.sery
✓ Closed issue #8 (06 - char device tests)
✓ Closed issue #7 (05 - '-t' option tests)

Creating issue in EpitechPromo2028/B-PSU-100-MPL-1-1-myls-julien.sery

https://github.com/EpitechPromo2028/B-PSU-100-MPL-1-1-myls-julien.sery/issues/9

Creating issue in EpitechPromo2028/B-PSU-100-MPL-1-1-myls-julien.sery

https://github.com/EpitechPromo2028/B-PSU-100-MPL-1-1-myls-julien.sery/issues/10
Issues posted successfully
https://github.com/EpitechPromo2028/B-PSU-100-MPL-1-1-myls-julien.sery/
```

![Pasted image 20240305155007](https://github.com/jserygit/Workshop-API-Org/assets/145333959/9c762ea2-5edf-4ebc-a1b4-04d86b0af230)

![Pasted image 20240305155040](https://github.com/jserygit/Workshop-API-Org/assets/145333959/c08ce68a-fd52-42a3-8b09-427025bcfde2)

À vous de faire le vôtre et de le personnaliser à votre guise
