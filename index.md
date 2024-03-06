Exercice 1

Pour creer un index:

db.salles.createIndex({"capacite": 1, "adresse.codePostal": 1})

Pour détruire un index:

db.salles.dropIndex({"capacite": 1, "adresse.codePostal": 1})
