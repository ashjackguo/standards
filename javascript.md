# Javascript


1. **[Variables](#variables)**
1. **[Strings](#strings)**
1. **[Operators](#operators)**
1. **[Arrays](#arrays)**
1. **[Objects](#objects)**
1. **[Functions](#functions)**
1. **[Iteration](#iteration)**
1. **[Try/Catch](#trycatch)**
1. **[Libraries](#libraries)**
1. **[NPM](#npm)**
1. **[Documentation & Style](#documentation)**

## <a name="variables">Variables</a>

### 1.1 `const/let/var`
#### When working in ES6 (Ember Projects, gulpfile, etc.)

Default to `const`. If you need to change the value later on, then you can use `let`. Do not use `var` **ever**

> This ensures that you can't reassign your references, which can lead to bugs and difficult to comprehend code.

#### When in legacy code (non-Ember projects)
Do **not** use `const` or `let`.

### 1.2 Instantiate variables at top of functions

> Avoids issues with hoisting and temporal dead zones

```javascript
//bad
function foo() {
	this.year = 1993;

	const foo = 'bar';

	this.baz = foo;
}

//good
function foo() {
	const foo = 'bar';

	this.year = 1993;
	this.baz = foo;
}
```

### 1.3 Declare variables across several lines

> Simplify changeset diffs, reduce risk of creating global variables, helps with breakpoints.

```javascript
//very bad
function foo() {
	const foo = 1, bar = 2, baz = 3;
}

//bad
function foo() {
	const foo = 1,
		  bar = 2,
		  baz = 3;
}

//good
function foo() {
	const foo = 'bar';
	const bar = 2;
	const baz = 3;
}
```

### 1.4 Group together `const` and `let` declarations
> To avoid complications...

```javascript
//bad
function sheNever() {
	const foo = 1;
	let bar = 2;
	const baz = 3;
	let bae = 4;
}

//good
function keptTheSameAddress() {
	const foo = 1;
	const baz = 3;
	let bar = 2;
	let bae = 4;
}
```

### 1.5 Variable Casing
When using `const`, do *not* capitalize the variable name.

> It's unnecessary.


```javascript
//bad
const FOO = 1;
const BAR = 3;

//good
const foo = 1;
const baz = 3;
```

## <a name="strings">Strings</a>

### 2.1 String Literals (ES6 only)
> This ensures readability in strings where variables are inserted

Always use string literals when concatenating strings together.

```javascript
//bad
const string = 'The ASH UI team is ' + awesomeAdjective + '!';

//good
const string = `The ASH UI team is ${awesomeAdjective}!`;
```

## <a name="operators">Operators</a>

### 3.1 Equality Operators
Default to Strict Equality, `===`. Use `==` only for special cases, such as when you don't care about the type. When using `==` comment code explaining your choice.

```javascript
//bad
if(1 == '1')        // evaluates to true  

//good
if('5' === 5)       // evaluates to false
if(5 === 5)         // evaluates to true
```

## <a name="arrays">Arrays</a>

### 4.1 Dangling Commas
There is no hard standard for dangling commas.

> Yes it looks ugly and weird but it has benefits in simplifying diffs.

```javascript
//ok
const arr = [
	'foo',
	'bar',
];
const obj = {
	hey: true,
	jude: true,
};

//also ok
const arr = [
	'foo',
	'bar'
];
const obj = {
	hey: true,
	jude: true
};
```

## <a name="objects">Objects</a>

### 5.1 Method Declarations
**When able to code in ES6**, use shorthand method declarations. Otherwise normal method declarations are OK.

```javascript
//bad unless stuck in ES5
const obj = {
    doThisThing: function() {
        return true;
    }
};

//good (but only in ES6)
const obj = {
    doThisThing() {
        return true;
    }
};
```

### 5.2 Dangling Commas
There is no hard standard for dangling commas.

> Yes it looks ugly and weird but it has benefits in simplifying diffs.

```javascript
//ok
const arr = [
	'foo',
	'bar',
];
const obj = {
	hey: true,
	jude: true,
};

//also ok
const arr = [
	'foo',
	'bar'
];
const obj = {
	hey: true,
	jude: true
};
```

## <a name="functions">Functions</a>

### 6.1 Arrow Functions
#### You can only use these within ES6. Ignore this section when working with ES5.

Arrow functions cut down and have special properties in terms of lexical scoping.

This preserves the value of `this` which removes the need for you to "save" the value of this (commonly as `that` or `self`).

```javascript
const myObject = {
	init() {
		const that = this;

		someFunctionWithCallback(1, function() {
			console.log(this);

			//logs the Window object. Most likely not the behavior you are expecting. You have use the "that" variable we initiated up there.

			console.log(that);
		});
	},
	init2() {
		someFunctionWithCallback(1, () => {
			console.log(this);

			//logs myObject!
		});
	}
}
```
When there is only one parameter, do not encase it in parenthesis.

```javascript

//bad
let itemsLength = items.map((item)=>{
 return item.length;
});

//good
let itemsLength = items.map( item =>{
 return item.length;
});
```

#### Exceptions
Use normal syntax for things like event listeners where do **not** want to preserve `this`.

```javascript
// This is a special exception.
//bad
thing.on('click', e => {
	$(this).addClass('newClass');
	//this will NOT work
});

//good
thing.on('click', function(e) {
	$(this).addClass('newClass');
});
```

## <a name="iteration">Iteration</a>

### 7.1 Use of `for` loop vs. iterative methods (`.map()`, `.forEach()`, etc.)

**When working with large datasets**, use the classic `for` loop as it allows you to `break;` out of the iteration, which prevents the expense of looping through unnecessary items after a certain criteria has been met. **You cannot do this with iterative methods**

```javascript
const array = [1, 2, 3, ... 10,000];
const newArray = [];

// Bad
array.forEach((item, index) => {
	if (index < 50) {
		newArray.push(item * 3);
	}
});

// Good
for (let i = 0; i < array.length; i++) {
	if (i === 50) {
		break;
	}
	newArray.push(array[i] * 3);
}
```
## <a name="errors">Error Handling</a>

### 8.1 Displaying Errors
> Errors happen. If an error blocks or inhibits the user flow, we should always display the error to the user. You can `console.error()` errors if useful for developers, but do not rely on this for end users. If using Ember, you can use the `debug` or `warn` methods, which get stripped out in production (see [@ember/debug](https://emberjs.com/api/ember/release/modules/@ember%2Fdebug) for more info).

### 8.2 Try/Catch
> Helps catch errors before they cause the code to blow up

**Use try/catch block if:**
 - You are attempting to access an object that is more than **two levels** deep
 - You are even slightly uncertain that the object structure may vary

**Tips**
- Make sure that the catch block of the try/catch does something meaningful for the user
- If you need a fail-safe error message to display as a result of the catch in a try/catch, talk to your PO for the message content

```javascript
//bad
function showErrorMessage (arg1){
	//this will result in Uncaught TypeError: Cannot read property 'responseJSON' of undefined
	error = arg1.response.responseJSON.responseStatus;
	return error;
}
```

**Executing something after try catch:** Use finally to execute that code rather than rewrite the code twice or only write for the happier (try) path.

```javascript
//good
function showErrorMessage (arg1){
	try {
		error = arg1.response.responseJSON.responseStatus;
	} catch (e) {
		error = 'Something went wrong fetching the response'; //in this case, e is undefined so you don't need it on the line above
		//OR
		error = e;
	} finally {
		removeLoader();
		return error;
	}		
}
```

### 8.3 Catching Errors with Asynchronous Operations
> Anytime you're doing something with a successful resolution of an asynchronous operation (happy path), you should ensure you are also catching errors (sad path).

#### Using Promise.then()
Use `.catch()` to catch any errors with the promise, including http errors.

```javascript
//bad
const list = [...staleList];

fetchNewestItem().then(response =>{
	list.push(response);
})
```

```javascript
//good
const list = [...staleList];

fetchNewestItem().then(response =>{
	list.push(response);
}).catch(error =>{
	displayErrorToUser(error);
})
```

#### Using Async/Await
Similar to `Promise.then()`, include a catch, but use a try/catch statement for `async` functions.

```javascript
//bad
const list = [...staleList];

(async function (){
	list.push(await fetchNewestItem())
})()
```

```javascript
//good
(async function (){
	try{
		list.push(await fetchNewestItem())
	}catch(error){
		displayErrorToUser(error)
	}
})()
```

## <a name="#libraries">Libraries</a>

### 9.1 jQuery

#### Write in Vanilla JavaScript wherever possible
> Why? jQuery is a huge library, and no longer necessary for cross-browser compatibility. Writing in vanilla javascript will also give you a deeper understanding of the core language

**Note:** When updating legacy code to use vanilla javascript, be mindful of which objects are passed to 3rd party plugins or in-house plugins that still use jQuery, and wrap the object in a jQuery wrapper accordingly

## <a name="npm">NPM</a>
### 9.1 Semantic Versioning

- Update the Major version when making a breaking API changes.
- Update the Minor version when introducing a new feature.
- Update the Patch version when adding a bug fix.
- Always start on a minor version 0.1.0.
- Do not publish to the registry until the code can deliver functionality, tested, and can be consumed by other applications.
- If the code is being used on production, then it should be at least on version 1.0.0.

## <a name="documentation">Documentation & Style</a>
> Why? Because other devs need to be able to work in your code without setting up a meeting first.

### 10.1 Documentation
While there isn't a hard standard, a good rule of thumb is if you need to explain what it's doing to the next developer, you should add documentation. You can document a block at the top of a function or tell a story as developers step through your code. Don't document obvious things.

```javascript
//BAD

function isPalindrome(word){

	// create and set the newWord variable
	let newWord = word.replace(/[^a-zA-Z0-9]/g, '');

	newWord = newWord.toLowerCase();

	// return true if one of the conditions are true
	if(newWord.length == 0 || newWord.length == 1){
		return true;
	}


	if(newWord === newWord.split('').reverse('').join('')){
		return true;
	}

	// return false
	return false;
}
```

```javascript
// Good

// Check if a given word/string is palindrome
function isPalindrome(word){

	// strip all characters and symbols except a-z, A-Z, and 0-9
	let newWord = word.replace(/[^a-zA-Z0-9]/g, '');

	newWord = newWord.toLowerCase();

	if(newWord.length === 0 || newWord.length == 1){
		return true;
	}

	// check the original word against the new reversed string
	if(newWord == newWord.split('').reverse('').join('')){
		return true;
	}

	return false;

}
```


### 10.2 Readability

- Good code is readable by other developers.
- Good code can do complex things.
- Code that can do complex things, but is not readable by other developers is not good code.
