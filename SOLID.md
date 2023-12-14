# SOLID Principles in Software Design

## Introduction

SOLID is an acronym for five principles of object-oriented programming and design, introduced by Robert C. Martin. These principles help developers create software that is easy to maintain, extend, and refactor, and they are integral to agile or adaptive software development.

-   **S**ingle Responsibility Principle (SRP)
-   **O**pen/Closed Principle (OCP)
-   **L**iskov Substitution Principle (LSP)
-   **I**nterface Segregation Principle (ISP)
-   **D**ependency Inversion Principle (DIP)

Applying SOLID principles leads to cleaner, more modular code, and a system that is easier to understand, modify, and maintain. They provide a foundation for avoiding common design pitfalls and creating resilient software.

## Single Responsibility Principle (SRP)

### Definition

The Single Responsibility Principle states that a class should have only one reason to change. This means that a class should only have one job or responsibility. By limiting the responsibility of a class, we encapsulate the features and minimize the number of changes that are necessary for any future feature developments or refactoring efforts.

### Examples

#### ❌ Incorrect way (violating SRP):

```typescript
class CustomerManager {
    private customer: Customer | undefined;

    // 📝 Customer related methods
    setCustomer(customer: Customer) {
        this.customer = customer;
    }

    getCustomer(): Customer | undefined {
        return this.customer;
    }

    // ❌ Session related methods
    setSession(session: Session) {
        this.session = session;
    }

    invalidateSession() {
        this.session = undefined;
    }
}
```

#### ✅ Correct way (following SRP):

```typescript
class CustomerManager {
    private customer: Customer | undefined;

    setCustomer(customer: Customer) {
        this.customer = customer;
    }

    getCustomer(): Customer | undefined {
        return this.customer;
    }
}

class SessionManager {
    private session: Session | undefined;

    setSession(session: Session) {
        this.session = session;
    }

    invalidateSession() {
        this.session = undefined;
    }
}
```

## Open/Closed Principle (OCP)

### Definition

The Open/Closed Principle (OCP) asserts that software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification. This means we should be able to add new features or components to the application without altering established code. This principle enhances the maintainability and reusability of the code, reducing potential errors from changing existing code.

```ts
interface NotificationProviderRepository {
    initialize: VoidFunction;
    register: (customerId: string) => void;
    unregister: VoidFunction;
}

class NotificationProviderOneSignalImpl implements NotificationProviderRepository {
    public initialize = () => {
        // logic here
    };

    public register = (customerId: string) => {
        // logic here
    };

    public unregister = () => {
        // logic here
    };
}

class NotificationProvider {
    static #instance: NotificationProviderRepository;

    public static get instance(): NotificationProviderRepository {
        if (!NotificationProvider.#instance) {
            NotificationProvider.#instance = new NotificationProviderOneSignalImpl();
        }

        return NotificationProvider.#instance;
    }
}

// Closed for modification - We don't need to change it when want to switch to a different notification provider.

// Open for extension - create new classes (like NotificationProviderOneSignalImpl) that implement this interface.

// Conclusion - We can easily swap the provider without affecting the consumer of the `NotificationProvider`
```

## Liskov Substitution Principle (LSP)

### Definition

The Liskov Substitution Principle (LSP) is a principle of object-oriented design that states if a program is using a base class, it should be able to use any of its subclasses without the program knowing it. In other words, the subclasses should be substitutable for their base class without altering the correctness of the program. This principle ensures that a derived class does not affect the behavior of the base class, thus enforcing consistency across subclasses.

#### ❌ Incorrect way (violating LSP):

```ts
class Bird {
    fly() {
        console.log('Flying...');
    }
}

class Duck extends Bird {}

class Ostrich extends Bird {
    fly() {
        throw new Error("I can't fly!");
    }
}

function makeBirdFly(bird: Bird) {
    bird.fly();
}

const duck = new Duck();
makeBirdFly(duck); // OK

const ostrich = new Ostrich();
makeBirdFly(ostrich); // Error: I can't fly!
```

#### ✅ Correct way (following LSP):

