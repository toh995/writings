# Part 2 - Changing Software
## Chapter 6 - I Don’t Have Much Time and I Have to Change It
Writing tests takes extra time, but in the long-run will end up saving time.

> When I work with teams, I often start by asking them to take part in an experiment. For an iteration, we try to make no change to the code without having tests that cover the change. If anyone thinks that they can’t write a test, they have to call a quick meeting in which they ask the group whether it is possible to write the test.

Suppose:
- You're adding net-new functionality to existing code
- The existing code is untested

Ideally, we would take the time to add tests for the old code.

However, if doing so is hard, and we have a time constraint, then we could add tests only for the new code, leaving the old code untested for now.

Here are some strategies to do this.

First, let's look at an example. Suppose we have an `Employee` class that pays employees. We want to add new functionality, to log payments.

```typescript
class Employee {
    pay(): void {
        let amount = 0;
        for (const timecard of this.timecards) {
            amount += (timecard.hours * this.payRate);
        }
        this.payDispatcher.pay(this, amount);
    }
}
```

### Sprout Method
Insert a **net-new** function. The old code invokes the new function.

The new function gets tests, but the old code/call-site does not.

```typescript
class Employee {
    // NET-NEW! Has new unit tests!
    logPayment(amount: number): void {}

    pay(): void {
        let amount = 0;
        for (const timecard of this.timecards) {
            amount += (timecard.hours * this.payRate);
        }
        this.payDispatcher.pay(this, amount);

        // NET-NEW!
        logPayment(amount);
    }
}
```

### Sprout Class
Same as sprout method, except you extract the net-new functionality into a new class instead of method.

Two reasons to use Sprout Class:
- New functionality **should** be extracted in a separate class
- It's too difficult to get the existing class into a test harness

```typescript
// NET-NEW! Has new unit tests!
class Logger {
    constructor(private employee: Employee) {}
    logPayment(amount: number) {}
}

class Employee {
    pay(): void {
        let amount = 0;
        for (const timecard of this.timecards) {
            amount += (timecard.hours * this.payRate);
        }
        this.payDispatcher.pay(this, amount);

        // NET-NEW!
        const logger = new Logger(this);
        logger.logPayment(amount);
    }
}
```

### Wrap Method
The idea here is to REPLACE the original method. See example:

```typescript
class Employee {
    pay(): void {
        // The original `pay` function was extracted into its own function!
        dispatchPayment();
        // Invoke the net-new code!
        logPayment();
    }

    dispatchPayment(): void {
        let amount = 0;
        for (const timecard of this.timecards) {
            amount += (timecard.hours * this.payRate);
        }
        this.payDispatcher.pay(this, amount);
    }

    // NET-NEW! Has new unit tests!
    logPayment(): void {}

}
```

This is essentially the same as the "Sprout Method" technique, except we perform the extra step of extracting the old code into its own function.

### Wrap Class
The idea here is to REPLACE the original class. See example:

```typescript
class Employee {
    pay(): void {
        let amount = 0;
        for (const timecard of this.timecards) {
            amount += (timecard.hours * this.payRate);
        }
        this.payDispatcher.pay(this, amount);
    }
}

// Throughout the codebase, replace any instance of `Employee` with `LoggingEmployee`
class LoggingEmployee extends Employee {
    constructor(private employee: Employee) {}

    // Preserve the same public-facing API as `Employee`
    pay(): void {
        // call the old code
        this.employee.pay();
        // call the net-new code
        this.logPayment();
    }

    // NET-NEW! Has unit tests!
    logPayment(): void {}
}
```

## Chapter 7 - It Takes Forever to Make a Change
Why does it take so long to make a change?

### Understanding
In large codebases, it takes a while to gain context, and understand the current system.

Some of this is unavoidable - the larger the codebase, the more context there is to gain.

> In a well-maintained system, it might take a while to figure out how to make a change, but once you do, the change is usually easy and you feel much more comfortable with the system. In a legacy system, it can take a long time to figure out what to do, and the change is difficult also.

SOLUTION:
> Systems that are broken up into small, well-named, understandable pieces enable faster work.

### Lag Time
> Lag time is the amount of time that passes between a change that you make and the moment that you get real feedback about the change.

One reason for large lag time - modules are all tightly coupled - i.e. the entire app MUST be compiled all together.

Ideally, we should be able to compile a single module in isolation. This allows rebuilds/tests to run much faster.

#### Breaking Dependencies
In this strategy, we establish abstract interfaces for our modules.

