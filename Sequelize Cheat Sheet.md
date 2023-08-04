## What is Sequelize?
Sequelize is an ORM library that allows us to write SQL queries to a database in a more familiar language. With sequelize, we can perform CRUD actions on a SQL database using JavaScript Syntax. 
	VOCAB: ORM, CRUD, RDBMS

In an ORM, the objects we create in code are directly mapped to the records in a database. This is an OOP approach to interacting with a database. 
Remember that a class is the blueprint for creating an object - representing the possible attributes/properties the object could have. The object created is an instance of the class.
	VOCAB: OOP, instance

```js
//Class Example

class Cat {
	static id = 0;
	
	constructor(name, furColor, furLength) {
	this.catId = Cat.id++;
		this.name = name;
		this.furColor = furColor;
		this.furlength = furlength;
	}
}
//a cat instance can be created with a name, the color of their fur, and the length of their fur
//everytime the constructor is called (when a cat is made) the id increments
```

In SQL, a table represents all that data that could potentially be made. A record is an example of the data made following the table's fields.
	VOCAB: record, schema

```sql
CREATE TABLE cats (
	id INTEGER PRIMARY KEY,
	name VARCHAR(10) NOT NULL,
	fur_color VARCHAR(10) NOT NULL,
	fur_length VARCHAR(10)
);
//a cat record can be created with a name, a fur color, and a fur length
//everytime we insert a new cat record, the id incrememnts
```

In this way, a CLASS is similar to a TABLE SCHEMA and an INSTANCE is similar to a TABLE'S RECORD.
Sequelize allows us to modify the object instances created in javaScript, which then in turn modifies the SQL database where the information for that object is stored.

### What else does Sequelize do?
- Provides input sanitation. Without it, we could potentially expose our database to malicious SQL Injection. 
	VOCAB: Input Sanitation, SQL Injection

- Allows us to be more strict on what data we allow to be entered into a databse. SQL only allow us to choose a datatype. Adding JavaScript, we can determine many more factors, such as length, style, content, and more BEFORE it reaches the database.

- Lets us query data precisely and more efficiently, as well as dynamically.
- Handles managing relationships better using built in methods and structures.

## The three core components of Sequelize

### Models
A model is the blueprint for data. Literally, it is a JavaScript class, but also acts as a schema. It is basically the mid-way point between JS and SQL.
There is one class definition in javascript for classes, and there is oen model for each table in Sequelize. A model will contain the properties that we want to put in our database, with additional information about how we want the data structured before insertion. 
	*The model is the blueprint for the JS object we make in Sequelize, which then gets turned into a record and saved in the database*
A model will also come with class methods for working with relationships to other models, and for querying - automatically created by Sequelize.
*a model has a class name, propertie/attributes, and methods*

### Migrations
A migration is a single change to a database's table/schema. Migrations are run for whatever we want to change in a table. This includes the initial creation of a table, adding a column to a table and removing a column from a table.

The migration *performs* the change to the database, but remember that the model is still the blueprint. The model must match the patterns found in a migration,
*if we create a migration to chamge a schema, we must update the model to match*

Think of migrations as commits. They can be run to the most current migration, or rolled back to a specific point.
*migrations create or alter a table in the database*

### Seeds
A seed is a file that populates a database with sample data. 
*seeds insert data that already exists, they are not for inserting new data*
They are mostly used for testing purposes, or for adding information to an otherwise emtpy database.

Seed data also needs to match the model or migration structure

## Using Sequelize in an Express app
To use Sequelize with express, we need to install the following libraries:
- sqlite3
- sequelize
- sequelize-cli
	These are in addition to the three needed for express
	
These will be installed into the server folder of your applciation
```terminal
cd server

npm install dotenv
npm install -D dotenv-cli //install as development dependancy
npm install express
npm install express-async-errors

npm install -D sqlite3 //install as development dependancy
npm install sequelize
npm install sequelize-cli
```

After they are all installed, we can set up the default file structure for sequlize automatically
*The following is an example without using an RC file for demonstration only*

```
npx sequelize init
```

This will bring the file structure from this:
![[before-init.png]]
to this:
![[after-init.png]]

Notice above how the directories for `models`, `migrations`, and `seeders` are all within the server level directory. We can utilize a `.sequelizerc` file before runing the init command to tell sequelize how to structure the folders into a specific location

