# Angular 2

## Naming

For components, directives, services, providers, helpers, reducers, interfaces etc., use the following naming conventions:

- For components: don't use `SomeComponent.ts` or `some-component.ts`, use `some.component.ts`
- For unit test spec files, use `some.component.spec.ts`
- For view template files, use `some.component.html`

## File ordering

For components, store all the files that have to do with your component in their same respective directory. The same goes for directives, services, providers, helpers, reducers, interfaces etc.:

```
components/
 └---some-view
      └---header
           |---header.component.html
           |---header.component.spec.ts
           └---header.component.ts
 
```

## Exports and imports

Don't use default exports in Angular files, no matter how much a good idea they seem.

**Good:**
```ts
export class SomeComponent {}
```

**Bad:**
```ts
export default class SomeComponent {}
```

Leave an empty line between native Angular and native library imports and custom imports, like so:

```ts
import { Component, Input, Output } from '@angular/core';
import { Store } from '@ngrx/store';

import { SomeService } from '../../serives';
import { SomeHelper } from '../../helpers';
```

Only use the require statement to import a module if the dependency doesn't support the new Node import/export notation:

```ts
import { Component } from '@angular/core';
const SomeLibrary = require('some-library'); // etc.
```

## Public, private, protected etc.

@Inputs, @Outputs, View or Content Child(ren), Hostbindings and any field you use from a template or annotate for Angular should be public:

**Plain wrong:**
```ts
export class SomeComponent {
	private @Input() someProperty: string = 'Give me some privacy';
}
```

Do not use the public statement. Everything in TypeScript (and JavaScript) is public by default:

**Good:**
```ts
export class SomeComponent {
	someProperty: string = 'Welcome!';
	private anotherProperty: string = 'Leave me alone';
}
```


**Wrong:**
```ts
export class SomeComponent {
	public someProperty: string = 'I was public anyway...';
}
```

Always annotate the access level for private and protected properties and methods:

```ts
export class SomeComponent {

	/**
	 * @private
	 * @type {boolean} - Property description here please
	 */
	private someProperty: boolean = false;

	/**
	 * Describe the following method over here.
	 * @private
	 * @param {sting} someParam - The text to log
	 * @return {void}
	 */
	private logSomeText(someParam: string): void {
		console.log(someParam);
	}
}
```

## Components

Always use a separate file for your view template. Do not write your HTML in the Component decorator:

**Good:**
```ts
import { Component } from '@angular/core';

@Component({
	selector: 'some-component',
	templateUrl: 'some.component.html'
})
export class SomeComponent {}
```

**Bad:**
```ts
import { Component } from '@angular/core';

@Component({
	selector: 'some-component',
	template: `
	<div class="some-somponent">
		<header class="some-component__header">
			Etc.
		</header>
	</div>
	`
})
export class SomeComponent {}
```

Don't use require statements for your templates or styles, use templateUrls and styleUrls. In Webpack, the Angular 2 template-loader plugin will change it to require at build time anyway:

**Good:**
```ts
@Component({
	selector: 'some-component',
	templateUrl: 'some.component.html',
	styleUrl: 'some.component.css'
})
```

**Wrong:**
```ts
@Component({
	selector: 'some-component',
	template: require('./some.component.html'),
	style: require('./some.component.css'),
})
```

## Forms

- Don't use `form.controls.controlName`, use `form.get('controlName)'`
- Don't use `control.errors?.someError`, use `control.hasError('someError')`