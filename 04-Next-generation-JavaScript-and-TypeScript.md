# Next generation JavaScript and TypeScript

## Arrow functions

```
    const add = (a: number, b: number) => {
        return a + b;
    };

    console.log(add(7, 6));
```

> 13

The arrow function syntax has some nice variations. We could shorten the function even more because there is only one statement in the function body.

```
    const add = (a: number, b: number) => a + b;

    console.log(add(7, 6));
```

> 13

Look at this code.

```
    const add = (a: number, b: number) => a + b;

    const printOutput = (output: (string | number)) => console.log(output);

    printOutput(add(7, 6));
```

Because there is only one parameter in the ``printOutput()`` function we can shorten the function itself.

```
    const add = (a: number, b: number) => a + b;

    const printOutput: (a: string | number) => void = output => console.log(output);

    printOutput(add(7, 6));
```

> 13

The issue here is that we are substituting shortness for readability. There is another use for using this shortened syntax.

```
    const button = document.querySelector('button');

    if (button) {
        button.addEventListener('click', event => { console.log(event) });
    }

    > MouseEvent {isTrusted: true, screenX: 907, screenY: 242, clientX: 900, clientY: 162, …}
```

**Note:** in an arrow function if you have NO parameters you HAVE TO use an empty pair of parentheses, ``() => {....}``.

### Default function parameters

In the ``add()`` function we know we need two numbers but we can provide a default parameter in case only one number is sent.

```
    const add = (a: number, b: number = 1) => a + b;

    const printOutput: (a: string | number) => void = output => console.log(output);

    printOutput(add(7));
```

> 8

**Note:** if a default number is used it should be the **last** parameter in the list.

## The spread operator

``spread`` is a new vanilla JavaScript operator.

```
    const hobbies = ['programming', 'reading', 'rowing'];
    const activeHobbies = ['hiking'];

    activeHobbies.push(...hobbies);

    console.log(activeHobbies);
```

> (4) ["hiking", "programming", "reading", "rowing"]

It is saying push all of the elements from one array (hobbies) onto the current array (activeHobbies).

Another way to do this is.

```
    const hobbies = ['programming', 'reading', 'rowing'];
    const activeHobbies = ['hiking', ...hobbies];

    console.log(activeHobbies);
```

> (4) ["hiking", "programming", "reading", "rowing"]

Produces the same result.

The ``spread`` operator also works with objects.

```
    const person = {
        name: 'Alan',
        age: 68
    }

    const anotherPerson = person;
```

This isn't creating another person it is actually creating a pointer to the ``person`` object in memory.

If we want to create a new person we can use the ``spread`` operator.

```
    const person = {
        name: 'Alan',
        age: 68
    }

    let anotherPerson = { ...person };

    anotherPerson.name = 'James';
    anotherPerson.age = 14;

    console.log(person);
    console.log(anotherPerson);
```

> {name: "Alan", age: 68}       
> {name: "James", age: 14}

I have changed the values in ``anotherPerson`` to prove that both objects are different.

## Rest parameters

``rest`` parameters allow you to take a list of values and use them as parameters

```
    const add = (...numbers: number[]) => {
        let result = 0;
        numbers.forEach((number) => {
            result += number;
        });

        return result;
    };

    const addNumbers = add(5, 7, 5.6, 3.4, 6);

    console.log(addNumbers);
```

> 27

Another way to do this is to use the ``reduce()`` function to merge a list of values.



```
    const add = (...numbers: number[]) => {
        return numbers.reduce((curResult, curValue) => {
            return curResult + curValue;
        }, 0);
    };

    const addNumbers = add(5, 7, 5.6, 3.4, 6);

    console.log(addNumbers);
```

> 27

This is a really handy routine to add a list of numbers.

## Array and Object destructuring

We want to be able to destructure an array and the longhanded way to do this is to create a new set of variables to hold the values in the array.

```
    const hobbies = ['programming', 'reading', 'rowing'];

    const hobbies1 = hobbies[0];
    const hobbies2 = hobbies[1];
    const hobbies3 = hobbies[2];
```

A better way is to use destructuring.

```
    const hobbies = ['programming', 'reading', 'rowing'];

    const [hobby1, hobby2, hobby3] = hobbies;
```

This assumes that you have to put enough variables in the destructured array to cover all of the items in the original array (hobbies).

You can destructure the remaining elements in the original array with.

```
    const [hobby1, ...remainingHobbies] = hobbies;

    console.log(hobby1);
    console.log(remainingHobbies);
```


> programming           
> (2) ["reading", "rowing"]

``remainingHobbies`` is a new array and the original array (hobbies) hasn't been changed.

You can also destructure objects.

```
    const person = {
        firstName: 'Alan',
        age: 68
    }

    const { firstName, age } = person;

    console.log(firstName);
    console.log(age);
```

> Alan      
> 68

Note that the names you use when you destructure an object have to be the same as the property names in your object.

You can override this by using the following code.

```
    const person = {
        firstName: 'Alan',
        age: 68
    }

    const { firstName: userName, age } = person;

    console.log(userName);
    console.log(age);
```

The colon in this case isn't for a type value it is just JavaScript syntax used to rename a variable.


## How code gets compiled

It is important to keep in mind that TypeScript will compile JavaScript based on the settings in your ``tsconfig.json`` file.

A lot of the code we covered in this section will compile down to JavaScript similar to what we used in our TypeScript because we are compiling to **es6** and that contains the newer features we were working with.

If you change the target to **es5** the compiled JavaScript will be much larger because TypeScript has to build workarounds for the newer TypeScript code we were using.
