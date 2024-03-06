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
