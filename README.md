# Nucleo

Nucleo creates and manages a strongly typed and predictable state container free of dependencies. It was built in TypeScript for be ran in any JavaScript ecosystem.

> Important note: This project is still under development. It's not recommended for production use yet, despite Nucleo basic functionalities are checked as done.

## Roadmap

It's in this project milestones https://github.com/mtmr0x/nucleo/milestones;

For requesting any feature, please open an issue with the prefix "[FEATURE REQUEST]".

## Why

JavaScript is a really dynamic language which we can't always rely in the language resilience from types perspective. Every project has types problems and developers can not make sure all the time the data is predictable. Inspired by how GraphQL does a great job with safety and trustful data and Redux by its simplicity on storing state, Nucleo was idealized to make sure the data model (contracts) and the data types are expected and reliable.

## Installation

Using NPM:

```
npm i nucleojs
```

Using Yarn:

```
yarn add nucleojs
```

## Basic usage

### Defining a data model (contract):

```javascript
import {
  NucleoString,
  NucleoNumber,
  NucleoObject
} from 'nucleojs'

const completeNameContract = new NucleoObject({
  name: 'completeNameContract',
  fields:  {
    firstName: NucleoString,
    lastName: NucleoString
  }
});

const userContract = new NucleoObject({
  name: 'user', // don't need to be the same name as the variable, but need to be unique
  fields: {
    name: completeNameType,
    age: NucleoNumber
  }
});

const productsContract = new NucleoObject({
  name: 'products',
  fields: {
    title: NucleoString
  }
});

const contracts = {
  user: userContract,
  products: productsContract
};
```

### Creating the store:

```javascript
import { createStore } from 'nucleojs';

const store = createStore(contracts); // send contracts to create the store
const { dispatch, update, getStore, subscribe } = store; // these 4 functions are returned from store creation
```

### Dispatching and updating the store:

Nucleo provides two methods of saving data, used for different approaches.

**dispatch:** works for saving data according to the full contract, used to save the very first contract state in the store or to update the whole contract in the store;

**update:** works for updating parts of data, it performs a index search in the object and save it. `update` will fail if you try to first save a contract to the store using it.

---

Dispatch function, considering user contract above:

```javascript

dispatch('user')({ name: { firstName: 'John', lastName: 'Nor' } });
// it'll fail because it's missing age field

dispatch('user')({ name: { firstName: 'John', lastName: 'Nor' }, age: 27 });
// it'll save the data to store properly
```

Update function, considering user contract above:

```javascript
update('user')({ name: { firstName: 'John' }});
// it'll update only the user first name and only if this item has been already created in the store before
```

### Subscribing to changes

You can simply subscribe to store changes by passing your listener functions to `subscribe` function. The listener must be a function and Nucleo will execute your listener sending an object argument with the updated contract for each update or dispatch to the store:

```javascript
subscribe(listener); // if it's not a function, Nucleo will throw an error
```

And inside Nucleo, your listener will be executed like this:

```javascript
listener({ contractName }); // This way you can understand better what was updated and consult Nucleo store as you wish
```

## Error management

Nucleo makes error management easy by type checking every level of contracts and returning an Object human and machine readable. The `update` and `dispatch` methods return an object with the following structure:

```javascript
{
  status:  'NOK',
  errors: [
    {
      error: '<some key> is not in <some contract> contract and can not be saved in store',
      contract: 'contractName',
      field: 'fieldName'
    }
  ],
  data: { ... } // the data you just tried to save in store
}
```

Code example:

```javascript
import { 
  NucleoString,
  NucleoObject
} from 'nucleojs';

const userType = new NucleoObject({
  name: 'user',
  fields: {
    name: NucleoString,
  }
});

const contracts = {
  user: userType
};

const { update } = createStore(contracts); // send contracts to create the store

const user = update('user')({ age: 140 });

console.log(user.errors); // here you'll find the error below:
/*

  [
    {
      error: 'age field does not match its rules according to user contract',
      contract: 'user',
      field: 'age'
    }
  ]
}
*/
```

In cases with no errors, `errors` list will be an empty list and `status` will be `OK`

```javascript
{
  status: 'OK',
  errors: []
  data: { ... } // the data you just tried to save in store
}
```

## Contributing

Nucleo is open to any contribution made by community. And by contributing to Nucleo, you agree to our [code of conduct](https://github.com/mtmr0x/nucleo/blob/master/CODE_OF_CONDUCT.md)

### Bugs and improvements

Open a issue with the prefix [BUG] or [FEATURE REQUEST] for one of those cases with no restrictions, and try to clarify all your points bringing examples and well-founded arguments. If you need help in something, just make it clear at the beginning of your issue.

### Pull Requests

Once you want to fix something for Nucleo, you can just go ahead and do it, but for making people know the problem and that you're working on it (and maybe can get even more information) open an issue about it, and if necessary, just point that you desire to fix it, or if some help is needed.

For new features, it's highly recommended to open an issue first and discuss more to make sure this feature is really required to the project.

### Roadmap

Every approved issue as features and issues for fixes will be placed at Nucleo roadmap, in this repository milestones.

### Critical fixes

In case of critical fixes, which applies to everything that would directly interfere in Nucleo functionalities, we'll work hard to solve it and publish a new minor patch as soon as possible.

