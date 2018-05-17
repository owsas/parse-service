# Parse Service

This module exposes a class with static functions that help you find, save or delete Parse Objects with one call.

The motivation to use this package is because at some point you will need to test your code. Let's make an example with a function that adds two numbers:

```js
// add.js
import Parse from 'parse';

export function addTwoNumbers(a, b) {
  const obj = new Parse.Object('Addition');
  obj.set('total', a + b);
  return obj.save();
}
```
You will find that testing this function, with `jest` for example, is a little bit tricky. It is because the `save` function of the object is not easily spyable or mockable, because you are creating a new instance of a class.

The problem here is that the `save` function will try to call the network, and you don't really want that if you are just unit testing your code.

So to solve the presented problem, in your testing framework you would have to mock `Parse.Object` to return an instance which has a mocked `save` method.

Our given solution is to expose a class with static functions that you can easily spy and avoid network calls from your unit tests.

Let's see how the code changes:

```js
// add.js
import Parse from 'parse';
import { ParseService } from '@owsas/parse-service';

export function addTwoNumbers(a, b) {
  const obj = new Parse.Object('Addition');
  obj.set('total', a + b);
  return ParseService.save(obj); // the change is minimum, yet useful
}
```

As you can see, the `save` function from the object changed to `ParseService.save`, which is easily spyable in your testing framework. For example, in jest: 

```js 
// add.test.js
import { ParseService } from '@owsas/parse-service';
import { addTwoNumbers } from './add';

describe('#addTwoNumbers', () => {
  test('should add two numbers', async () => {
    // mock ParseService
    const mock = jest.spyOn(ParseService, 'save');
    mock.mockImplementationOnce(async obj => obj);

    // call the function
    const result = await addTwoNumbers(1,3);
    expect(result.get('total')).toEqual(4); // passes
  });
});
``` 

In the presented example, you can see that we mocked `ParseService`'s `save` function, and then tested that the returned object had the total set to the sum of 1 and 3.

In the same fashion, `ParseService` exposes static functions for:
* Finding results in a Parse.Query (`ParseService.find`),
* Finding the first result of a Parse.Query (`ParseService.first`),
* Saving objects (`ParseService.save`)
* Destroying objects (`ParseService.destroy`)


## Installation

```
npm install --save @owsas/parse-service
```

or with Yarn:
```
yarn add @owsas/parse-service
```

Please, make sure you have also installed `parse` in your project, because this package has no dependencies

## API

```ts
class ParseService {
  static find(
    query: Parse.Query, 
    options?: Parse.Query.FindOptions
  ): Parse.Promise<Parse.Object[]>;

  static first(
    query: Parse.Query, 
    options?: Parse.Query.FirstOptions,
  ): Parse.Promise<Parse.Object>;

  static save(
    obj:Parse.Object, 
    options: Parse.Object.SaveOptions
  ): Parse.Promise<Parse.Object>;

  static destroy(
    obj:Parse.Object, 
    options: Parse.Object.DestroyOptions
  ): Parse.Promise<Parse.Object>;
}
```

## Dev Mode
Clone this repo, and start adding your code in the `index.ts` file.  
When you are done, write the tests in the `index.test.ts` file. For testing, this repo works with [Jest](https://facebook.github.io/jest/).

Once you finished, you can publish your module to npm with `npm publish`. This will compile your Typescript code
and send it to npm.

Make sure to change the name of the package in `package.json`

## Dev Features
* Testing with Jest
* Linting out of the box (checks the style of your code), with TSLint
* Build, prepublish and other scripts to help you to develop
* Works with Typescript: Static typing for your JS Applications, reducing amount of runtime errors
* Coverage out of the box, thanks to Jest
* Uses deterministic module resolving, with Yarn

## Credits

Developed by Juan Camilo Guarín Peñaranda,  
Otherwise SAS, Colombia  
2018

## License 

MIT.

## Support us on Patreon
[![patreon](./repo/patreon.png)](https://patreon.com/owsas)