in the server level, create a file called `.sequelizerc`
```
const path = require("path"); //library that manages absolute paths

module.exports = {
  config: path.resolve("config", "database.js"), 
  //loads the config into the config folder into a database.js file
  
  "models-path": path.resolve("db", "models"),
  "seeders-path": path.resolve("db", "seeders"),
  "migrations-path": path.resolve("db", "migrations"),
};
```

Each line in the file tells sequelize where to create the directory for each folder (the asbolute path to this location, plus a folder name, plus a file/folder name)

If we have this folder in our server level directory to begin with, when we run the init command, our  file structure will go from this:
![[before-init-rc.png]]

to this:
![[after-init-rc.png]]

## Exploring File Structure
Now that we have our file strcture set up, lets look at what each section in our server directory is responsible for.

### server/config
The `server/config/database.js` directory/file is responsible for managing the environments that we will be using the application with.
- development for creating the app
- test for testing and debugging the app
- production for client end use
Each environemnt will have different credentials, log-in info, and database paths.
We will be working mostly with development as an environment.

The contents of the file will be exported out as a module:
```js
module.exports = {     //export the object
  development: {       //working in a development environment
    storage: process.env.DB_FILE,     
    //the location of our database, with a path currently stored in our dotenv file
    
    dialect: "sqlite",     //the type of database we will be connecting to
    seederStorage: "sequelize",     
    //lets sequelize keep trackof which seed files have and have not been run 
    //(data does not need to be seeded twice)
    
    benchmark: true,     //logs how long it takes to complete a query
    logQueryParameters: true,     //logs the values used as parameters in queries
    typeValidation: true,     
    //prevent data from being inserted if it does not pass model validations
    
    // logging: false
    //optionally allows sequelize to console.log each query made
  }
};
```

### server/db
This directory contains the folders that hold the models, migrations, and seeders directories. Each new file made will go into their respective place.

### server/.env.example && server/.env
Remeber that we are using the dotenv package for our project. Dotenv allows environment variables to be read from a file, rather than typing them out one by one into the terminal.
We will need a dotenv `.env` file to hold our environment variables, which includes the path to our database.
```
.env

DB_FILE=db/dev.db
```

The `.env.example` file exists for respository purposes. Since a development is unique to the user, we don't want to upload our behind-the-scenes log in information. The example file exists to demonstrate what enviroment variables need to be used for the app, and how to fill them out.

We can quickly copy over the contents of the example file into a new `.env` using the terminal command:
```termial
cp .env.example .env //copy from <example> into <new file env>
```

### server/app.js
Our express application, along with all related directories, will also go into this folder such as (routes)

### server/package.json && sever/package-lock.json
Keeps track of all the dependencies that need to be used in this specific directory when we run npm install

## More on Models
The model is the JavaScript class representation of a table's .schema.
Since it is a class, it can have defined data types as table properties, and methods that can be assigned to the instance or the class.
*When defining dataTypes in a model, they will be prepended by `DataTypes`*
There will only ever be a single model for any schema. Any changes made outside the model neeed to be made to the model file as well.
*One model - Many migrations*
```js
const { Model } = require('sequelize');
module.exports = (sequelize, DataTypes) => {
  class User extends Model {
    // Helper method for defining associations.
    static associate(models) {
      // define association here
    }
	function() {
		// define functions for the class or instance
	}
  };
  User.init({
    // Model attributes are defined here
    firstName: {
      type: DataTypes.STRING, //use DataTypes.___ in the model
      allowNull: false, 
      // we can place any database level constraints in the base of the object
      // if it can go in the migration, it can go here
      validate: {
	      notNull: {
		      msg: 'Please enter a value' 
		      // we can set a custom error message if a validation does not pass
	      }
      } 
      // any custom validations for a single data type go in its definition
      // this is where non constraints definitions can go
      // 
    },
    lastName: {
      type: DataTypes.STRING
      // allowNull defaults to true
    }
  }, {
    // Other model options go here
    sequelize, // We need to pass the connection instance
    modelName: 'User', // We need to choose the model name
    validate: {} // model level validation
  });

  return User;
};
```

