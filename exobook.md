Exercice Book

1.  db.employees.find()
2.  db.employees.find({ age: { $gt: 33 } })
3.  db.employees.find().sort({ salary: -1 })
4.  db.employees.find({}, { \_id: 0, name: 1, job: 1 })
5.  db.employees.aggregate([ { $group: { _id: "$job", totalEmployees: { $sum: 1 } } }])
6.  db.employees.updateMany({ job: "Developer" }, { $set: { salary: 80000 } }
