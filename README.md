# Découverte Fast-API et Github Client - Création d'une API et automatisation création d'issue github 

Ce workshop vous apprendra a crée une api avec Fast-api puis a la testé avec POSTMAN.

L'API crée vous permettra de gérer les données de la moulinette afin de crée une liste de tâches à faire pour chaque Projet EPITECH

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

Une fois l'API lancée, vous pouvez tester son bon fonctionnement de la manière suivante grâce a POSTMAN en utilisant le lien sur lequel votre api se host :

![image](https://github.com/jserygit/Workshop-API-Org/assets/145333959/f244956a-587e-4c89-99d0-63f53b459785)

Si tout se passe bien vous devriez obtenir cela :

![image](https://github.com/jserygit/Workshop-API-Org/assets/145333959/eb588c55-76b4-47e2-b12d-926ebba9a664)

## Étape 2 Récupérer les informations de la moulinette :

Pour cela il va nous falloir envoyer une requête a l'api de TekMe pour cela nous allons d'en un premier temps importé la librairie python `requests` dans notre `main.py`
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

Effectuer une requête a l'aide de postman :
![image](https://github.com/jserygit/Workshop-API-Org/assets/145333959/c11aef92-70a1-4e97-b0dc-f22396ad49ca)


## Étape 3 Traitement des données :

Il nous faut maintenant être capable de récupéré les information d'un projet.
pour cela nous allons faire en sorte de pouvoir mettre le nom d'un projet dans le path de l'api afin qu'elle nous sorte les info de celui-ci, collé le code suivant dans `main.py`:

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
    ##si oui on ecrit l'url du repo dans le fichier repo.txt
    ##on parcours les skills pour récupérer les tests qui sont pas passés
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
        ## on parcours les tests de chaque skills pour voir si ils sont tous passés
        
            ## si un test n'est pas passé, on passe la variable passed a False
            
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
