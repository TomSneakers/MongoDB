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