Then, we can implement "fake" modules inside of a test environment. This has the following advantages:
- Speeds up compile times for tests
- Makes code easier to change - we can swap out/edit the concrete implementations of these interfaces, as needed

##### Example
Suppose we have a software architecture like this:

![](./assets/figure-7.1.png)

The constructor for `AddOpportunityFormHandler` accepts two arguments: `ConsultantSchedulerDB` and `OpportunityItem`.

We can extract out an abstract interface for the two arguments:
![](./assets/figure-7.3.png)

We can further extract these out into packages:
![](./assets/figure-7.4.png)

> [!NOTE]
> This strategy increases *overall* compilation time, but decreases *local* compilation time.

## Chapter 8 - How Do I Add a Feature?
> In legacy code, one of the most important considerations is that we don’t have tests around much of our code. Worse, getting them in place can be difficult.

We are tempted to use the techniques in chapter 6 (I Don’t Have Much Time and I Have to Change It). For example, sprout and wrap techniques.

However, these don't add test coverage for existing legacy code.

It is better to add tests wherever possible.

We can use TDD:
> 0. Get the class you want to change under test.
> 1. Write a failing test case.
> 2. Get it to compile.
> 3. Make it pass. (Try not to change existing code as you do this.) 4. Remove duplication.
> 5. Repeat.

TDD is useful to help us incrementally improve the code design, while ensuring the features continue to work properly.

**The Liskov Substitution Principle (LSP)**
> The LSP implies that clients of a class should be able to use objects of a subclass without having to know that they are objects of a subclass.

How to avoid LSP violations:
> 1. Whenever possible, avoid overriding concrete methods.
> 2. If you do, see if you can call the method you are overriding in the overriding method.

## Chapter 9 - I Can't Get This Class into a Test Harness
> Here are the four most common problems we encounter:
> 1. Objects of the class can’t be created easily.
> 2. The test harness won’t easily build with the class in it.
> 3. The constructor we need to use has bad side effects.
> 4. Significant work happens in the constructor, and we need to sense it.

### The Case of the Irritating Parameter
Try constructing an object for a test, but the constructor has dependencies that are annoying to set up, or triggers unwanted side effects, or is just slow.

This is called an **irritating parameter**.

Ideas:
- Mock/fake the irritating parameter
- Pass in `null` for the parameter
- Subclass the irritating parameter, then override/remove the annoying code

### The Case of the Hidden Dependency
Inside a constructor for an object, we construct another object and save it as a dependency.

It can be tough to mock/fake this dependency.

We can refactor, such that the dependency becomes a constructor argument! (Parameterize Constructor)

### The Case of the Construction Blob
Similar to hidden dependency, except the dependency is used as a dependency for other objects.

We can use "Supersede Instance Variable", i.e. something like:

> We write a setter on the class that allows us to swap in another instance after we construct the object.
```
class WatercolorPane {
    public:
    WatercolorPane(Form *border, WashBrush *brush, Pattern *backdrop) {
        ...
        anteriorPanel = new Panel(border);
        anteriorPanel->setBorderColor(brush->getForeColor());
        backgroundPanel = new Panel(border, backdrop);
        cursor = new FocusWidget(brush, backgroundPanel);
        ... 
    }
    void supersedeCursor(FocusWidget *newCursor) {
        delete cursor;
        cursor = newCursor;
    }
}
```

### The Case of the Irritating Global Dependency
Many times, the global state is a singleton object
- Add a way to mock/fake the singleton
- Move the global dependency to be a parameter of the constructor

### The Case of the Onion Parameter
> ...we end up having to create objects to create objects to create objects to create a parameter for a constructor of the class that we want to test. Objects inside of other objects—it seems like a big onion. 
- Pass in `null` for the parameter
- Mock/fake the parameter

### The Case of the Aliased Parameter
Subclass and override

## Chapter 10 - I Can’t Run This Method in a Test Harness
> Here are some of the problems that we can run into.
> - The method might not be accessible to the test. It could be private or have some other accessibility problem.
> - It might be hard to call the method because it is hard to construct the parameters we need to call it.
> - The method might have bad side effects (modifying a database, launching a cruise missile, and so on), so it is impossible to run in a test harness.
> - We might need to sense through some object that the method uses.

### The Case of the Hidden Method
If a method is private
- Try to test that method via an existing public method (BEST)
- Make the private method public
    - Potentially extract the method into a separate utility module
    - The original module can hold a private reference to this new module
- Make the private method protected, then subclass inside of a test, and invoke the protected method
