# Classes and Interfaces

Classes are the descriptions of real life entities that we work with every day.

![Object orientated programming](assets/images/object-orientated-programming.jpg "Object orientated programming")

We can create objects from the class blueprint.

![Classes and instances](assets/images/classes-and-instances.jpg "Classes and instances")

Each instance of an object can contain different data but they have the same properties amd methods.

## Creating a first class

```
    class Department {
        name: string

        constructor(n: string) {
            this.name = n;
        }
    }

    const accounting = new Department('Accounting');

    console.log(accounting);
```

> Department {name: "Accounting"}

A constructor is is special method that gets called when an object is initialised. It supplies an initial value to each of the properties in the object.

If we look at the compiled JavaScript it is basically the same as what we created in TypeScript.

#### es6 JavaScript

```
    "use strict";
    class Department {
        constructor(n) {
            this.name = n;
        }
    }
    const accounting = new Department('Accounting');
    console.log(accounting);
    //# sourceMappingURL=app.js.map
```

Now we will change the *tsconfig.json* target to and earlier version of JavaScript, **es5**. And it is totally different. **es5** doesn't have classes so it creates a constructor function as a workaround.

#### es5 JavaScript

```
"use strict";
var Department = (function () {
    function Department(n) {
        this.name = n;
    }
    return Department;
}());
var accounting = new Department('Accounting');
console.log(accounting);
//# sourceMappingURL=app.js.map
```

**es6** developed the idea of classes to create a cleaner version of the object code.

The key point here is that we can create objects with either sets of code. If we downcompile to **es5** we can be assured of older browser support.

## Constructor functions & The "this" keyword

We can also add methods to our class definition. In the code below we describe the department.

This code contains an error. ``name`` is a global browser variable and is different to the name property we are using in the **Department** class.

We can get around this problem by using the ``this`` keyword.

```
    describe() {
        console.log(`Department: ${this.name}`);
    }
```

We use the ``this`` keyword on internal properties and methods to call this items within the class. Now we can describe our object instance.

```
    class Department {
        name: string

        constructor(n: string) {
            this.name = n;
        }

        describe() {
            console.log(`Department: ${this.name}`);
        }
    }

    const accounting = new Department('Accounting');

    console.log(accounting.describe());
```

> Department: Accounting

Anyone who has worked with JavaScript's ``this`` keyword would know that it can be tricky. For example I can add another accounting object.

```
    const accountingCopy = { describe: accounting.describe };
    accountingCopy.describe();
```

If I compile here I don't get an error but I get the following output.

> Department: undefined

What we are doing is creating a new object based on a dummy literal ``accounting.describe`` and the describe property on this new object ``describe:`` is a pointer to the accounting.describe method in the accounting object. So we are pointing at the ``describe()`` method on a concrete object and we are not passing the value of the method to ``describe:``. We are actually passing the method to describe. When we call ``accountingCopy.describe();`` it executes the method. The problem is that ``this.name`` will not point to the object, accounting. In this case ``this`` is responsible for calling ``accountingCopy`` so it is returns undefined.

We call ``describe`` on ``accountingCopy`` so it doesn't work properly. This happens in TypeScript as well as JavaScript.

There is a workaround to this problem. You can add a parameter to ``describe()``. ``this`` is a special parameter understood by TypeScript. It is basically a hint to what the method points to. It needs a type so you add the class name.

```
    describe(this: Department) {
        console.log(`Department: ${this.name}`);
    }
```

Now ``accountingCopy.describe();`` will fail because it is pointing to the wrong object.

**Note:** the ``describe(this: Department)`` method doesn't require a parameter. The ``this`` placeholder isn't a parameter and is being used by TypeScript.

## Private and Public access modifiers

We have a very simple class and now we will build on that class by adding more properties and methods.

```
    class Department {
        name: string;
        private employees: string[] = [];

        constructor(n: string) {
            this.name = n;
        }

        describe(this: Department) {
            console.log(`Department: ${this.name}`);
        }

        addEmployee(employee: string) {
            this.employees.push(employee);
        }

        changeDepartmentName(newName: string) {
            this.name = newName;
        }

        printEmployeeInformation() {
            console.log(this.employees.length);
            console.log(this.employees);
        }
    }

    const accounting = new Department('Accounting');
    accounting.addEmployee('Alan');
    accounting.addEmployee('James');
    accounting.addEmployee('Charley');

    accounting.describe();

    accounting.printEmployeeInformation();
    accounting.changeDepartmentName('Accounting101');
    accounting.describe();
```


> Department: Accounting        
> 3         
> (3) ["Alan", "James", "Charley"]