```ts
interface FlyingBird {
    fly(): void;
}

class Bird {}

class Duck extends Bird implements FlyingBird {
    fly() {
        console.log('Flying...');
    }
}

class Ostrich extends Bird {
    // Ostriches can't fly, so we don't implement the FlyingBird interface
}

function makeBirdFly(bird: FlyingBird) {
    bird.fly();
}

const duck = new Duck();
makeBirdFly(duck); // OK

const ostrich = new Ostrich();
// makeBirdFly(ostrich); // This would now cause a TypeScript error
```

## Interface Segregation Principle (ISP)

### Definition

The Interface Segregation Principle (ISP) is a principle of object-oriented design that advocates for many small, specific interfaces instead of one large, general interface. It states that clients should not be forced to depend on interfaces they do not use, leading to a system that's easier to refactor, change, and understand. This principle helps prevent changes in one part of a system from causing unexpected side effects in other parts, making your code more flexible and adaptable to future changes.

### Examples

```ts
// ❌ Instead of one large interface
interface User {
    manageAccount(): void;
    viewReports(): void;
}

// ✅ We segregate it into two smaller, more specific interfaces
interface AccountManager {
    manageAccount(): void;
}

interface ReportViewer {
    viewReports(): void;
}

// Now, our classes can implement only the interfaces they need
class Admin implements AccountManager, ReportViewer {
    manageAccount() {
        console.log('Admin is managing account');
    }

    viewReports() {
        console.log('Admin is viewing reports');
    }
}

class BusinessOwner implements ReportViewer {
    viewReports() {
        console.log('Business owner is viewing reports');
    }
    // Business owners don't manage accounts, so we don't implement AccountManager
}

class Employee implements AccountManager {
    manageAccount() {
        console.log('Employee is managing account');
    }
    // Employees don't view reports, so we don't implement ReportViewer
}

function performAction(user: AccountManager | ReportViewer) {
    if ('manageAccount' in user) {
        user.manageAccount();
    }

    if ('viewReports' in user) {
        user.viewReports();
    }
}

const admin = new Admin();
const owner = new BusinessOwner();
const employee = new Employee();

performAction(admin); // Outputs: "Admin is managing account" and "Admin is viewing reports"
performAction(owner); // Outputs: "Business owner is viewing reports"
performAction(employee); // Outputs: "Employee is managing account"
```

## Dependency Inversion Principle (DIP)

### Definition

Principle suggests that we should decouple our code by depending on abstractions rather than concrete implementations. This leads to more flexible, reusable, and easier-to-change code. It's a key principle for managing dependencies within a system, and it's often achieved using techniques like dependency injection.

### Examples

```ts
interface NotificationProviderRepository {
    initialize: VoidFunction;
    register: (customerId: string) => void;
    unregister: VoidFunction;
}

class NotificationProviderOneSignalImpl implements NotificationProviderRepository {
    public initialize = () => {
        // logic here
    };

    public register = (customerId: string) => {
        // logic here
    };

    public unregister = () => {
        // logic here
    };
}

class NotificationProviderTwoSignalImpl implements NotificationProviderRepository {
    public initialize = () => {
        // logic here
    };

    public register = (customerId: string) => {
        // logic here
    };

    public unregister = () => {
        // logic here
    };
}

class NotificationProvider {
    static #instance: NotificationProviderRepository;

    public static get instance(): NotificationProviderRepository { ✅
        if (!NotificationProvider.#instance) {
            NotificationProvider.#instance = new NotificationProviderOneSignalImpl();
        }

        return NotificationProvider.#instance;
    }

    public static get instance(): NotificationProviderOneSignalImpl { ❌
        if (!NotificationProvider.#instance) {
            NotificationProvider.#instance = new NotificationProviderOneSignalImpl();
        }

        return NotificationProvider.#instance;
    }

    public static get instance(): NotificationProviderTwoSignalImpl { ❌
        if (!NotificationProvider.#instance) {
            NotificationProvider.#instance = new NotificationProviderTwoSignalImpl();
        }

        return NotificationProvider.#instance;
    }
}

// We should always decouple the dependency away from the specific implementation
```