To use a model in any other setting, we need to import it from the path leading to the models directory.
`const {ThisModel} = require('relative/path/to/models');`
To create a new model definition, run the command
`npx sequelize model:generate --name ModelNameSingular --attributes name:type,name2:type`
Creating a model this way will automatically create a migration for the same table with the same attributes
*the model created will be referred to as its javascript class - Model*
*the table in the database will be referred to as the table name in capitalCase and plural - Models*
*We will not be using sql underscore syntax with sequelize - WE DON'T WRITE IN SQL, hence the point of the ORM*

### Model Validations
A validation has more power and control than a contraints. An insrtance of the model will not be created unless it pases the validartiuons. This means that no data will be inserted into the databse if everything isn't perfect to begin with.
We can use the same migration constraints for the datatype definitions in a model.
We put any custom or built in validators insiode a validation object in the datatype definition
```js
//object of data types
firstName: {
	type: DataTypes.STRING(maxLength),
	allowNull: false,
	unique: true,
	validate: {
		isAlpha: {
			msg: "Please enter only alphabetic characters" //error message when thrown
		},
		isCapitalized(value) {
			if(value[0] !== value[0].toUpperCase) throw new Error('First name must be capitalized'); //throws error with message
		}
	}
},
//object of model options
{
	validate: {
	//a model level validator comes in the options object AFTER all definitions, and can check the entire object against itself before finalizing
		isNotLastName() {
			if(this.firstName === this.lastName) throw new Error('First and last name cannot be the same'); //refer to other data in the instance using `this.property`
	}
}
```

It is good to use both validations and constraints. Validations should be the first level of defense, and contraints should be the final level.

## More on Migrations
A migration is a single change made to a table. Keep in mind that creating a table is a change as well, so we run migrations to make tables, add columns, change columns, remove columns, and removes tables.
An entire table can be made in a single migration, or it could be made piece by piece with several migrations.
Any time we create a migration that changes a table, we need to go into the model for that table and update the entire file to match any changes being made.
*One model - Many migrations*
Migrations can be run or rolled back, so there will always be two different functions in a migrations:
up and down.
Anything we do in up needs to have the opposite done in down.
Add a table in up, remove a table in down.
Add a column in up, remove a column in down

Migrations are essentially version control for a database. We can implement and revert changes as we create code and test features, just lkke we can do in git, This way we don;t have to drop entire tables to update or remove features. Migrations are timestamped so we know when something was made or changed. They also allow collaboration between other programmers so that everyone is working with the mosty up yto date version of schemas.

In production, we can create a migration to add on top of user data without effecting any old data. If there are bugs in the new release, we can just revert the migration and return to a state that was already bug free.
- Test in development/test environment
- rollout to production

To create a migration, run the command
`npx sequelize model:generate --name SingularName`
unlike a model, a migration only needs a name
*a model is the blueprint, it needs all the data it will contain*
*a migration is a change, we might not be adding any data types*

A migration will have up and down portions
The up happens when we run the migration, the down happens when we undo it

```js
//create a table in the up
await queryInterface.createTable('TableNamePlural', objectOf DataTypes);
await queryInterface.createTable('LowRankWyverns', {
	name: Sequelize.STRING,
	//database level constraints are defined in the migration, prepended by Sequelize
	//they can be simple one liners (above)
	//or we can add more constraints to a single field (below)
	monsterType: {
		type: Sequelize.STRING,
		allowNull: false,
	}
});

//remove a table in the down
await queryInterface.dropTable('TableNamePlural');
await queryInterface.dropTable('LowRankWyverns');
//to drop a table, we only need the table name

//add a column in the up
await queryInterface.addColumn('TableNamePlural', 'columnName', {dataType});
await queryInterface.addColumn('MiningOres', 'uniqueLocale' Sequelize.STRING);

//remove a column in the down
await queryInterface.removeColumn('TableNamePlural', 'columnName');
await queryInterface.removeColumn('MiningOres', 'uniqueLocal');
```

Primary keys can be defined in the migration definition, but foreign keys will be defined differently.

```terminal
//running a migration (all of them) (also reruns migrations)
npx dotenv sequelize db:migrate

//undo the latest migration
npx dotenv sequelize db:migrate:undo

//undo all migrations
npx dotenv sequelize db:migrate:undo:all
```

## More on Seeds
A seeder file is a file that populates a table with data. Seeders also have up and down functions, so anything we add in the up of a seeder needs to be removed in the down.

Seeding happens only acter all models are created and migrations are run. Since seeding is used to inert data based on a model into the table, it would not be possible with one of those pieces missing.