This is working nicely but there is an issue in that we can change employee values from inside the object instance (which we want to do) and from outside of the object instance. This means that anybody can change our object instance and this is not something we want other users to do.

For example we could add the code.

```
    accounting.employees[3] = 'Ethan';
```

This is legal but we want to stop developers from doing this. We want to make sure there is only one way to do this and that is the ``accounting.addEmployee()`` method. TypeScript has a method to stop this.

```
    private employees: string[] = [];
```

You use the ``private`` keyword to make the property only accessible from inside the object instance.

The ``private`` keyword is called a modifier.

Now, ``accounting.employees[3]`` brings up an error so we will have to remove it.

**Note:** you can also mark methods as private as well.

You also have a ``public`` modifier and this is the default so it isn't necessary. ``public`` modifiers are accessible from outside.

We could do this.

```
    accounting.name = 'New Name';
```

In our situation we probably don't want to do this so we could make the property ``private`` and this would leave us with no way of changing the department name. If we wanted to change the department name we should write a method to do this.

Note also that ``public`` and ``private`` are recent additions to JavaScript so in the past all properties were public.

If we look at the **es6** JavaScript that has been compiles it has no mention of private or public.

In our case TypeScript uses these modifiers for our code for checking but this feature is not available at runtime.

```
    class Department {
        private name: string;
        private employees: string[] = [];

        constructor(n: string) {
            this.name = n;
        }

        describe(this: Department) {
            console.log(`Department: ${this.name}`);
        }

        addEmployee(employee: string) {
            this.employees.push(employee);
        }

        changeDepartmentName(newName: string) {
            this.name = newName;
        }

        printEmployeeInformation() {
            console.log(this.employees.length);
            console.log(this.employees);
        }
    }

    const accounting = new Department('Accounting');
    accounting.addEmployee('Alan');
    accounting.addEmployee('James');
    accounting.addEmployee('Charley');

    console.log(accounting.describe());
    console.log(accounting.printEmployeeInformation());
    console.log(accounting.changeDepartmentName('Accounting101'));
    console.log(accounting.describe());
```

> Department: Accounting        
> 3     
> (3) ["Alan", "James", "Charley"]      
> Department: Accounting101

## Shorthand Initialisation

There are some shortcuts when creating classes. lets add another field to our class.

```
    class Department {
        id: string;
        name: string;
        private employees: string[] = [];

        constructor(i: string, n: string) {
            this.id = i;
            this.name = n;
        }

        // ...
```

There is an easier way to create properties.

```
    class Department {
        private employees: string[] = [];

        constructor(private id: string, private name: string) { };

        describe(this: Department) {
            console.log(`Department ${this.id}: ${this.name}.`);
        }

        // ...
```

Now, when we call the describe method we get the following result.

> Department a123: Accounting.

## "Readonly" properties

There is another access modifier, ``readonly``. These are used for when a property has been initialised and you don't want anyone to change that property.

```
    constructor(private readonly id: string, private name: string) { };
```

The readonly keyword has been introduced by TypeScript and isn't available in JavaScript. if you try to write to this property you will fail. This adds some extra type safety to our code and make our intentions clear.

## Inheritence

We have been able to create our class blueprint and work with it but imagine our department may have different needs. For example, the IT department needs a property for administrators and the accounting department has a need for reports.

We could have a base department class with properties and methods that all departments need butthen we have departments with their own properties and methods. **Inheritance** can help us implement something like this.

Imagine we changed the Department class in our example to the following.
```
class ITDepartment extends Department {

}

const accounting = new ITDepartment('a123', 'Accounting');
```

Even with this little bit of code we can still use our progam and it will return the same results because our new accounting class inherits the constructor and all of the properties and methods from the base Department class.

**Note:** Classes can only inherit from one other class.

You can add a constructor into your inherited class.

```
    class ITDepartment extends Department {
        constructor(id: string, public admins: string[]) {
            super(id, 'IT');
            this.admins = admins;
        }
    }

    const it = new ITDepartment('d1', ['Alan', 'James']);
```

In your inherited class you **MUST** have a ``super()`` function call. ``super()`` calls the constructor of the base class.

If we hover over the ``super()`` function we see that it takes an id and a name. We pass the inherited classes id to ``super()`` and we can hard code a value for the name. This will now call the constructor of the base class from inside the sub class.

**Note:** in your inherited class you can only add the ``super()`` call to the first line of the constructor. All other code for the constructor **MUST** be below the ``super()`` call.

**Note:** we are hard coding our department name in our ``super()`` call. It is not a parameter in our inherited constructor.

We can add extra properties into our constructor. In our case we added the ``admins`` array.

