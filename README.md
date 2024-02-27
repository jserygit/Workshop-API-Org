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