To create a seed, run the command
`npx sequelize seed:generate --name SeedName`
 Since we can create as many seeders as we'd like, it is bets to create a new seed if we want to update data, such as adding a new column to the table and filling it out with more data. 
 A seed can only be run once unless it is rolled back. This means that if we tried to add additional info to an existing seed and run it again, it would fail. We must either roll it back first and then run it again, or simply run a new seed file.

In the up portion of the seeder file is where data will be inerted into the table. Anytime we want to modify records or the table, we will use the line 
`await queryInterface.method();`

In the down portion, do the opposite method and pass in as muich of the same data as we can. This will 
- Help the program find the correct table, since we could have many and
- make sure we are modifying the correct data

```js
//inserting
await queryInterface.bulkInsert('TableNamePlural', ArrayOfObjects);
//One object for each record we are instering
await queryInterface.bulkInsert('LowRankWyverns', [
	{
		name: 'Kulu-Ya Ku',
		type: 'Bird Wyvern',
		habitat: ['Dessert Plains']
	},
	{
		name: 'Azuros',
		type: 'Fanged Beast',
		habitats: ['Ruined Shrines', 'Flooded Forest']
	}
]);

//deleting
await queryInterface.bulkDelete('TableNamePlural', ObjectWithWhereClause)
//In the bulk delete, the second argument is an object that contains where clause
//so rather than an array of objects, we pass in a field and a set of values
await queryInterface.bulkDelete('Low RankWyverns', {
	name: ['Kulu-Ya Ku', 'Azuros']
	//try to use a unique identifier so we don't delete similar records
	//delete all where name IN set of values
	//we only want to delete the records we added in the up portion
	//we dont bulk delete everything in the table because then we will lose all data from everywhere
})
```

```
//run the latest seed
npx dotenv sequelize db:seed

//run specific seed
npx dotenc sequelize db:seed --seed SeedName

//run all seeds
npx dotenv sequelize db:seed:all

//undo a seed
npx dotenv sequelize db:seed:undo

//undo specific seed
npx dotenv sequelize db:seed:undo --seed SeedName

//undo all seeds
npx dotenv sequelize db:seed:undo:all
```
*whenever we are doing something that modifies the actual database, make sure to use dotenv and db in the command*

## Retrieving data
Rather than directly using select statements, we retrieve data from thbe database by calling a method on the class/model the table relates to. In order to redference the model we are slectingf from, it needs to be imported in the file we are selecting in.

```js
const { ModelName } = require('path/to/models');

//single piece
const one = await ModelName.findOne();
const single = await ModelName.findByPk();

//An array of records/instances
const many = await ModelName.findAll();
```

We can pass an options object into all of these methods to expand the search query with where clauses and what columns to grab.

```js
//select specific columns
const arr = await ModelName.findAll({
	attributes: ['col', 'col2', 'col3'],
	where: {keys: and values}
});

const allDogs = await Dog.findAll({
	attributes: ['name', 'color'],
	where: {
		color: 'brown'
	}
});

//we can look for exact values using a direckt key-value pair
// if we want to looselty look up values, we can use the Op import

const { Op } = require('sequelize');
const allDogs = await Dog.findAll({
	attributes: ['name'],
	where: {
		name: {
			[Op.like]: 'L%'
		}
	}
});
//passing comma separated clauses will act as AND statements
//an and statement can also be explicitly passed in, with an array of objects key pairs to search
//the same can be done with an or statement

const allDogs = await Dog.FindAll({
	attributes: ['name'],
	where: {
		[Op.or]: [
			{name: {
				[Op.like]: 'L%'
			}},
			{color: 'brown'}
		]
	}
});
//WHERE name LIKE'L%' OR color=brown
//can also be passed in for two of the same attribute
where: {
	color: {
		[Op.or]: ['Brown', 'Black']
	}
}
```

## Inserting Data
To insert new data into a table (without using a seeder) we can use two different ways. One with two steps, and one with a single step.

Remember that all the data we insert into a table is first an instance of a model. That means we can first create an object with data whcich will be validated at JS level, and if it passes everything, we can put it into the database as a record. This can be done in two steps.

```js
//build an instance
// THIS IS ONLY CREATED LOCALLY IN JS
//Not saved to server
const newInstance = ModelName.build({
	property: value,
	property: value
});

const newDog = Dog.build({
	name: 'Luna',
	color: 'Brown'
});

//upload an instance to the server
//NOW it interacts with the server
//use await since it's async now
await newInstance.save();

await newDog.save();
```

