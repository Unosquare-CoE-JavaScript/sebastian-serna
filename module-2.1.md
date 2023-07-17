# Testing web app with Cypress

**Cypress** is a frontend testing tool for web applications that allows us to make end to end test

**Basics**

Commonly for testing we need to isolate our tests in suite. Cypress provides us a `describe()` function to isolate our tests and a cy object to assert our code

**Basic methods**

For every test you need to navigate the page
`cy.visit('your-page')`

These methods allow us to get elements in our page
`cy.get('selector')`
`cy.contains('text')`
`cy.should()`

```js
describe('Cypress testing', () => {
	beforeEach(() => {
		cy.visit('/my-page');
	});
	
	it('should have a form', () => {
		cy.get('form').should('exist');
	});

	it('should have the words Add Item', () => {
		// contains looks in the whole page for the text
		cy.contains('Add Item');
	});

	it('should write something in an input field', () => {	
		// you can type in an input field
		cy.get('[data-test]="new-item-input"').type('Good');
		// you can click elements
		cy.get('[data-test]="add-item"').click();
	});
});
```

Testing turns into an easy task when following these simple steps
- Visiting a page
- Query for an element
- Trigger an event on the element
- Make an assertion

**Aliases**

This feature gives us a way to give a name to an element and reference it later on

```js
// aliases
cy.get('[data-test]="items-unpacked"').as('unpackedItems');
cy.get('[data-test]="items-packed"').as('packedItems');

it('should hold onto an alias', () => {
	// reference to the alias
	cy.get('@unpackedItems').find('label').first().as('firstItem');
	cy.get('@firstItem').invoke('text').as('text');
});
```

**Generating tests**

You can generate test programmatically with Javascript to mock values from APIs or things we usually get from the server.

```js
const filterColumnsData = [
	'name',
	'description',
	'where to order',
	'secret',
];

describe('Test for columns', () => {
	// visit the page and write tests
	
	for (const property of properties) {
		it(`should have a column for ${property}`, () => {
			// check the columns are rendered with the mocked values programmatically
			cy.get(`#${property}-column`);
		});
	}
});
```

We can also check the path of our location programmatically

```js
it('should navigate to "/sign-in" when you click the "Sign In" button', () => {
	cy.get('[data-test]="sign-in"').click();
	cy.location('pathname').should('contain', 'sign-in');
});
```

**Form validation**

Native HTML validation triggers the default form validation that the browser provides us, how do we go about to test this in different browsers

```js
it('should require an email', () => {
	cy.get('@submit').click();
	// invoke the prop validationMessage
	cy.get('[data-test]="sign-up-email:invalid"').invoke('prop', 'validationMessage').should('contain',  'Please fill out this field');
	
	// validate the state
	cy.get('[data-test]="sign-up-email:invalid"').invoke('prop', 'validity').should('be.true');
});
```

**Tasks and Commands**

When we use cypress, we control the browser with Javascript. But, there are times where we might need to do stuff on the Node.js level. Cypress provides us tools for this.

We should store our tasks in a `plugins` directory

```js
import reset from 'prisma/reset.cjs';

const plugins = (on) => {
	on('task', {
		reset() {
			return reset();
		},
		seed() {
			return seed();
		}
	});
}

export default plugins;

// test...........
it('should execute a task'. () => {
	cy.task('reset');
});

// Add a command
Cypress.Commands.add('command-name', () => {...});
```

**Network requests & Sessions**

Most applications rely on other systems, like third-party APIs so cypress provides us a method to intercept network requests

```js
cy.intercept({
	method: 'GET',
	url: '/users/*',
	[{username: 'John Doe', id: 1}]
}).as('getUsers'); // alias to reference cypress
```

Or we can use fixtures which are dummy data cypress provides us

```js
cy.intercept('GET', '/activities/*', { fixture: 'my dummy value' });
```

When we intercept a request cypress returns us the body and we can make our assertions

```js
cy.intercept('POST', '/user/*', (req) => {
	expect(req.body).contains('some user');
});
```

As we can control the browser with cypress we can also have access to cookies

```js
it('should set a cookie', () => {
	cy.getCookie('jwt').then(cookie => {
		const value = decodeToken(cookie);
		expect(value.email).to.equal(user.email);
	});
});
```
