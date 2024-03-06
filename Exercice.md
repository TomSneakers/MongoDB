Exercice Book

1.  db.employees.find()
2.  db.employees.find({ age: { $gt: 33 } })
3.  db.employees.find().sort({ salary: -1 })
4.  db.employees.find({}, { \_id: 0, name: 1, job: 1 })
5.  db.employees.aggregate([ { $group: { _id: "$job", totalEmployees: { $sum: 1 } } }])
6.  db.employees.updateMany({ job: "Developer" }, { $set: { salary: 80000 } }

EXERCICE EXO

1.  db.salles.find( { smac: true }, { \_id: 1, nom: 1 })
2.  db.salles.find({ capacite: { $gt: 1000 } }, { \_id: 0, nom: 1 })
3.  db.salles.find({ "adresse.numero": { $exists: false } }, { \_id: 1 })
4.  db.salles.find({ avis: { $size: 1 } }, { \_id: 1, nom: 1 })
5.  db.salles.find( { avis: { $size: 1 } }, { \_id: 1, nom: 1 })
6.  db.salles.find( { styles: "blues" }, { \_id: 0, styles: 1 })
7.  db.salles.find( { $expr: { $eq: [{ $arrayElemAt: ["$styles", 0] }, "blues"] } }, { \_id: 0, styles: 1 })
8.  db.salles.find( { "adresse.codePostal": /^84/, capacite: { $lt: 500 } }, { \_id: 0, "adresse.ville": 1 })
9.  db.salles.find( { "avis.note": { $gte: 8, $lte: 10 } }, { \_id: 0, nom: 1 })
10. db.salles.find( { "avis.date": { $gt: new Date("2019-11-15") } }, { \_id: 0, nom: 1 })
11. db.salles.find( { $expr: { $gt: [{ $multiply: ["$\_id", 100] }, "$capacite"] } }, { \_id: 0, nom: 1, capacite: 1 })
12. db.salles.find({ smac: true, $where: "this.styles.length > 2"},{ \_id: 0, nom: 1 })
13. db.salles.distinct("adresse.codePostal")
14. db.salles.updateMany({}, { $inc: { capacite: 100 } })
15. db.salles.updateMany({ styles: { $exists: false } }, { $set: { styles: ["jazz"] } })
16. db.salles.updateMany( { \_id: { $nin: [2, 3] } }, { $pull: { styles: "funk" } })
17. db.salles.updateOne( { \_id: 3 }, { $addToSet: { styles: { $each: ["techno", "reggae"] } } })
18. db.salles.updateMany( { nom: { $regex: /^P/i } }, { $inc: { capacite: 150 }, $set: { contact: { telephone: "04 11 94 00 10" } } })
19. db.salles.updateMany( { nom: { $regex: /^[aeiou]/i } }, { $push: { avis: { date: new Date(), note: 10 } } })
20. db.salles.updateMany( { nom: { $regex: /^[zZ]/ } }, { $set: { nom: "Pub Z", capacite: 50, smac: false } }, { upsert: true })
21. db.salles.find({ \_id: { $type: "objectId" } }).count()
22. db.salles.find({ \_id: { $not: { $type: "objectId" } } }) .sort({ capacite: -1 }) .limit(1)
23. db.salles.replaceOne( { nom: "Pub Z" }, { nom: "Pub Z", capacite: 60 })
24. db.salles.deleteOne({ \_id: { $type: "objectId" }, capacite: { $lte: 60 }})
25. db.salles.findOneAndUpdate( { "adresse.ville": "Nîmes" }, { $inc: { capacite: -15 } }, { returnDocument: "after" })

EXERCICE GEO 1
var KilometresEnRadians = function(kilometres){
var rayonTerrestreEnKm = 6371;
return kilometres / rayonTerrestreEnKm;
};

var salle = db.salles.findOne({"adresse.ville": "Nîmes"});

var rayonEnRadians = KilometresEnRadians(60);

var requete = {
"adresse.localisation": {
$geoWithin: {
$centerSphere: [salle.adresse.localisation.coordinates, rayonEnRadians]
}
},
"styles": { $all: ["blues", "soul"] }
};

db.salles.find(requete);

EXERCICE GEO 2
var marseille = {"type": "Point", "coordinates": [43.300000, 5.400000]}

var rayonEnRadians = KilometresEnRadians(100);

var requete = {
"adresse.localisation": {
$geoWithin: {
$centerSphere: [marseille.coordinates, rayonEnRadians]
}
}
};

db.salles.find(requete, {"adresse.ville": 1}).sort({
"adresse.localisation": 1
});

EXERCICE GEO 3
var polygone = {
"type": "Polygon",
"coordinates": [
[
[43.94899, 4.80908],
[43.95292, 4.80929],
[43.95174, 4.8056],
[43.94899, 4.80908]
]
]
};

var requete = {
"adresse.localisation": {
"$geoWithin": {
            "$geometry": polygone
}
}
};

db.salles.find(requete, { "nom": 1, "\_id": 0 });

//////////////////////////
//////////////////////////
////////AGRÉGATION////////
//////////////////////////
//////////////////////////

EXERCICE AGREGATION 1
var pipeline = [
{
$match: {
            capacite: { $gt: 50 } // Filtrer les salles avec une capacité supérieure à 50
        }
    },
    {
        $project: {
            nom_ville: "$adresse.ville", // Sélectionner le nom de la ville
grande: { $gt: ["$capacite", 1000] } // Booléen : true si la capacité est supérieure à 1000, sinon false
}
}
];

db.salles.aggregate(pipeline);

EXERCICE AGREGATION 2
var pipeline = [
{
$project: {
            nom: 1, // Afficher le nom de la salle
            avant_extension: "$capacite", // Capacité originelle
apres_extension: { $add: ["$capacite", 100] } // Capacité après extension
}
}
];

db.salles.aggregate(pipeline);

EXERCICE AGREGATION 3
var pipeline = [
{
$group: {
            _id: { $substrBytes: ["$adresse.codePostal", 0, 2] }, // Extraction du numéro de département
capacite_totale: { $sum: "$capacite" } // Calcul de la capacité totale pour chaque département
}
}
];

db.salles.aggregate(pipeline);

EXERCICE AGREGATION 4
var pipeline = [
    {
        $unwind: "$styles" // Dérouler le tableau des styles
    },
    {
        $group: {
            _id: "$styles", // Grouper par style musical
            nombre_salles: { $sum: 1 } // Compter le nombre de salles pour chaque style
        }
    },
    {
        $sort: { _id: 1 } // Trier par ordre alphabétique des styles
    }
];

db.salles.aggregate(pipeline, { collation: { locale: 'fr', caseLevel: false } });


EXERCICE AGREGATION 5

var pipeline = [
{
$bucket: {
            groupBy: "$capacite", // Champ sur lequel les buckets sont créés
boundaries: [100, 500, 5000], // Limites des buckets
default: "other", // Bucket par défaut pour les valeurs en dehors des limites
output: {
count: { $sum: 1 } // Compter le nombre de salles dans chaque bucket
}
}
}
];

db.salles.aggregate(pipeline);

EXERCICE AGREGATION 6
var pipeline = [
{
$unwind: "$avis" // Dérouler le tableau des avis
},
{
$match: {
"avis.note": 10 // Filtrer les avis avec une note de 10
}
},
{
$group: {
_id: "$nom", // Grouper par nom de salle
avis_excellents: { $push: "$avis" } // Construire un tableau d'avis excellents
}
}
];

db.salles.aggregate(pipeline);
