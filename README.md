# Truffle Test Generator

A utility for creating new test files in a Truffle project.

## Install

```
npm i @audits/truffle-test-generator --save-dev
```

## Usage

Use Truffle to compile your contracts first.

```
truffle compile
```

Generate tests for a contract named MyContract.

```
npx ttg MyContract
```

_Replace MyContract with the name of the contract you want to create tests for._

## How It Works

The test generator will look at the json files located in the `./build/contracts` directory for the given contract name. As long as that contract exists and has been compiled, a test file will be created for it.

Each test will include a standard template for the beginning of the file, which includes `require`ing the artifact, declaring the contract test, and setting some common account variables.

```javascript
const MyChildContract = artifacts.require('MyChildContract')

contract('MyChildContract', (accounts) => {
  const maintainer = accounts[0]
  const user1 = accounts[1]
  const user2 = accounts[2]
  const stranger = accounts[3]

  let mychildcontract
```

While reading the contract's json file, it will also generate input parameters for your constructor. However, it will be up to you to update the values as your contract requires.

```javascript
// Be sure to update these constructor values
let _initialParentValue = 0
let _initialChildValue = 0

beforeEach(async () => {
  mychildcontract = await MyChildContract.new(_initialParentValue, _initialChildValue, {from: maintainer})
})
```

Any inherited contracts with external or public functions will have a test stub (as a describe block) created for it.

```javascript
describe('setValue', () => {

})
```

This allows you to easily have a structure for your tests and ensure that each external/public method will be tested, provided you write a test for it.

The test file will be placed in the `test` directory within your Truffle project and named `MyContract_test.js`, where MyContract is the name of the contract. In order to prevent a user from accidentally erasing an existing file, `-new` will be appended to the name and a new file will be created if the filename already exists. If the `-new` file exists, it will be overwritten.