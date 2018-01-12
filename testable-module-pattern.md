# Testable Module Pattern

I follow this pattern to simplify the testing of a module.

## Problems to Address

1. Mocking Utilities

In my experience, mocking utilities usualy bring with them some overhead. If you understand how they work and what exactly they are doing, they are useful. But if you are creating code that could be maintained by someone who doesnt know these tools, it makes it difficult to get them spun up on writing tests and following TDD in general.
Also, I have found mocking utilities ususally encourage a design that is not easily tested in general and typically are harder to maintain.

2. Simplicity

This pattern helps maintain simplicity in the modules you create and help to make your tests understandable by the average passer-by.

3. Encourages Maintable and Extensible Module Design

Poorly designed modules suffer deeply when it comes time to add new functionality or when attempting to fix bugs. 

4. Testing

This doesnt really need an explination.

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


export default new MyService();
```

There are many cool things going on here.

* Dependancy Injection With Default Parameters
* `export` class for testing
* `export default` with instanstiated module

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