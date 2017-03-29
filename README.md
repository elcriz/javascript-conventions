# JavaScript and TypeScript conventions

## Functional (declarative) programming versus imperative programming

Try to use ES2015 functional programming methods (e.g. .map, .filter and .reduce) where applicable. This not only saves lines of code and obsolete variables, it is also a lot more readable and easier to understand:

**Wrong:**
```js
let totalPersonsAmount = 0;
let totalAnimalsAmount = 0;
let totalPlantsAmount = 0;

for (let index = 0; index++; index >= rooms.length) {
    totalPersonsAmount = personsCount + rooms[index].personsAmount;
    totalAnimalsAmount = personsCount + rooms[index].animalsAmount;
    totalPlantsAmount = personsCount + rooms[index].plantsAmount;
}
```

**Right:**
```js
const totalPersonsAmount = rooms.reduce((amount, room) => (room.personsAmount + amount), 0);
const totalAnimalsAmount = rooms.reduce((amount, room) => (room.animalsAmount + amount), 0);
const totalPlantsAmount = rooms.reduce((amount, room) => (room.plantsAmount + amount), 0);
```

Another example:

**Wrong:**
```js
let roomsWithAnimals = [];

for (let index = 0; index++; index >= rooms.length) {
    if (room.animalsAmount > 0) {
        roomsWithAnimals.push(room);
    }
}
```

**Right:**
```js
const roomsWithAnimals = rooms.filter(room => room.animalsAmount);

// or

const roomsWithAnimalsThatHavePlants = rooms
	.filter(room => room.animalsAmount)
	.filter(room => room.plantsAmount);
```

## Other new ES2015+ functionality

### Destructuring

Try to destruct objects if you need to use their properties in new variables, rather than declaring those variables separately:

**Wrong:**
```js
function doSomething(room) {
	let personsAmount = room.personsAmount;
	let animalsAmount = room.animalsAmount;
	let plantsAmount = room.plantsAmount;
	console.log(personsAmount); // i.e. 3
	// ...
}
```

**Right:**
```js
function doSomething(room) {
	const { personsAmount, animalsAmount, plansAmount } = room;
	console.log(personsAmount); // i.e. 3
	// ...
}
```

### Merging arrays

**Wrong:**
```js
const boysArray = ['Edgar', 'Raoul', 'Marvin'];
const girlsArray = ['Colinda', 'Sophia', 'Romy'];
const boysAndGirls = boysArray.concat(girlsArray);
```

**Right:**
```js
const boysArray = ['Edgar', 'Raoul', 'Marvin'];
const girlsArray = ['Colinda', 'Sophia', 'Romy'];
const boysAndGirls = [...boysArray, ...girlsArray];

// or:

const boysAndGirls = [...boysArray, 'Colinda', 'Sophia', 'Romy'];

const hasAnM = name => name.toLowerCase().indexOf('m') !== -1;
const personsWithAnMInTheName = [
	...boysArray.filter(hasAnM),
	...girlsArray.filter(hasAnM)
]; // logs [ ‘Marvin’, ‘Romy' ]
```

### Immutable pattern use

Try to keep the defining of new variables to an absolute minimum. This will keep memory garbage (and the need to clean it up) to a minimum as well. Also, less variables means less chance of errors.

***Wrong:***
```js
for (let key in object) { // valid code, but not optimal
	console.log(key);
}
```

**Right:**
```js
Object.keys(object).map(key => { // this is better in most cases
	console.log(key);
});
```

Try to keep the mutating of previously defined variables and data structures to a minimum. Mutating data can produce code that is hard to read and error prone.

**Wrong:**
```js
const person = {
	firstName: 'John',
	lastName: 'Papa'
};

const newPerson = person;
newPerson.lastName = ‘Denver’;

console.log(newPerson === person); // true
console.log(person); // { firstName: ‘John’, lastName: ‘Denver’ }
```

**Right:**
```js
const person = { // Do not use ‘let’ here, because we do not intend to change the value
	firstName: 'John',
	lastName: 'Papa'
};

const newPerson = Object.assign({}, person, {
	lastName: 'Denver'
});

// or, by using the ES7 proposed object spread operator

const newPerson = {
	...person,
	lastName: 'Denver'
};

console.log(newPerson === person); // false
console.log(person); // { firstName: ‘John’, lastName: ‘Papa’ }
console.log(newPerson); // { firstName: ‘John’, lastName: ‘Denver’ }
```

As you can see, thanks to the use of Object.assign, we prevent the mutation of the person object (which would otherwise be mutated by reference, since properties are passed by reference in objects (and arrays).

## Happy Path pattern use

Don’t use if-elseif-else and/or if-else loops. We should strive to reduce any unnecessary and/or superfluous nesting. Stick to the happy path:

**Wrong:**
```js
if (condition) {
	// ...
} else if (otherCondition) {
	// ...
} else {
	// ...
}
```

**Right:**
```js
if (condition) {
	doStuff();
}

if (!condition) {
	doOtherStuff();
}
```

**Better:**
```js
if (condition) {
	doStuff();
	return;
}

doOtherStuff();
```

Also, try to return as soon as you know your method cannot do any more meaningful work:

**Wrong:**
```js
const someMethod = (error, results) => {
	if (!error) {
		doOtherStuff(results);
		doMoreStuff();
		// ...etc.

	} else {
		handleError(error);
	}
}
```

**Right:**
```js
const someMethod = (error, results) => {
	if (error) {
		handleError(error);
		return;
	}

	doOtherStuff(results);
	doMoreStuff();
	// ...etc.
}
```

## Annotations and documentation

Write annotations for all properties and methods. This not only is helpful for new developers joining our team, but is also a helpful reminder of how your code works if you re-visit it after a long time. Besides, many IDEs and editors show annotations when you hover annotated method or function wherever it is used in your code.

**Wrong:**
```ts
// --------- JavaScript

function setLoadingState(to) { // ES5
	this.isLoading = to;
}

const setIsLoading = to => { // ES2015+
	this.isLoading = to;
};

// --------- TypeScript

public isLoading: boolean = false;

public setLoadingState(to: boolean): void {
	this.isLoading = to;
}
```

**Right:**
```ts
// --------- JavaScript

/**
 * Set the loading state.
 * This is a multiline comment.
 * @param {boolean} to - The new state
 * @return {void}
 */
function setLoadingState(to) {
	this.isLoading = to;
}

// or

/**
 * Set the loading state.
 * @param {boolean} to - The new state
 * @return {void}
 */
const setIsLoading = to => {
	this.isLoading = to;
};

// --------- TypeScript

/**
 * @type {boolean} - Loading state
 */
public isLoading: boolean = false;

/**
 * Set the loading state.
 * @param {boolean} to - The new state
 * @return {void}
 */
public setLoadingState(to: boolean): void {
    this.isLoading = to;
}
```