Building the instance first allows us to run the validation method on the new instance to make sure it passes al the data validations and constraints before we insert it to the database.

```js
const newDog = await Dog.build({
	name: 'Cujo',
	color: 'black'
});

await nenwDog.validate();
//evaluates to a promise
//succeeds if everything passes
//rejects is something doesnt match criteria

```

If we don't want to do any additional steps in between, we can do this all in one go.

```js
await Dog.create({
	name: 'Luna',
	color: 'Brown'
});

//can also insert several at once passing in an array of objects
await Dog.bulkCreate([
	{
		name: 'Krypto',
		color: 'White'
	},
	{
		name: 'Ace',
		color: 'Black'
	}
]);
```

## Updating Already Inserted Data
To update the information in a record, we can find one specific record from the database, and then set the new properties, and then save it.

```js
const updateDog = await Dog.findByPk(primaryKey);
updateDog.property = value;
//or do multiple at once
updateDog.set({
	key: property,
	key: property
});

//save the data to the database after updating it
await updateDog.save();
```

Just like build/create, we can skip doing this in two step susing .update

```js
const updateDog = await Dog.findByPk(primaryKey);
await updateDog.update({
	key: value,
	key: value
});

//we can update multiple records at once passing in a second options object containing a where clause
// the method will be called on the model instead of the instance
await Model.update(
	{
		key: value,
		key: value
	},
	{
		where: {
			id: {
				[Op.in]: [1, 2, 3, 4, 5]
			}
		}
	}			);
```

## Deleting records
To delete a record, we only need to call the .destroy() method on the instance retrieved from the database

```js
const instance = await Model.findByPk(primary key);//call it on ther moidel
instance.destroy();
//removes it from the database

//we can delete multiple records at once using a query
//call the method on the model
//pass in a where clause
await Model.destory({
	where: {
		property: value
	}
});
//deletes all records that match criteria
```

## Foreign Key
Remember that a foreign key in SQL is a constraint that is used to reference another table's data, creating a relationship between the two.
*the foreign key exists as a primary key in one table, and is referenced as the foreign key in another*
This is similar to how a normal column is set up, but with a few more properties
```js
//In the migration
normalColumn: {
	type: Sequelize.INTEGER
},
foreignKeyColumn: {
	type: Sequelize.INTEGER,
	references: {
		model: 'TableName' //name of TABLE, not the MODEL
		key: 'id' //the data being referenced in the above table
		//similar to saying in SQL
		//key INTEGER
		//FOREIGN KEY (key) REFERENCES table(id)
	},
	onDelete: 'cascade' //remmeber that on delete cascade deletes THIS record
	//when the primary key it references is deleted
}
```

The references key only needs to be defined for the table USING the foreign key.

## Join Tables
Remeber that join tables are used to connect many-to-many relationships. 
One to many / Many to one relationships can simply be defined using a join *query* where the one is referenced as a foreign key inside the many
- think of users and tweets. We would attatch the user ON the tweet table, rather than every tweet ON the user table

But a many to many relationship, where neither table is the one in the relationship, would require a join table. To start, we must make sure that each table is already created. We cannot join two tables if one of them has not been made yet. (migrations and models)
When both tables are made, then we can make the join table.
Inside the migration and model for the join table, create references to each foreign key
```js
id: {
	...
},
foreignKey1: {
	type: ,
	references: {
		model: ,
		key:
	},
	onDelete:
},
foreignKey2: {
	type: ,
	references: {
		model: ,
		key:
	},
	onDelete:
}
```

This will create the table, it will still need to be populated with entrries

## Associations
Remember the three association types in SQL:
- One to one: one row in table A references on row in table B (mostly used to decrease the amount of info on a single table/record)
- one to many / many to one: one row in table A is referenced several times in another table (used when a single thing can have multiple things, one MH monster has many parts)
- many to many: when multiple tables interact with mutiple of each other - tracked with a join table (MH monsters would be a single table, MH locales would be a single table, and MonsterLocales would be a many to many - Many monsters can be in many locations)

A relationshis at its most basic level only tracks what connects to what. An association is a deeper level understanding of the relationship, and tracks not only what things connect, but HOW they connect. Something will have something else, and something will belong to something else. (One monster HAS many parts. A part BELONGS TO one monster)

