# Design Patterns in TypeScript

this Document is based on courses/books/articls, and I see this is more than inough to learn _OOP design Patterns_

> If you like this summary and are interested in more, you can follow me on [LinkedIn](https://www.linkedin.com/in/kareem-anees-0496b62b3) or [Facebook](https://www.facebook.com/TheyCallMeAbdo) or [My website](https://real-kareem.space) .

---

## Table of content

- [Design Patterns in TypeScript](#design-patterns-in-typescript)
  - [_Singleton_](#singleton)
    - [Benefits of the Singleton Pattern](#benefits-of-the-singleton-pattern)
    - [Common Uses of the Singleton Pattern](#common-uses-of-the-singleton-pattern)
  - [_Factory Pattern_](#factory-pattern)
    - [Example: Cats 🐈](#example-cats-🐈)
  - [_Abstract Factory Pattern_](#abstract-factory-pattern)
    - [Example](#example-1)
  - [_Builder Pattern_](#builder-pattern)
    - [Example: Himalayan Cat Builder](#example-himalayan-cat-builder)
    - [Usage](#usage)
    - [When to Use the Builder Pattern](#when-to-use-the-builder-pattern)
  - [_Object Pool Pattern_](#object-pool-pattern)
    - [Example: Cat Object Pool](#example-cat-object-pool)
    - [Common Uses of Object Pools](#common-uses-of-object-pools)
  - [_Dependency Injection (DI)_](#dependency-injection-di)
    - [Example: IOC Container](#example-ioc-container)
    - [Advantages of Dependency Injection](#advantages-of-dependency-injection)
  - [Decorator Pattern](#decorator-pattern)
    - [Use Case: Retry Logic with Decorators](#use-case-retry-logic-with-decorators)
    - [Use Case: Input Validation with Decorators](#use-case-input-validation-with-decorators)
  - [Adapter Pattern](#adapter-pattern)
  - [Facade Pattern](#facade-pattern)
  - [Proxy Pattern](#proxy-pattern)
  - [Chain of Responsibility](#chain-of-responsibility)
  - [Iterator Pattern](#iterator-pattern)
  - [Observer Pattern](#observer-pattern)
- [The End](#the-end)

---

## [_Singleton_]()

The Singleton pattern ensures that only one instance of a class is created throughout the application.

```ts
class Singleton {
  private static instance: Singleton;

  constructor() {
    if (Singleton.instance) {
      return Singleton.instance;
    }
    Singleton.instance = this;

    // Initialize the instance normally
  }

  public static getInstance(): Singleton {
    return Singleton.instance;
  }
}
```

This approach guarantees that only a single instance of the class is initialized.

### **Benefits of the Singleton Pattern**

- **Statefulness**: A single instance holds shared state across the system.
- **One-time Initialization**: Ensures resources are initialized only once.

### **Common Uses of the Singleton Pattern**

1. Configuration Management
2. Logging
3. Database Connections
4. Thread Pool Management
5. Caching
6. Service Locators
7. Multithreading Control

> **In general:** Use the Singleton pattern for centralized, state-holding entities or services.

---

## [_Factory Pattern_]()

The Factory pattern creates objects without exposing the instantiation logic to the client. It lets you define interfaces for the types of objects being created.

### Example with Cats 🐈

```ts
// Define interfaces
interface HimalayanCat {
  color: string;
  fur: string;
  size: string;
}

interface PersianCat {
  color: string;
  fur: string;
  size: string;
}
```

> **Why interfaces matter:** Users focus on **interfaces**, not the class structure. Using classes directly might cause issues when adding new types.

```ts
// Factory class
class CatsFactory {
  public static createHimalayanCat(): HimalayanCat {
    return { color: "white", fur: "long", size: "large" };
  }

  public static createPersianCat(): PersianCat {
    return { color: "black", fur: "short", size: "small" };
  }
}
```

**Advantages**:

- Promotes **loose coupling** by decoupling the creation logic from the usage.

---

## [_Abstract Factory Pattern_]()

The Abstract Factory pattern provides an interface to create families of related or dependent objects without specifying their concrete classes.

### Example

```ts
class CatFactory {
  static createCat(type: string): HimalayanCat | PersianCat {
    if (type === "Himalayan") {
      return { color: "white", fur: "long", size: "large" };
    } else if (type === "Persian") {
      return { color: "black", fur: "short", size: "small" };
    } else {
      throw new Error("Invalid cat type");
    }
  }
}
```

**Why use it?**  
Instead of forcing users to know the exact implementation details (e.g., specific interfaces), you provide them with what they need dynamically.

---

## [_Builder Pattern_]()

The Builder pattern simplifies the creation of complex objects by constructing them step-by-step.

### Example: Himalayan Cat Builder

```ts
class HimalayanCatBuilder {
  private color: string;
  private fur: string;
  private size: string;

  setColor(color: string): HimalayanCatBuilder {
    this.color = color;
    return this;
  }

  setFur(fur: string): HimalayanCatBuilder {
    this.fur = fur;
    return this;
  }

  setSize(size: string): HimalayanCatBuilder {
    this.size = size;
    return this;
  }

  build(): HimalayanCat {
    return { color: this.color, fur: this.fur, size: this.size };
  }
}
```

### Usage

```ts
const himalayanCat = new HimalayanCatBuilder()
  .setColor("white")
  .setFur("long")
  .setSize("large")
  .build();
```

### **When to Use the Builder Pattern**

1. When the object is **complex** and requires multiple parameters.
2. When the object must be **constructed in multiple stages**, such as in [_Function Currying_](https://github.com/RealKareemAnees/GoFunctionalProgramming#function-currying).

---

## [_Object Pool Pattern_]()

The Object Pool pattern is used to manage reusable objects, especially when object creation is expensive.

### Example: Cat Object Pool

```ts
class Cat {
  public name: string;
  constructor(name: string) {
    this.name = name;
    console.log("Cat created");
  }
}

class CatsObjectPool {
  private static _instance: CatsObjectPool;
  private _cats: Cat[] = [];
  private poolSize: number = 10;

  private constructor() {}

  public static get instance() {
    if (!this._instance) {
      this._instance = new CatsObjectPool();
    }
    return this._instance;
  }

  public loadCatsPool() {
    for (let i = 0; i < this.poolSize; i++) {
      this._cats.push(new Cat("Cat" + i));
    }
  }

  public getCat(): Cat | null {
    if (this._cats.length === 0) {
      console.log("No cats available");
      return null;
    }
    return this._cats.pop();
  }
}
```

**Key Idea**: Create a set of reusable objects instead of instantiating them repeatedly.

### **Common Uses of Object Pools**

1. Thread Pooling
2. Memory-Intensive Object Management

> **When to use**: Whenever objects are expensive to create or destroy, and you need to retrieve them dynamically.

---

## [_Dependency Injection (DI)_]()

Dependency Injection (DI) is a design pattern that removes hard-coded dependencies, allowing code to depend on **abstractions (interfaces)** rather than concrete implementations.

### Example: IOC Container

```ts
class IOCContainer {
  private _services: { [key: string]: any } = {};

  register<T>(
    name: string,
    service: new (...args: any[]) => T,
    dependencies: string[] = []
  ): void {
    this._services[name] = new service(
      ...dependencies.map((d) => this.resolve(d))
    );
  }

  resolve<T>(name: string): T {
    return this._services[name];
  }
}

// Usage

class A {
  constructor() {
    console.log("A created");
  }

  doSomething() {
    console.log("A did something");
  }
}

class B {
  constructor(private a: A) {
    console.log("B created");
  }

  doSomething() {
    this.a.doSomething();
  }
}

const container = new IOCContainer();

container.register("a", A);
container.register("b", B, ["a"]);

const a = container.resolve<A>("a");
const b = container.resolve<B>("b");

a.doSomething();
b.doSomething();
```

### **Advantages of DI**

- Promotes **modularity** by decoupling dependencies.
- Enables easier **testing** by substituting implementations.
- Makes code more **flexible** and **extensible**.

---

## [Decorator Pattern]()

The **Decorator Pattern** is a structural design pattern that implements **polymorphism** by extending a class and altering its behavior dynamically. Here's an example:

```ts
// Base Component
class Notifier {
  send(message: string): void {
    console.log(`Basic Notification: ${message}`);
  }
}

// Decorator Base Class
class NotifierDecorator extends Notifier {
  protected notifier: Notifier;

  constructor(notifier: Notifier) {
    super();
    this.notifier = notifier;
  }

  send(message: string): void {
    this.notifier.send(message); // Delegate to the original notifier
  }
}

// Concrete Decorator: Adds Email Notifications
class EmailNotifier extends NotifierDecorator {
  send(message: string): void {
    super.send(message); // Call the base notification
    console.log(`Email Notification: ${message}`);
  }
}

// Concrete Decorator: Adds SMS Notifications
class SMSNotifier extends NotifierDecorator {
  send(message: string): void {
    super.send(message); // Call the base notification
    console.log(`SMS Notification: ${message}`);
  }
}

// Client Code
const basicNotifier = new Notifier();
const emailNotifier = new EmailNotifier(basicNotifier);
const smsNotifier = new SMSNotifier(emailNotifier);

// Send notifications
smsNotifier.send("Hello, world!");
```

### Use Case: Retry Logic with Decorators

The decorator pattern can be extended to include **retry logic** using decorators in TypeScript 5:

```ts
function retry(times: number) {
  return function (
    target: any,
    propertyKey: string,
    descriptor: PropertyDescriptor
  ) {
    const originalMethod = descriptor.value;
    descriptor.value = function (...args: any[]) {
      let attempt = 0;
      while (attempt < times) {
        try {
          return originalMethod.apply(this, args);
        } catch (error) {
          attempt++;
          if (attempt === times) {
            throw error;
          }
        }
      }
    };
  };
}

// Usage
class S {
  @retry(3)
  print() {
    console.log("Hello World");
    throw new Error("Error");
  }
}

const s = new S();
s.print();
```

**Expected Output**:

```plaintext
Hello World
Hello World
Hello World
Error: Error
```

### Use Case: Input Validation with Decorators

Another common use case is **validating method inputs**:

```ts
function validateInput(validator: (args: any[]) => boolean) {
  return function (
    target: any,
    propertyKey: string,
    descriptor: PropertyDescriptor
  ) {
    const originalMethod = descriptor.value;
    descriptor.value = function (...args: any[]) {
      if (!validator(args)) {
        throw new Error("Invalid input");
      }
      return originalMethod.apply(this, args);
    };
  };
}

// Usage
class S {
  @validateInput((args) => args[0] === "hi")
  public static print(input: any) {
    console.log(input);
  }
}

S.print("hi"); // Outputs: hi
S.print("hello"); // Throws: Error: Invalid input
```

---

## [Adapter Pattern]()

The **Adapter Pattern** bridges the gap between an expected interface and an existing class. Instead of modifying the existing class, an adapter wraps it to provide compatibility.

> **When to Use:** When there is a slight difference between interfaces. For major interface mismatches, consider using a **proxy**.

```ts
// Old System (LegacyPrinter)
class LegacyPrinter {
  oldPrint(message: string) {
    console.log(`Legacy Printer: ${message}`);
  }
}

// New System (Printer)
class Printer {
  print(message: string) {
    console.log(`New Printer: ${message}`);
  }
}

// Adapter to make LegacyPrinter compatible with Printer
class PrinterAdapter extends Printer {
  private legacyPrinter: LegacyPrinter;

  constructor(legacyPrinter: LegacyPrinter) {
    super();
    this.legacyPrinter = legacyPrinter;
  }

  // Adapt the old method to the new interface
  print(message: string) {
    this.legacyPrinter.oldPrint(message); // Calls the old system's method
  }
}

// Usage
const legacyPrinter = new LegacyPrinter(); // Old system's object
const adaptedPrinter = new PrinterAdapter(legacyPrinter); // Adapter

// Using the new interface, but calling the legacy system's method
adaptedPrinter.print("Hello from the Adapter!");
```

---

## [Facade Pattern]()

The **Facade Pattern** provides a simplified interface to a complex subsystem.

```ts
// Subsystems
class TV {
  turnOn() {
    console.log("TV is on");
  }
  turnOff() {
    console.log("TV is off");
  }
}

class SoundSystem {
  turnOn() {
    console.log("Sound system is on");
  }
  turnOff() {
    console.log("Sound system is off");
  }
}

class DVDPlayer {
  turnOn() {
    console.log("DVD player is on");
  }
  play(movie: string) {
    console.log(`Playing movie: ${movie}`);
  }
  turnOff() {
    console.log("DVD player is off");
  }
}

// Facade
class HomeTheaterFacade {
  private tv: TV;
  private soundSystem: SoundSystem;
  private dvdPlayer: DVDPlayer;

  constructor() {
    this.tv = new TV();
    this.soundSystem = new SoundSystem();
    this.dvdPlayer = new DVDPlayer();
  }

  watchMovie(movie: string) {
    console.log("Get ready to watch a movie...");
    this.tv.turnOn();
    this.soundSystem.turnOn();
    this.dvdPlayer.turnOn();
    this.dvdPlayer.play(movie);
  }

  endMovie() {
    console.log("Shutting down home theater...");
    this.tv.turnOff();
    this.soundSystem.turnOff();
    this.dvdPlayer.turnOff();
  }
}

// Client
const homeTheater = new HomeTheaterFacade();
homeTheater.watchMovie("Inception");
homeTheater.endMovie();
```

---

## [Proxy Pattern]()

The **Proxy Pattern** acts as an intermediary for another object, controlling access or adding functionality. It's commonly used for **lazy initialization**, **access control**, **logging**, or **caching**.

```ts
// Subject
interface Image {
  display(): void;
}

// RealSubject
class RealImage implements Image {
  private filename: string;

  constructor(filename: string) {
    this.filename = filename;
    this.loadFromDisk(); // Heavy operation
  }

  private loadFromDisk() {
    console.log(`Loading image: ${this.filename}`);
  }

  display() {
    console.log(`Displaying image: ${this.filename}`);
  }
}

// Proxy
class ImageProxy implements Image {
  private realImage: RealImage | null = null;
  private filename: string;

  constructor(filename: string) {
    this.filename = filename;
  }

  display() {
    if (!this.realImage) {
      // Only load the real image when it's needed
      this.realImage = new RealImage(this.filename);
    }
    this.realImage.display();
  }
}

// Client
const image1: Image = new ImageProxy("photo1.jpg");
const image2: Image = new ImageProxy("photo2.jpg");

image1.display(); // Loads and displays photo1
image1.display(); // Displays photo1 without loading it again
image2.display(); // Loads and displays photo2
```

---

## [Chain of Responsibility]()

The **Chain of Responsibility** pattern creates a chain of objects that handle a request sequentially, passing it along the chain if the current object doesn't handle it.

It’s similar to **monads** in functional programming or **middleware** in web development frameworks like Express.js.

```ts
// Handler Interface
interface Logger {
  setNext(logger: Logger): Logger;
  log(level: string, message: string): void;
}

// Concrete Handler 1: Info Logger
class InfoLogger implements Logger {
  private nextLogger: Logger | null = null;

  setNext(logger: Logger): Logger {
    this.nextLogger = logger;
    return logger;
  }

  log(level: string, message: string): void {
    if (level === "INFO") {
      console.log(`Info Logger: ${message}`);
    } else if (this.nextLogger) {
      this.nextLogger.log(level, message);
    }
  }
}

// Concrete Handler 2: Debug Logger
class DebugLogger implements Logger {
  private nextLogger: Logger | null = null;

  setNext(logger: Logger): Logger {
    this.nextLogger = logger;
    return logger;
  }

  log(level: string, message: string): void {
    if (level === "DEBUG") {
      console.log(`Debug Logger: ${message}`);
    } else if (this.nextLogger) {
      this.nextLogger.log(level, message);
    }
  }
}

// Concrete Handler 3: Error Logger
class ErrorLogger implements Logger {
  private nextLogger: Logger | null = null;

  setNext(logger: Logger): Logger {
    this.nextLogger = logger;
    return logger;
  }

  log(level: string, message: string): void {
    if (level === "ERROR") {
      console.log(`Error Logger: ${message}`);
    } else if (this.nextLogger) {
      this.nextLogger.log(level, message);
    }
  }
}

// Usage: Setting up the chain
const infoLogger = new InfoLogger();
const debugLogger = new DebugLogger();
const errorLogger = new ErrorLogger();

// Chain the handlers
infoLogger.setNext(debugLogger).setNext(errorLogger);

// Test: Log messages with different levels
infoLogger.log("INFO", "This is an info message."); // Info Logger handles this
infoLogger.log("DEBUG", "This is a debug message."); // Debug Logger handles this
infoLogger.log("ERROR", "This is an error message."); // Error Logger handles this
infoLogger.log("WARNING", "This is a warning message."); // Unhandled
```

> **When to use:**
>
> - Flexible and dynamic request handling, such as logging systems, customer support ticketing systems, or form validation pipelines.
> - Simplifying conditional logic by delegating responsibility to handlers.

> **Key Benefits:**
>
> - Decouples the sender of a request from its receivers.
> - Allows new handlers to be added without modifying existing code.

---

## [Iterator Pattern]()

The **Iterator Pattern** provides a way to sequentially access elements in a collection without exposing its underlying representation. It's useful in stateful situations like checklists or managing large datasets.

```ts
// Iterator Interface
interface Iterator<T> {
  hasNext(): boolean;
  next(): T;
}

// Concrete Iterator
class BookIterator implements Iterator<string> {
  private books: string[];
  private index: number = 0;

  constructor(books: string[]) {
    this.books = books;
  }

  hasNext(): boolean {
    return this.index < this.books.length;
  }

  next(): string {
    if (this.hasNext()) {
      return this.books[this.index++];
    }
    throw new Error("No more elements");
  }
}

// Aggregate Interface (Collection)
interface Collection<T> {
  createIterator(): Iterator<T>;
}

// Concrete Aggregate (BookCollection)
class BookCollection implements Collection<string> {
  private books: string[];

  constructor(books: string[]) {
    this.books = books;
  }

  createIterator(): Iterator<string> {
    return new BookIterator(this.books);
  }
}

// Usage
const books = new BookCollection(["1984", "Brave New World", "Fahrenheit 451"]);
const iterator = books.createIterator();

while (iterator.hasNext()) {
  console.log(iterator.next()); // Output: 1984, Brave New World, Fahrenheit 451
}
```

> **When to use:**
>
> - Custom traversal logic for collections like trees, graphs, or lists.
> - Allowing multiple ways to traverse a collection (e.g., depth-first vs. breadth-first search).

> **Key Benefits:**
>
> - Adheres to the **Single Responsibility Principle** by separating traversal logic from collection logic.
> - Provides a consistent interface for traversing collections.

---

## [Observer Pattern]()

The **Observer Pattern** establishes a one-to-many relationship where multiple observers automatically get updated when the subject’s state changes.

```ts
// Observer Interface
interface Observer {
  update(temperature: number, humidity: number, pressure: number): void;
}

// Subject Interface
interface Subject {
  registerObserver(observer: Observer): void;
  removeObserver(observer: Observer): void;
  notifyObservers(): void;
}

// Concrete Subject: WeatherStation
class WeatherStation implements Subject {
  private observers: Observer[] = [];
  private temperature: number = 0;
  private humidity: number = 0;
  private pressure: number = 0;

  registerObserver(observer: Observer): void {
    this.observers.push(observer);
  }

  removeObserver(observer: Observer): void {
    const index = this.observers.indexOf(observer);
    if (index !== -1) {
      this.observers.splice(index, 1);
    }
  }

  notifyObservers(): void {
    for (const observer of this.observers) {
      observer.update(this.temperature, this.humidity, this.pressure);
    }
  }

  setMeasurements(
    temperature: number,
    humidity: number,
    pressure: number
  ): void {
    this.temperature = temperature;
    this.humidity = humidity;
    this.pressure = pressure;
    this.notifyObservers();
  }
}

// Concrete Observer: CurrentConditionsDisplay
class CurrentConditionsDisplay implements Observer {
  private temperature: number = 0;
  private humidity: number = 0;

  update(temperature: number, humidity: number, pressure: number): void {
    this.temperature = temperature;
    this.humidity = humidity;
    this.display();
  }

  display(): void {
    console.log(
      `Current conditions: ${this.temperature}°C and ${this.humidity}% humidity`
    );
  }
}

// Concrete Observer: ForecastDisplay
class ForecastDisplay implements Observer {
  private pressure: number = 0;

  update(temperature: number, humidity: number, pressure: number): void {
    this.pressure = pressure;
    this.display();
  }

  display(): void {
    console.log(`Forecast: Pressure is ${this.pressure} hPa`);
  }
}

// Usage
const weatherStation = new WeatherStation();

const currentDisplay = new CurrentConditionsDisplay();
const forecastDisplay = new ForecastDisplay();

weatherStation.registerObserver(currentDisplay);
weatherStation.registerObserver(forecastDisplay);

// Simulating weather data updates
weatherStation.setMeasurements(25, 60, 1013); // Output: Current conditions: 25°C and 60% humidity, Forecast: Pressure is 1013 hPa
weatherStation.setMeasurements(30, 65, 1012); // Output: Current conditions: 30°C and 65% humidity, Forecast: Pressure is 1012 hPa
```

> **When to use:**
>
> - Event-driven systems, such as GUIs or real-time data updates.
> - When multiple parts of a system need to react to state changes in a centralized object.

> **Key Benefits:**
>
> - Promotes loose coupling between the subject and observers.
> - Dynamically adds or removes observers without modifying the subject.

> **Common Pitfalls:**
>
> - Ensure observers properly unregister to avoid memory leaks.
> - Be cautious of notification delays when dealing with large numbers of observers.

# The end
