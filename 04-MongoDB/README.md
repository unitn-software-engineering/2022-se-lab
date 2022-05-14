---
theme: default
_class: lead
paginate: true
backgroundColor: #fff
marp: true
backgroundImage: url('https://marp.app/assets/hero-background.svg')
header: '04 - MongoDB'
footer: 'Marco Robol - Trento, 2022 - Software Engineering'
---

# **MongoDB and Mongoose**

Software Engineering, 2nd part - Lab

#### Marco Robol - marco.robol@unitn.it

*Academic year 2021/2022 - Second semester*

---

# Plan for the second part of the course

- April 19-21: Design thinking, project arch, API
- April 26-28: Foundations JS, Node.js, git
- ***May 2-5: Agile Methodology, MongoDB, API***
- May 9 - May 22: Sprint #1
    - More on agile methodology, testing, git branching
- May 23 - June 7: Sprint #2
    - More on testing, devops/CI

---

# Contents of today class

- [MongoDB](www.mongodb.com) and [Mongoose](https://mongoosejs.com/)

> Lab teaching material: github.com/unitn-software-engineering/2022-se-lab.git

> EasyLib: github.com/unitn-software-engineering/EasyLib

---

# MongoDB - mongodb.com

> https://www.mongodb.com/en-us/what-is-mongodb

- MongoDB stores data in flexible, JSON-like documents, meaning fields can vary from document to document and data structure can be changed over time

- The document model maps to the objects in your application code, making data easy to work with

- Ad hoc queries, indexing, and real time aggregation provide powerful ways to access and analyze your data

- MongoDB is a distributed database at its core, so high availability, horizontal scaling, and geographic distribution are built in and easy to use

- MongoDB is free to use.

---

## Getting Started

> https://www.mongodb.com/docs/guides/server/introduction/

- Define Your Data Set
- Start Thinking in JSON
- Identify Candidates for Embedded Data and Model Your Data

```json
{ "name": "notebook",
  "qty": 50,
  "rating": [ { "score": 8 }, { "score": 9 } ],
  "size": { "height": 11, "width": 8.5, "unit": "in" },
  "status": "A",
  "tags": [ "college-ruled", "perforated"]
}
```

---

## Get MongoDB

- Install MongoDB locally - www.mongodb.com/try/download/community
  - Tutorial https://www.mongodb.com/docs/guides/server/install/
- Use MongoDB as a service - cloud.mongodb.com
- Develop on codesandbox.io or replit.com 

---

## MongoDB as a service - cloud.mongodb.com

- Register on cloud.mongodb.com
- Create a new project
- Build a Database (Free version)
  - Setup username and password used to connect db
- Go to Network Access -> Add IP adress -> Allow Access from Anywhere 
- Go back on 'Datbase' Click and click on 'Connect' to get connection details.

> Replace <password> with the password for the admin user. Replace myFirstDatabase with the name of the database that connections will use by default. Ensure any option params are URL encoded.

    mongodb+srv://admin:<password>@cluster0.f9mww.mongodb.net/myFirstDatabase?retryWrites=true&w=majority

---

# Mongoose mongoosejs.com

> elegant mongodb object modeling for node.js

> Mongoose provides a straight-forward, **schema-based** solution to model your application data. It includes *built-in type casting, validation, query building, business logic hooks* and more, out of the box.


---

## Get Mongoose

    $ npm install mongoose

```javascript
const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost:27017/test');

const Cat = mongoose.model('Cat', { name: String });

const kitty = new Cat({ name: 'Zildjian' });
kitty.save().then(() => console.log('meow'));
```

> https://mongoosejs.com/docs/guide.html

---

## Defining your schema

```javascript
import mongoose from 'mongoose';
const { Schema } = mongoose;

const bookSchema = new Schema({
  title:  String, // String is shorthand for {type: String}
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
});
```

---

**Ids** - By default, Mongoose adds an _id property to your schemas.

```javascript
bookSchema.path('_id'); // ObjectId { ... }
```

## Creating a model

To use our schema definition, we need to convert our **bookSchema** into a **Model** we can work with. To do so, we pass it into mongoose.model(modelName, schema):

```javascript
const BookModel = mongoose.model('Book', bookSchema);
```

When you create a new document, a new _id of type ObjectId is created.

```javascript
const doc = new BookModel();
doc._id instanceof mongoose.Types.ObjectId; // true
```

---

## Querying

> https://mongoosejs.com/docs/models.html#querying

Finding documents is easy with Mongoose, which supports the rich query syntax of MongoDB. Documents can be retrieved using a model's **find**, **findById**, **findOne**, or **where** static methods.

```javascript
BookModel.find({ size: 'small' }).where('createdDate').gt(oneYearAgo).exec(callback);
```

## Saving

> https://mongoosejs.com/docs/documents.html#updating-using-save


```javascript
const doc = await MyModel.findOne();
doc.name = 'foo';
await doc.save();
```

---

# Subdocuments versus Nested Paths

> https://mongoosejs.com/docs/subdocs.html#subdocuments-versus-nested-paths

```javascript
// Subdocument
const subdocumentSchema = new mongoose.Schema({
  child: new mongoose.Schema({ name: String, age: Number })
});
const Subdoc = mongoose.model('Subdoc', subdocumentSchema);

// Nested path
const nestedSchema = new mongoose.Schema({
  child: { name: String, age: Number }
});
const Nested = mongoose.model('Nested', nestedSchema);
```

---

# Populate

> https://mongoosejs.com/docs/populate.html

```javascript
const personSchema = Schema({
  _id: Schema.Types.ObjectId,
  name: String,
  stories: [{ type: Schema.Types.ObjectId, ref: 'Story' }]
});
const storySchema = Schema({
  author: { type: Schema.Types.ObjectId, ref: 'Person' },
  title: String,
  fans: [{ type: Schema.Types.ObjectId, ref: 'Person' }]
});
const Story = mongoose.model('Story', storySchema);
const Person = mongoose.model('Person', personSchema);

Story.findOne({ title: 'Casino Royale' })
.populate('author').exec(function (err, story) { ... });
```

---

# MongoDB with mongoose in EasyLib

> https://github.com/unitn-software-engineering/EasyLib

How to run: `npm run start_local`

#### package.json
```json
  "scripts": {
    "start": "node index.js",
    "start_local": "node -r dotenv/config index.js" }, ...
```

What is *-r dotenv/config* ? ...

---

### dotenv - www.npmjs.com/package/dotenv

    $ npm install dotenv

> Dotenv loads environment variables from a .env file into process.env.

```javascript
require('dotenv').config()
console.log(process.env) // remove this after you've confirmed it working
```

**Preload** - You can use the --require (-r) command line option to preload dotenv. By doing this, you do not need to require and load dotenv in your application code.

    $ node -r dotenv/config your_script.js

---

## Let's go back on mongoose and EasyLib

- **mongoose models**
  - app/models/
- **express routers**
  - app/

#### app/models/book.js
```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

// set up a mongoose model
module.exports = mongoose.model('Book', new Schema({ 
	title: String
}));
```

---

#### app/books.js

```javascript
const Book = require('./models/book');

router.get('', async (req, res) => {
    // https://mongoosejs.com/docs/api.html#model_Model.find
    let books = await Book.find({});
    ...
}
router.get('/:id', async (req, res) => {
    // https://mongoosejs.com/docs/api.html#model_Model.findById
    let book = await Book.findById(req.params.id);
    ...
}
router.post('', async (req, res) => {
    let book = new Book({
          title: req.body.title
    });
    book = await book.save();
    res.location("/api/v1/books/" + book.id).status(201).send();
});
```

---

#### index.js

```javascript
const app = require('./app/app.js');
const mongoose = require('mongoose');

app.locals.db = mongoose.connect(process.env.DB_URL, 
  {useNewUrlParser: true, useUnifiedTopology: true})
.then ( () => {
    console.log("Connected to Database");
    app.listen(8080, () => { console.log(`Server listening`) });
});
```

---

# Questions?

marco.robol@unitn.it

---

# Next

- Versioning and collaboration with [Git](https://git-scm.com/) and [Github.com](https://github.com/) - 2nd part

#### Versioning and collaboration with Git

- Resolving conflicts in 3-way merge - Quick recap
- Collaboration - Quick recap
- Advanced
  - stash, rebase, tag, reset, revert
- Undoing Commits & Changes
  - reset, revert

#### Git branching strategies