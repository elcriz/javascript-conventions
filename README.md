# JavaScript conventions

## Functional (declarative) programming versus imperative programming

Whenever possible, try to use functional programming methods (such as `.map`, `.filter`, `.reduce` and the older `.forEach` for arrays) and paradigms. This not only saves lots of lines of code and variable creation bloat, it is also a lot more readable and easier to understand at first sight.

**Good:**
```js
// Just 3 variables (we are likely never to change their values again)
const totalPersonsAmount = rooms.reduce((total, room) => total + room.personsAmount, 0);
const totalAnimalsAmount = rooms.reduce((total, room) => total + room.animalsAmount, 0);
const totalPlantsAmount = rooms.reduce((total, room) => total + room.plantsAmount, 0);
```

**Bad:**
```js
let totalPersonsAmount = 0; // variable 1
let totalAnimalsAmount = 0; // variable 2
let totalPlantsAmount = 0; // variable 3

for (let index = 0; index++; index >= rooms.length) { // variable 4
	totalPersonsAmount = personsCount + rooms[index].personsAmount; // mutations
	totalAnimalsAmount = personsCount + rooms[index].animalsAmount; // mutations
	totalPlantsAmount = personsCount + rooms[index].plantsAmount; // mutations
}
```

By using `Array.prototype.reduce` in the example above, we can define a variable and abstractly assign its logic in one go, thus saving 5 lines of code (whilst maintaining readability), an unneeded variable and a headache ahead.

Another example, using `Array.prototype.filter`:

**Good:**
```js
const roomsWithAnimals = rooms.filter(room => room.animalsAmount);
```

**Bad:**
```js
let roomsWithAnimals = [];
for (let index = 0; index++; index >= rooms.length) {
    if (room[index].animalsAmount > 0) {
        roomsWithAnimals.push(room);
    }
}
```

Another example, using `Array.prototype.find`:

```js
const roomWithOnePerson = rooms.find(room => room.personsAmount === 1);
```

Try to chain these functional methods together, if possible:

```js
const roomWithPlantsAndOnePerson = rooms // A single variable, containing the one object we need
	.filter(room => room.plantsAmount)
	.find(room => room.personsAmount === 1);
```

### Destructuring vs declaring many, many variables

Try to destruct objects if you need to use their properties in new variables, rather than declaring those variables separately. This mostly comes in hand when a function needs to do something with an object it receives:

**Bad:**
```js
function doSomething(room) {
	let personsAmount = room.personsAmount;
	let animalsAmount = room.animalsAmount;
	let plantsAmount = room.plantsAmount;
	console.log(personsAmount); // i.e. 3
	// ...
}
```

**Good:**
```js
function doSomething(room) {
	const { personsAmount, animalsAmount, plantsAmount } = room;
	console.log(personsAmount); // i.e. 3
	// ...
}
```

### Merging arrays using spread operators

**Less good:**
```js
const boysArray = ['Edgar', 'Raoul', 'Marvin'];
const girlsArray = ['Colinda', 'Sophia', 'Romy'];
const boysAndGirls = boysArray.concat(girlsArray);
```

**Good:**
```js
const boysArray = ['Edgar', 'Raoul', 'Marvin'];
const girlsArray = ['Colinda', 'Sophia', 'Romy'];
const boysAndGirls = [...boysArray, ...girlsArray]; // offers more possibilities

// or:

const boysAndGirls = [...boysArray, 'Colinda', 'Sophia', 'Romy'];

const hasAnM = name => name.toLowerCase().indexOf('m') !== -1;
const personsWithAnMInTheName = [
	...boysArray.filter(hasAnM),
	...girlsArray.filter(hasAnM)
];
console.log(personsWithAnMInTheName); // [ ‘Marvin’, ‘Romy' ]
```

## Immutability: once a value, always that value

Try to keep the defining of new variables to an absolute minimum. This will keep memory garbage (and the need to clean it up) to a minimum as well. Also, less variables means less chance of errors.

**Good:**
```js
const arrayWithKeys = Object.keys(someObject);
```

**Bad:**
```js
let arrayWithKeys = [];
for (let key in someObject) {
	arrayWithKeys.push(key);
}
```

Try to prevent mutating of previously defined variables and data structures to a minimum or better: none at all. Why? Mutating data can produce code that is hard to read and error prone down the line. Also, by maintining the immutability pattern, your code becomes more predictable and is easier to (unit) test as well.

- Assign value to a variable once
- Prevent assigning new values by reference
- Create a new variable if you need to work with a new value

**Good:**
```js
const person = { // Do not use ‘let’ here, because we do not intend to change the value
	firstName: 'John',
	lastName: 'Papa'
};

const newPerson = Object.assign({}, person, { // The empty object {} kills the reference
	lastName: 'Denver'
});

console.log(newPerson === person); // false
console.log(person); // { firstName: ‘John’, lastName: ‘Papa’ }
console.log(newPerson); // { firstName: ‘John’, lastName: ‘Rambo’ }
```

