## Documentation
[API Docs](https://sequelize.org/api/v6/)
[Sequelize Docs](https://sequelize.org/docs/v6/category/core-concepts/)
[QueryInterface](https://sequelize.org/api/v6/class/src/dialects/abstract/query-interface.js~queryinterface)
[Validations](https://sequelize.org/docs/v6/core-concepts/validations-and-constraints/#validators)
[Operators](https://sequelize.org/docs/v6/core-concepts/model-querying-basics/#operators)


## Syntax
When referring to the *model* or the *table*, they will be written in CapitalCase.
	The *model* will be singular.
		*Animal*
	The *table* will be plural.
		*Animals*

When referring to a property or an instance, they will be written in camelCase.
	An instance of a *model*
		*animal*
	An array of instances
		*animals*
	A column in a table.
		*animalKingdom*


## Command Line

### Create a migration
- NOTICE the migration is singular!
npx sequelize migration:generate --name purpose-of-migration
*npx sequelize migration:generate create-animal*
*npx sequelize migration:generate add-weight-to-animal*

### Create a model
- Remember! Creating a model automatically creates a migration with the same attributes too!
- There are no spaces between attributes, they're seperated by commas only
- NOTICE the model is singular!
npx sequelize model:generate --name ModelName --attributes name:type,name:type
*npx sequelize model:generate --name Animal --attributes weight:float,species:string*

### Create a seed
npx sequelize seed:generate --name name-of-seed
*npx sequelize seed:generate --name zoo-reptiles

## Running Migrations/Seeders
- Since we're **modifying** the contents of the database, we need to include *dotenv* and *db*
*npx dotenv sequelize db:migrate*
*npx dotenv sequelize db:seed*
*npx dotenv sequelize db:seed:all*

## Undoing Seeds
*npx dotenv sequelize db:migrate:undo*
*npx dotenv sequelize db:migrate:undo:all*
*npx dotenv sequelize db:seed:undo*
*npx dotenv sequelize db:seed:undo:all*

## Up VS Down
In both the seeder and migration files, there is code for when the file is *run* (up) and *undone* (down). These add or remove the changes made to the database.
Whatever we do in one, we should do the opposite in the other.
- We will use `await queryInterface` when we modify the contents of a database
Some opposite methods include:
`await queryInterface.createTable() / .dropTable()`
`await queryInterface.addColumn() / .removeColumn()`
`await queryInterface.addConstraint() / .removeConstraint()`
`await queryInterface.bulkInsert() / .bulkDelete()`

## What goes where?

### Model
- Class and instance level methods
- Constraints *and* validations

#### Example Model
`npx sequelize model:generate --name cat --attributes name:string,age:integer`

```js
'use strict';
const { Model } = require('sequelize');
module.exports = (sequelize, DataTypes) => {
  class Cat extends Model {
    // ...

    static associate(models) {
      // ...
    }
  };

  Cat.init({
    name: {
      allowNUll: false,
      type: DataTypes.STRING,
      validate: {
        notEmpty: true,
        len: [1, 20],
        isAlpha: { msg: 'Name can only contain letters' }
      }
    },
    age: {
      type: DataTypes.INTEGER,
      validate: { min: 0 }
    }
  }, {
    sequelize,
    modelName: 'Cat'
  });
  return Cat;
};
```

### Migration
- Constraints
- `await queryInterface`
- methods like `.addColumn` & `.removeColumn`

#### Example Migration
`npx sequelize migration:generate --name create-cat`

```js
'use strict';
module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.createTable('Cat', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      name: {
        type: Sequelize.STRING,
        allowNull: false
      },
      age: {
        type: Sequelize.INTEGER
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE,
        defaultValue: Sequelize.literal('CURRENT_TIMESTAMP')
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE,
        defaultValue: Sequelize.literal('CURRENT_TIMESTAMP')
      }
    });
  },
  down: async (queryInterface, Sequelize) => {
    await queryInterface.dropTable('Cat');
```

### Seed
- `await queryInterface`
- methods like `.bulkInsert()` & `.bulkDelete()`
- the `bulkDelete()` option object is specifically a where clause, the `where` does not need to be inserted

#### Example Seed
`npx sequelize seed:generate --name starter-reptiles`

```js
'use strict';

const { sequelize } = require("../models");
const { Op } = require('sequelize');

const data = [
  {
    name: 'Rio',
    age: 3
  },
  {
    name: 'Aava',
    age: 8
  },
  {
    name: 'Scout',
    age: 1
  },
  {
    name: 'Cuga',
    age: 1
  }
];

module.exports = {
  up: async (queryInterface, Sequelize) => {
    await queryInterface.bulkInsert('Cats', data);
  },

  down: async (queryInterface, Sequelize) => {
    await queryInterface.bulkDelete('Cats',{
      [Op.or]: data
    })
  }
};
```

## Finding Records
```js
//Single - Primary Key
const record = await Model.findByPk(id);

//Single - Query Search
const record = await Model.findOne({
	where: {}
});

//Multiple - Query Search
const records = await Model.findAll({
	where: {}
});
```

## Updating records
```js
//Single - One Step
const record = await Model.findByPk();
record.update({
	key: newVal || this.originalVal
});

//Single - Multi-step
const record = await Model.findByPk();
record.key = val;
await record.save();
```

## Deleting Records
```js
//Single
const record = await Model.findByPk(id);
await record.destroy();

//Multiple
await Model.destroy({
	where: {}
})
```