```
    constructor(id: string, public admins: string[]) {
        super(id, 'IT');
        this.admins = admins;
    }
```

We have to add an admins array into our inherited class call.

```
    const it = new ITDepartment('d1', ['Alan', 'James']);
```

This is our base and inherited class code.

```
    class Department {
        private employees: string[] = [];

        constructor(private readonly id: string, private name: string) { };

        describe(this: Department) {
            console.log(`Department ${this.id}: ${this.name}.`);
        }

        addEmployee(employee: string) {
            this.employees.push(employee);
        }

        changeDepartmentName(newName: string) {
            this.name = newName;
        }

        printEmployeeInformation() {
            console.log(this.employees.length);
            console.log(this.employees);
        }
    }

    class ITDepartment extends Department {
        constructor(id: string, public admins: string[]) {
            super(id, 'IT');
            this.admins = admins;
        }
    }

    const it = new ITDepartment('d1', ['Alan', 'James']);
    it.addEmployee('Alan');
    it.addEmployee('James');
    it.addEmployee('Charley');

    it.describe();

    it.printEmployeeInformation();
```

> Department d1: IT.        
> 3     
> (3) ["Alan", "James", "Charley"]

We can also log the complete department details.

```
    console.log(it);
```

> ITDepartment {        
>     id: "d1",         
>     name: "IT",       
>     employees: Array(3), admins: Array(2)     
>     }     
>     admins: (2) ["Alan", "James"]     
>     employees: (3) ["Alan", "James", "Charley"]       
>     id: "d1"      
>     name: "IT"        
>     __proto__: Department

Now we can create our accounting department inherited class.

```
    class AccountingDepartment extends Department {
        constructor(id: string, private reports: string[]) {
            super(id, 'Accounting');

        }

        addReport(text: string) {
            this.reports.push(text);
        };

        printReports() {
            console.log(this.reports);
        }
    }

    const accounting = new AccountingDepartment('d2', []);
    accounting.addEmployee('Fred');
    accounting.addEmployee('Mary');

    accounting.addReport('This is report 1.');
    accounting.addReport('This is report 2.');
    accounting.addReport('This is report 3.');

    accounting.describe();

    accounting.printEmployeeInformation();

    console.log(accounting);
```

> Department d2: Accounting.        
> 2     
> (2) ["Fred", "Mary"]      
> AccountingDepartment {        
>     id: "d2", name: "Accounting", employees: Array(2), reports: Array(3)      
> }     
> employees: (2) ["Fred", "Mary"]       
> id: "d2"name: "Accounting"        
> reports: (3) ["This is report 1.", "This is report 2.", "This is report 3."]      
> __proto__: Department

## Overriding properties & the "protected" modifier

We can create aour own methods that override the methods with the base class.

In the ITDepartment inherited class we can create the method ``addEmployees()`` that overrides the method in the base class.

```
    addEmployee(name: string) {
        if (this.name === 'Max') {
            return;
        }

        this.employees.push(name);
    }
```

This won't work because``employees`` is private in the base class .

**Important:** private properties are only accessible in the class that they were created.

We don't want to make ``employees`` in the base class **public** because we don't want to access the property from outside the class.

What we can do is make the property **protected** and then we will be able to access the property from the class that inherits from the base class.

**Note:** ``name`` is accessible in the inherited classes method because it has **public** access in the base class.

We can now add employees from the inherited class.

```
    const accounting = new AccountingDepartment('d2', []);
    accounting.addEmployee('Fred');
    accounting.addEmployee('Mary');
    accounting.addEmployee('Max');
    accounting.addEmployee('Manu');
```

> (3) ["Fred", "Mary", "Manu"]

We can see that we were using the overwritten method ``addEmployee()`` because the name *Max* doesn't appear in our list of employees.

## Getters and setters

### getters

We can add a getter to "get" a private value from our class. In the accounting class we could add a new private field named ``lastReport`` to  get a copy of the last report added.

```
    private lastReport: string;

    get getMostRecentReport() {
        if (this.lastReport) {
            return this.lastReport;
        }

        throw new Error('No report found.');
    }
```

The code below the property is the getter and it returns the last report only if it exists.

We have to access a report in our constructor.

```
    constructor(id: string, private reports: string[]) {
        super(id, 'Accounting');
        this.lastReport = reports[0];
    }
```

And also in the ``addReoprt()`` method.

```
    addReport(text: string) {
        this.reports.push(text);
        this.lastReport = text;
    };
```

Now we can access the last report by.

```
    const accounting = new AccountingDepartment('d2', []);

    console.log(accounting.getMostRecentReport);
```

This will throw an error because no report exists at present. You can see our custom error report below.