**Bad:**
```js
const person = {
	firstName: 'John',
	lastName: 'Papa'
};

const newPerson = person;
newPerson.lastName = 'Rambo';

console.log(newPerson === person); // true
console.log(person); // { firstName: ‘John’, lastName: 'Rambo' }
```

As you can see, thanks to the use of `Object.prototype.assign`, we prevent the mutation of the person object (which would otherwise be mutated by reference, since properties are passed by reference in objects (and arrays).

### Immutability: copying/merging objects using object spread operators

Using `Object.prototype.assign` to create a copy of an object with new or updated values like in the examples above is good practice, but its syntax is rather verbose and thus difficult to read (depending on the context). An alternative approach is to use the **object spread operator**, proposed for newer versions of JavaScript. This lets you use the spread (`...`) operator for objects, in a similar way to the array spread operator:

```ts
// Using Object.prototype.assign:
return Object.assign({}, originalObject, {
	newProperty: 'some value'
});

// Using the ES7 proposed object spread operator
return { ...originalObject, newProperty: 'some value' };
```

Since the object spread syntax is still a Stage 3 proposal for ECMAScript you'll need to use a transpiler such as Babel to use it in production. If you are using TypeScript for your project, you can already use the object spread operator if you are using version 2.1 or above.

### Immutability: the use of `const` vs `let`

Try to use `const` if you intend never to change the value or reference again. Use `let` if you must reassign references (for example, within a `for` loop) or better: just don't do that. By the way, block scoping is achieved by both `let` and `const`:

```js
if (true) {
	let cheese = 'yellow'; // block scoped, shiny yellow cheese
	const banana = 'yellow';
}

cheese = 'green'; // will throw an exception because 'cheese' doesn't exist here
console.log(banana); // same thing: ReferenceError
```
### Don't take `const`'s immutability for granted

Remember: although `const` suggests that the variable created will be immutable (a constant, as used in many other programming languages), strictly taken it isn't. The use of `const` is no guarantee the variable's value can't be changed again:

```js
const someObject = {};
someObject.someProperty = 42;
console.log(someObject.someProperty); // logs 42
```

In the example above, only the _binding_ is immutable, not the value. Only primitive types values are immutable by defenition:

```js
const someVariable = 42;
someVariable.someValue = 16;
console.log(someVariable.someValue); // logs 'undefined'
```

To really make an object's value immutable, you can use `Object.prototype.freeze`:

```js
const someObject = Object.freeze({
	someProperty: 42
});
someObject.someProperty = 16; // throws an exception
```

Keep in mind that `Object.prorotype.freeze` (which is widely supported since ES5) only 'freezes' shallow values. Deep values can (but should not) be changed.

## Happy Path pattern use: avoid else, return early

Don’t use if-elseif-else and/or if-else loops. We should strive to reduce any unnecessary and/or superfluous nesting:

- Return as soon as you know a method cannot do any more meaningful work
- Reduce indentation by using if/return instead of a more toplevel if/else
- **Most important:** Try to keep the heavy part of your method's code at the lowest indendation level, and keep special cases together at the top

**Good:**
```js
if (condition) {
	doStuff();
}

if (!condition) {
	doOtherStuff();
}
```

**Bad:**
```js
if (condition) {
	// ...
} else if (otherCondition) {
	// ...
} else {
	// ...
}
```

Inside methods, try to return as soon as you know your method cannot do any more meaningful work:

**Good:**
```js
const someMethod = (error, results) => {
	if (error) {
		handleError(error);
		return;
	}

	doOtherStuff(results);
	doMoreStuff();
	// ...etc.
	// ...
}
```

**Bad:**
```js
const someMethod = (error, results) => {
	if (!error) {
		doOtherStuff(results);
		doMoreStuff();
		// ...etc.
		// ...

	} else {
		handleError(error);
	}
}
```

## Documenting your code with comments and annotations

Write annotations for all properties and methods. This is not only helpful for new developers joining the team, but is also a useful reminder of how your code works if you re-visit it after a long time. Besides, many IDEs and editors show annotations when you hover annotated method or function wherever it is used in your code, also showing you what types a method uses and why.

**Good:**
```js
/**
 * Set the loading state.
 * @param {boolean} to - The new state
 * @return {void}
 */
const setIsLoading = to => { // editor now also provides type and description for method, return variable and parameter
	this.isLoading = to;
};
```

**Bad:**
```js
const setLoadingState = to => { // some editors only provide the name of the 'to' parameter
	this.isLoading = to;
}
```

Typescript example:
```ts
/**
 * This class does this and that.
 */
class SomeClass extends AnotherClass {

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

}
```
