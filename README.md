# Découverte Fast-API et Github Client - Création d'une API et automatisation création d'issue github 

Ce workshop vous apprendra a crée une api avec Fast-api puis a la testé avec POSTMAN.

L'API crée vous permettra de géré les données de la moulinette afin de crée une liste de tache a faire pour chaque Projet EPITECH

Documentation Github Client:
https://cli.github.com/manual/

Documentation Fast-Api:
https://fastapi.tiangolo.com/tutorial/

## Étape 1 Configuration:

Il vous faudra d'abord si ce n'est pas deja fait installé `Fast-Api` et `POSTMAN` sur votre machine:

Lien pour installé `POSTMAN` : https://www.postman.com/downloads/

``` bash
pip install "fastapi[all]"
```

pour verifié l'instalation ajouter le script suivant dans le fichier `main.py`:

``` py
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
```

puis executé la commande suivante dans votre terminal:

``` bash
uvicorn main:app --reload
```

une fois l'API lancé vous pouvez tester son bon fonctionnement de la maniere suivante grace a POSTMAN en utilisant le lien sur le quelle votre api se host :

![image](https://github.com/jserygit/Workshop-API-Org/assets/145333959/f244956a-587e-4c89-99d0-63f53b459785)

si tout ce passe bien vous devriez obtenir cela:

![image](https://github.com/jserygit/Workshop-API-Org/assets/145333959/eb588c55-76b4-47e2-b12d-926ebba9a664)

## Étape 2 Récuperé les information de la moulinette:

Pour cela il va nous falloir envoyé une requete a l'api de TekMe pour cela nous allons d'en un premier temps importé la librairie python `requests` dans notre `main.py`
puis effectué la ligne suivante dans la quelle il vous faut remplacé `TOKEN-TEKME` par votre token que vous devrez trouvé sur tekme sur la page des moulinette en faisant F12 en cliquant sur l'onglet réseau :

``` python
import requests

resultat = requests.get('https://tekme.eu/api/profile/moulinettes', headers={'Authorization': 'TOKEN-TEKME'})
```

pour verifier le resultat on ajoute au path de notre api `/mouli`







