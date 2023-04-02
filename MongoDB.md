# Create MongoDB database and find results for the following queries

- Running docker containner
```
docker exec -it my-mongo bash
```

- Open mongo shell
```
mongosh
```
- Authentication
```
mongosh -u myAdmin --authenticationDatabase admin –p
```

- Create database
```
use students
```

- Insert data
```
db.students.insertMany([
    {"name":"Ramesh","subject":"maths","marks":87},
    {"name":"Ramesh","subject":"english","marks":59},
    {"name":"Ramesh","subject":"science","marks":77},
    {"name":"Rav","subject":"maths","marks":62},
    {"name":"Rav","subject":"english","marks":83},
    {"name":"Rav","subject":"science","marks":71},
    {"name":"Alison","subject":"maths","marks":84},
    {"name":"Alison","subject":"english","marks":82},
    {"name":"Alison","subject":"science","marks":86},
    {"name":"Steve","subject":"maths","marks":81},
    {"name":"Steve","subject":"english","marks":89},
    {"name":"Steve","subject":"science","marks":77},
    {"name":"Jan","subject":"english","marks":0,"reason":"absent"}
])
```

1. Find the total marks for each student across all subjects.
```
db.students.aggregate([
    {$unwind: '$marks'},
    {$group: {_id: '$name', totalMarks: {$sum: '$marks'}}}
])
```
- result
```
[
  { _id: 'Rav', totalMarks: 216 },
  { _id: 'Alison', totalMarks: 252 },
  { _id: 'Steve', totalMarks: 247 },
  { _id: 'Jan', totalMarks: 0 },
  { _id: 'Ramesh', totalMarks: 223 }
]
```

2. Find the maximum marks scored in each subject.
```
db.students.aggregate([
    {$unwind: '$marks'},
    {$group: {_id: '$subject', maxMarks: {$max: '$marks'}}}
])
```

- result
```
[
  { _id: 'maths', maxMarks: 87 },
  { _id: 'science', maxMarks: 86 },
  { _id: 'english', maxMarks: 89 }
]
```

3. Find the minimum marks scored by each student.
```
db.students.aggregate([
    {$unwind: '$marks'},
    {$group: {_id: '$name', minMarks: {$min: '$marks'}}}
])
```

- result
```
[
  { _id: 'Rav', minMarks: 62 },
  { _id: 'Alison', minMarks: 82 },
  { _id: 'Steve', minMarks: 77 },
  { _id: 'Jan', minMarks: 0 },
  { _id: 'Ramesh', minMarks: 59 }
]
```

4. Find the top two subjects based on average marks.
```
students> db.students.aggregate([
    {$unwind: '$marks'},
    {$group: {_id: '$subject', avgMarks: {$avg: '$marks'}}},
    {$sort: {avgMarks: -1}},
    {$limit: 2}
])
```
- result
```
[
  { _id: 'maths', avgMarks: 78.5 },
  { _id: 'science', avgMarks: 77.75 }
]
```