> Uncaught Error: No report found.      
>     at AccountingDepartment.get getMostRecentReport [as getMostRecentReport] (app.ts:39)      
>     at app.ts:76

We will add the ``getMostRecentReport()`` method call after we have created some reports.

```
    accounting.addReport('This is report 3.');

    console.log(accounting.getMostRecentReport);
```

> This is report 3.

**Note:** a getter and setter look like methods but they are still properties so to access them you don't use parenthesese.

## setters

We can also create a setter to add a new report.

```
    set setMostRecentReport(value: string) {
        if (!value) {
            throw new Error('Please add a new report!');
        }

        this.addReport(value);
    }
```

After we create an instance of accounting we can add a report.

```
    const accounting = new AccountingDepartment('d2', []);

    accounting.setMostRecentReport = 'This is a new report.';
```

> AccountingDepartment {        
>     id: "d2", name: "Accounting", employees: Array(3), reports: Array(4), lastReport: "This is report 3."     
> }     
> employees: (3) ["Fred", "Mary", "Manu"]       
> id: "d2"      
> lastReport: "This is report 3."       
> name: "Accounting"        
> reports: (4) ["This is a new report.", "This is report 1.", "This is report 2.", "This is report 3."]     
> getMostRecentReport: (...)

## Static methods and properties

Static methods and properties can be accessed not on an instance of an object. This means that you don't have to call ``new`` on a class but you access directly on the class. This is often used for utility methods that you want to use with a class. An example of a class that can be used to access a method would be.

```
    let pi = Math.PI

    // or

    let number = Math.pow(2);
```

You don't have to use a ``new`` instance of the Math class to access these methods and properties.  You can create your own static methods and properties in your own classes.

As an example, what if we want to directly call the Department class without instanciating an object of Department?

To create a method we use the ``static`` keyword in front of the method we are creating. The following code is a simple static method.

```
    static createEmployee(name: string) {
        return { name: name };
    }
```

We can call it with.

```
    const employee1 = Department.createEmployee('Alan');      
    console.log(employee1);
```

> {name: "Alan"}

This is handy to group methods or properties within a class.

We can also create a static property.

```
    class Department {
        static fiscalYear = 2021;
```

And call it with.

```
    const year = Department.fiscalYear;
    console.log(year);
```

> 2021

**Note:** One important point to remember is that you can't access your static methods and classes from your non static members.

This code produces an error.

```
    constructor(private readonly id: string, public name: string) {
        console.log(this.fiscalYear);
    };
```

## Abstract classes

An abstract method or abstract field is one that hasn’t had an implementation provided. These members must exist inside an abstract class, which cannot be directly instantiated.

The role of abstract classes is to serve as a base class for subclasses which do implement all the abstract members. When a class doesn’t have any abstract members, it is said to be concrete.

We can create an abstract method in Department and then create an implementation in the inherited class. Make sure the property that you are calling is not private but protected.

We are going to change the ``describe()`` method into an abstract method. First make ``id`` a protected property in the constructor.

```
    constructor(protected readonly id: string, public name: string) { };
```

Now change ``describe()`` to an abstract method by adding the keyword ``abstract`` and then removing the body of the method. We also have to add a return type.

```
    abstract describe(this: Department): void;
```

Now you have defined how your abstract method will look.

You will notice an error and that is the class that our abstracted method is in must be an abstract class. We can do this by changing the class name.

```
    abstract class Department {
```

Now we see that we have ended up with two more errors. We have to add a ``describe()`` method to both of our extended classes.

```
    class ITDepartment extends Department {
        constructor(id: string, public admins: string[]) {
            super(id, 'IT');
            this.admins = admins;
        }

        describe() {
            console.log(`Department ID: ${this.id}.`);
        }
    }
```

We can use the same method implementation in AccountingDepartment or we can create another implementation. In our case we'll use the same implementation.

```
    class AccountingDepartment extends Department {
        private lastReport: string;

        describe() {
            console.log(`Department ID: ${this.id}.`);
        }

        get getMostRecentReport() {
```

We can now see the changes in our output.

>{name: "Alan"}     
>2021       
>Department ID: d1.     
>3      
>(3) ["Charley", "Alan", "James"]       
>ITDepartment {id: "d1", name: "IT", employees: Array(3), admins: Array(0)}     
> This is report 3.     
>Department ID: d2.     
>3      
>(3) ["Fred", "Mary", "Manu"]       
> AccountingDepartment {id: "d2", name: "Accounting", employees: Array(3), reports: Array(4), lastReport:           
> "This is report 3."}

Another important note is that you can't instansiate the Department class anymore. Now it is basically just a class that is there to be inherited from.

## Singletons & Private Constructors