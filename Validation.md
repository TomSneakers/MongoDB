Exercice 1

db.createCollection("salles", {
validator: {
$jsonSchema: {
bsonType: "object",
required: ["nom", "capacite", "adresse"],
properties: {
nom: {
bsonType: "string"
},
capacite: {
bsonType: "int"
},
adresse: {
bsonType: "object",
required: ["codePostal", "ville"],
properties: {
codePostal: {
bsonType: "string"
},
ville: {
bsonType: "string"
}
}
}
}
}
}
})

db.salles.insertOne({"nom": "Super salle", "capacite": 1500, "adresse": {"ville": "Musiqueville", "codePostal": "12345"}})

Exercice 2

db.runCommand({
collMod: "salles",
validator: {
$jsonSchema: {
bsonType: "object",
required: ["nom", "capacite", "adresse"],
properties: {
nom: {
bsonType: "string"
},
capacite: {
bsonType: "int"
},
adresse: {
bsonType: "object",
required: ["codePostal", "ville"],
properties: {
codePostal: {
bsonType: "string"
},
ville: {
bsonType: "string"
}
}
},
\_id: {
anyOf: [
{ bsonType: "int" },
{ bsonType: "objectId" }
]
}
}
}
}
})

Pour supprimer les critères de validation ajoutés:

db.runCommand({
collMod: "salles",
validator: {}
})

Exercice 3:


db.runCommand({
   collMod: "salles",
   validator: {
      $jsonSchema: {
         bsonType: "object",
         required: ["nom", "capacite", "adresse"],
         properties: {
            nom: {
               bsonType: "string"
            },
            capacite: {
               bsonType: "int"
            },
            adresse: {
               bsonType: "object",
               required: ["codePostal", "ville"],
               properties: {
                  codePostal: {
                     bsonType: "string"
                  },
                  ville: {
                     bsonType: "string"
                  }
               }
            },
            _id: {
               anyOf: [
                  { bsonType: "int" },
                  { bsonType: "objectId" }
               ]
            },
            styles: {
               bsonType: "array",
               items: {
                  bsonType: "string",
                  enum: ["jazz", "soul", "funk", "blues"]
               }
            },
            smac: {
               bsonType: "bool"
            }
         }
      }
   }
})


db.salles.update({"_id": 3}, {$set: {"smac": true}})
