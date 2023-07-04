# Testing React with Jest and Testing library

Once you create a react application it comes with a test suit set up to run our test with Testing Library we do it by typing the command ```npm test``` and this is how our suit would look like

```js
import {render,screen} from '@testing-library/react';
import App from './App';

test('renders learn react link', () => {
	// render creates a virtual DOM for testing
	render(<App/>);
	// access the virtual DOM via screen global
	const linkElement = screen.getByText(/learn react/i);
	// assert what the result should expect
	expect(linkElement).toBeInTheDocument();
});
```

### Testing Behavior

To test behavior and interactions you need to get the element and trigger events programmatically

```js
test('should change button colors', () => {
	render(<App/>);
	const colorButton = screen.getByRole('button', {name: 'Change to blue'});
	expect(colorButton).toHaveStyle({ backgroundColor: 'red' });
	// trigger click
	fireEvent.click(colorButton);
	expect(colorButton).toHaveStyle({ backgroundColor: 'blue' });
	expect(colorButton).toHaveTextContent('Change to red');
});
```

There are many ways to trigger events with the ```fireEvent``` utility, we could call the the utility and then pass the element alongside with the event ```fireEvent(node: HTMLElement, event: Event)``` or as the example shown previously.

### Testing functions

In some case we'd need functions that are separate from our components because they're going to be used by several components and we'd just import it and use it in our tests

```js
export function replaceCamelWithSpaces(colorName) {
	return colorName.replace(/\B([A-Z])\B/g);
}

test('Works for no inner capital letters', () => {
	expect(replaceCamelWithSpaces('Red')).toBe('Red');
});

test('Works for multiple inner capital letters', () => {
	expect(replaceCamelWithSpaces('MediumVioletRed')).toBe('Medium Violet Red');
});
```

### userEvent & query methods

React testing library provides us with an userEvent object with this object the events return a promise to be awaited

```js
import userEvent from '@testing-library/user-event';

test('popover responds to hover', async () => {
	const user = userEvent.setup();
	render(<SummaryForm/>);
	// popover starts out hidden
	const nullPopover = screen.queryByText(/no ice cream will be delivered/i);
	expect(nullPopover).not.toBeInTheDocument();
	// popover appears on mouseover of checkbox label
	const termsAndConditions = screen.getByText(/terms and conditions/i);
	await user.hover(termsAndConditions);
	const popover = screen.getByText(/no ice cream will be delivered/i);
	expect(popover).toBeInTheDocument();
	// popover disappears when mouse out
	await user.unhover(termsAndConditions);
	expect(popover).not.toBeInTheDocument();
});
```

# Mock service worker

msw is a library that allows us to mock server calls without the need to make requests

```js
// mocks file
import {setupServer} from 'msw/node';
import {handlers} from './handlers';
export const server = setupServer(...handlers);

// another test file
// establish API mocking
beforeAll(() => server.listen());

afterEach(() => server.resetHandlers());

afterAll(() => server.close());
```

With this setup all of the requests are gonna be intercepted by the msw in our tests and we need to mock/simulate the responses and assert how out application is going to react to the responses.

```js
test('handles error for scoops and topping routes', async () => {
	server.resetHandlers(
		// 500 server error
		rest.get('http://mock-endpoint', (req, res, ctx) => res(ctx.status(500)))
	)
	render(<OrderEntry />);
	const alerts = await screen.findByAllRole('alert', {name:'Unexpected error ocurred'});
	expect(alerts).toHaveLength(1);
});
```

# Testing components wrapped in a Provider

Adding context to the Test suite

```js
import {OrderDetailsProvider} from 'OrderDetails';

// wrapper to render our component with a provider
const renderWithContext = (ui, options) => render(io, {
	wrapper: OrderDetailsProvider, ...options
});

test('render with providers', () => {
	renderWithContext(<OrderEntry />);
});
```

With unit testing we're validating that our code is doing what it is supposed to be doing, so with this tools we have to have in mind that testing is a lightweight abstraction so it is reccomended to mock values and request, simulate user interaction triggering events and many other cases.
