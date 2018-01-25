# Testable Module Pattern

I follow this pattern to simplify the testing of a module.

## Problems to Address

1. Mocking Utilities

In my experience, mocking utilities usualy bring with them some overhead. If you understand how they work and what exactly they are doing, they are useful. But if you are creating code that could be maintained by someone who doesnt know these tools, it makes it difficult to get them spun up on writing tests and following TDD in general. Additionally, if we wish to follow the priciple that the tests should document the code, adding a nothing layer of obstraction to the tests through the use of mocking utilities, just makes the test less documenting. For this reason, I perfer to write all my mocks in each of the tests, or in a `beforeEach()` in the same context as the test. This makes it as easy for the reader to make sense of what is going on. All the information is right there in the file they are looking at.
Also, I have found mocking utilities ususally encourage a design that is not easily tested in general and typically are harder to maintain.

2. Simplicity

This pattern helps maintain simplicity in the modules you create and as mentioned before, helps make your tests understandable by the average passer-by.

3. Modularity

This pattern encourages the design of modules that have low coupling. Coupling is the degree of interdependacy between two logical units (Classes, Modules, Functions, etc...). When we set up the injection of these dependacies throught the constructor, we can easily switch out those dependacies with something else, as long as the replacement adheres to the interface provided by the dependacy being replaced. Also, if you follow TDD, you will have great documentation on what the interface should look like for each of the dependacies by looking at the mocked dependacies.

## The Pattern

ES6 brought with it the class keyword. This pattern uses classes and specifically the constructor to help with testing.


`MyService.js`
```javascript
import fs from 'fs';

export class MyService{

    constructor(fs = fs){
        this.fs = fs;
    }

    useFS(filename){
        this.fs.write(filename);
    }
}

```

There are two cool things going on here.

* Dependancy Injection With Default Parameters
* `export` class for testing and use

The dependacy injection is important for testing purposes. `fs` is a module that has a lot of code behind it. It also interacts with an external system (the operating system). Both of these things would require a special mocking utility to allow us to test only our module.


`MyService.unit.test.js`
```javascript

import { MyService } from './MyService';

describe('MyService', ()=>{
    describe('useFS()', ()=>{
        it('should call the fs.write() method.', ()=>{
            // set up helper variables.
            let called = false;
            let filename = 'test_file';

            // set up mock dependacy
            let mockFS = {
                write: (file)=>{
                    expect(file).toEqual(filename);
                    called = true;
                }
            }

            // create specimen
            let specimen = new MyService(mockFS);

            // call function
            specimen.useFS(filename);

            // make assertions
            expect(called).toEqual(true);
        });
    });
});

```

To use the module in another part of your project it is fairly simple. Just instantiate the object like you would normally.

```javascript

import MyServuce from './MyService';

let filename = 'test-file.js';
let myService = new MyService();

myService.useFS(filename);

```

This works because of the default parameters. Another pattern you can follow if you wish to import an instantiated object into other modules, simply add a `default export new MyService()` at the bottom of your service module.