### Defining Associations
A model's association to other models can be defined in the associations method of the model.
In this method, we will describe using built in sequelize methods how one table will associate itself with another.  
`Source.function(target)`
The source will start the connection, and the target will end the connection.
The association method in sequelize are: (we can reference other models using `models.`)
- A.hasOne(models.B);
- A.hasMany(models.B);
- B.belongsTo(models.A);
- B.belongsToMany(models.A);

#### One to one
```js
//In source
A.hasOne(models.B, {
	foreignKey: 'foreignKeyName',
	onDelete: 'cascade',
	hooks: true
	//we can also set a few more properties in the options
	//hooks is necessary, look up what it does later
});

//In target
//We can also define these just like we do in the migration
//this will let us set contraints
B.belongsTo(models.A, {
	foreignKey: {
	name: 'foreignKeyName',
	allowNull: false
	}
})
```

#### One to Many / Many to One
```js
A.hasMany(models.B, {
	foreignKey: 'Bid'
});

B.belongsTo(models.A, {
	foreignKey: 'Bid'
});
```

#### Many To Many
```js
// a many to many is a join table
//so instead of a foreign key, we pass in the join table in the options object
A.belongsToMany(models.B, {
	through: 'JoinTableName'
});

B.belongsToMany(models.A, {
	through: 'JoinTableName';
})

//in the join table, define the foreign keys in the model/migration files like normal columns, and then include the references information
```
*the foreign key in the association can go in either or both models*

### Using associations
An association is not sql code, but shortcuts that create sql code and helper methods when run (when we perform a query) This means that we only needs to define the associations that we plan on querying to make an association useful. *an association is not necessary, but helpful*
This includes the other side of an association,. If we will only be querying one sode of the data, we don't need to define the appropriate other side of the association.

## Query With Association
We can use associations with queries to obtain additional information. Sequelize will automatically generate methods to retireve related records depending on the association.

### get
get is an automatically generated method from sequelize that lets us query data after an initial query
```js
//when using a belongsTo, we can use the get method
A.belongsTo(models.B, {foreignKey: 'key'});

//in the query
const item = await A.findBYPk(pk);
const relatedItem = item.getB(); //SINGULAR model name for a belongsTo association

//when using belonggsToMany or hasMany, we will use a plural model name
X.belongsToMany(models.Y);

const x = await X.findByPk();
const yArr = x.getXs(); //plural model names
```

### include
Include does the same thing, but as another key in the options object.
Similar use: single for a belongs to association, plural for hasMany or belongsToMany associations
using the include option will add the associated data to each record retrieved

```js
//In the association for the model
A.belongsTo(models.B, {
	foreignKey: 'key',
	onDelete: 'cascade',
	hooks: true
});

//singluar include
const a = A.findAll({
	where: {
		name: 'name'
	},
	include: ModelNameSingular (B) //attatched a single records
});

//plural include
const a = A.findAll({
	where: {
		name: 'name'
	},
	include: ModelNamePlural (Bs) //attatches an array of all associated records
});
```

## Lazy Vs Eager Loading
There are two ways to load and retrive associated data. All at once, or piece by piece.
Eager loading is faster and more efficiant, but lazy loading can be more useful when loading loarge quantities of data (only pull a little at a time)
Use what is best for the situation.

### Lazy Loading
lazy loading isloading data later, until we need it. This involves loading the main record first, and then using that record to later retireve the associated data.
```js
//Initial record
const thisTweet = await Tweet.findByPk(Pk);

//Use initial record for further data
const user = await thisTweet.getUser();
//the tweet does not have the user on it
//using an async function to query for the user record
```

### Eager loading
Eager loading is retireving the data in one move, a full single query
```js
const fullTweet = await Tweet.findOne({
	where: {
		id: id
	},
	include: ModelName(s)
});
// now this record has everything included with it to begin with
//now the associated record will be included as a propoerty on the record
//access it through reference
fullTweet.ModelName(s);
```

## Inserting with Associations
Since data can now be associated to one another, we want to be able to create anew recrd AND reference the thing it belongs to or has at the same time. This is where inserting with associations comes in to play.

```js
Author.hasMany(models.Book, {
	foreignKey: 'authorId'
});

Book.belongsTo(models.Author, {
	foreignKey: 'authorId',
	onDelete: 'cascade',
	hooks: true
});

//to create a new author
await Author.create({
	name: 'name'
});

//to create a new book
await Book.create({
	title: 'title',
	authorId: authorId
});
```

Associations allow sequlize to automatically crate methods to associate related data when making new records

using the `record.createModelname()` method, we can instantly create a record an associate it to the record we called it on

```js
const thisAuthor = await Author.findOne({
	where: {
		name: 'name'
	}
});

await thisAuthor.createBook({
	title: 'title'
});
//we did not need to pss in information about the author
//it was filled out and created for us automatically with this method
//the book was created with the instance's authorUId inserted

//works the other way around
const newBook = await Book.create({
	title: 'title'
});

await book.createAuthor({
	name: 'name'
});
//created a new author and associated it to the book
//the book ALSO gets updated to be associated to the author
//both records instantly updated at once
```

### Multiple Associated Inserts
using the normal create method, we can create multiple instances using an array of associated data objects
```js
await Author.create({
	name: 'name',
	books: [
		{title: 'title'},
		{title: 'title'}
	]
},
// we can include this second options argument to retrieve the data we just made to see if it worked and was created
{ inlude: [ ModelName ] }	
);
```

### Adding already created data
if all we need to do is relate two pieces of data without making any new ones, we can use the `addModelName` method instead

```js
//adding on record
modelA.addModelB(instanceOfB)

//adding multiple records
Modelb.addModelAs([instA, instA, instA]);
```

## Dynamnic Seeding
Dynamic seeding is just seeding, but adding data to specific records by querying for them first and then updating them. if we wanted to seed tables with associated data, we would have to insert the foreignkey manually in each record being added. With dymnamic seeding, we instead find the matching record, and associate the record to it using sequelize

To do this, we need to import the models into the seeder file
```js
const { modelA, modelB } = require('path/to/models');
```

then we need a single piece of data that we will use to seed
```js
//an aray of people record
//each person has a name, and an array of job records
const data = [
  {
    fullName: 'John Doe',
    jobs: [
      { company: 'Starbucks', role: 'Barista'},
      { company: 'Geico', role: 'Insurance Salesperson'}
    ]
  },
  {
    fullName: 'Jane Doe',
    jobs: [
      { company: 'WNBA', role: 'Basketball Player'}
    ]
  }
]
```

Assuming this seed is just for adding jobs and the people aloready exist: we want to find each person and add to them a new record of jobs
```js
//in the up
for(let i = 0; i < data.length: i++) { //iterate through the data
	let  {name, jobs } = data[i]; //destruct the name and jobs as variables
	const person = await People.findOne({ //find the person we want to add data to
		where: {
			name: name
		}
	});

	//now we have a single person to update, we can add each of their job records
	//iterate through the jobs they have in a nested loop
	for(let j = 0; j < jobs.length; j++) {
		await person.createJob(jobs[j]); // createa a job record associated to the person for each job in the jobs array
	}
}

//in the down
//do the same thing we did before, but instead of creating job records, DESTROY job records

...
	for(let j = 0; j < jobs.length; j++) {
		await Job.destroy({
			where: {
				personId: person.id,
				...jobs[i] 
				//remember that spreading an object just spreads they key value pairs
				//this is the same as listing each key value
				//this makes a specific search query to delete from
				
			}
		});
	}
```

*SEEDING DATA DOES NOT NEED TO BE VERY EFFICIANT BECAUSE WE ONLY DO IT MAYBE ONCE, SAVE YOUR TIME AND EFFORT INTO MAKING OTHER SEARCH QUERIES MORE EFFICIANT*

## Ordering Query Results
Quesry results can be ordered and filtered down to be more presentable by adding the order keyword to any search query
Order wwll always be a nested array of values, with the inner arrays containing two values: the columnName, and the second (optional) value - ASC-ending or DESC-ending
```js
const students = Student.findAll({
	where: {
		...
	},
	attributes: [
	
	],
	order: [
		['col1', 'DESC'], ['col2',]
		//will forst order by the first argument, then by the second 
		//for all results that match on the first
		//default ordering is by id and ascending (smallest - largest)
	]
});
```

We can order associated data by passing in the model name as a first argument, and then that's data's column name, and then the order
```js
findAll({
	where: ...,
	attributes: ...,
	include: { model: ModelName}
	order: [
		[ModelC, 'ModelC-Col'], ['col']
	]
})
```