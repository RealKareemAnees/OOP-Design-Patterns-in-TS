# Design Patterns in TypeScript

---

## 1. **Creational Patterns**

---

### [_Singleton_]()

this pattern makes us ensure that only one instance of a class is available

```ts
class Singleton {
  private static instance: Singleton;

  constructor() {
    if (Singleton.instance) {
      return Singleton.instance;
    } else {
      Singleton.instance = this;

      // Initialize the instance normally
    }
  }
  public static getInstance(): Singleton {
    return Singleton.instance;
  }
}
```

this way only one instance of the class is initialized

**benifets of doing so**

- statefullness
- only-once initialisation

<br>

> **Uses of Singleton pattern**
>
> 1. Configuration Management
> 2. Logging
> 3. Database Connection
> 4. Thread Pool Management
> 5. Caching
> 6. Service Locators
> 7. Multithreading Control
>
> **Typically any state-holding entity or a centralized service**

### [Factory]()

a _factory_ generates objects instead of the user

```ts
// interfaces
interface HimalayanCatInterface {
  color: string;
  fur: string;
  size: string;
}

interface PersianCatInterface {
  color: string;
  fur: string;
  size: string;
}
```

a good practice is to define interfaces, the **Users** care aboun interfaces, class proto doen't represent an interface from a design prospective

> I we defined a class instead of the intrface of the presian cat for example, we will face isssues witth other breeds using that class

```ts
// factory
class CatsFactory {
  public static createHimalayanCat(): HimalayanCatInterface {
    return {
      color: "white",
      fur: "long",
      size: "large",
    };
  }

  public static createPersianCat(): PersianCatInterface {
    return {
      color: "black",
      fur: "short",
      size: "small",
    };
  }
}
```

this pattern make the system loosely coupled

### [abstract factory]()

**instead of making the users expect cerain interface, we can give them what they need**

```ts
class CatFactory {
  static createCat(type: string): HimalayanCatInterface | PersianCatInterface {
    if (type === "Himalayan") {
      return {
        color: "white",
        fur: "long",
        size: "large",
      };
    } else if (type === "Persian") {
      return {
        color: "black",
        fur: "short",
        size: "small",
      };
    } else {
      throw new Error("Invalid cat type");
    }
  }
}
```

### [Builder pattern]()

```ts
// builder pattern

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

  build(): HimalayanCatInterface {
    return {
      color: this.color,
      fur: this.fur,
      size: this.size,
    };
  }
}
```

```ts
//usage

const himalayanCat = new HimalayanCatBuilder()
  .setColor("white")
  .setFur("long")
  .setSize("large")
  .build();
```

**I think that this pattern is only useful in only two cases**

- the object is very complex
- or the system needs to build this object on multiple stages like in [_Function currying in FP_](https://github.com/RealKareemAnees/GoFunctionalProgramming#function-currying)

### [object pool]()

```ts
// object pool

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
      this._cats.push(new Cat("cat" + i));
    }
  }

  public getCat() {
    if (this._cats.length === 0) {
      console.log("No cats available");
      return null;
    }
    return this._cats.pop(); // get the last cat
  }
}
```

creates set of usable objects on the fly

<br>

> **Uses of Object Pool**
>
> 1. Thread Pooling
> 2. Memory-Intensive Object Management
>
> **Typicaly when we need to retrieve a DS on the fly**

## Dependency injection

I made this in separate section

**The whole Idea of DI is to make users use interfsces of its issosiations instead of the concrete implementation**

```ts
// ioc-container

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

// usage

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
    a.doSomething();
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

in the previous code neither A nor B know about each other, we can user interfaces for further abstraction instead of using the class as a reference to methods

---

## Structural Design Pattern

### Decorator

decorator is the exact implementation on _Polymorphism_ where we extend a class and alter methods like this code

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

<br>

a common use case which I use alot is retry logic

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
```

this code uses decorator pattern and decorators in ts5

```ts
// usage
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

output should be like

```cmd
Hello World
Hello World
Hello World
23 |
24 | class S {
25 |   @retry(3)
26 |   print() {
27 |     console.log("Hello World");
28 |     throw new Error("Error");
               ^
error: Error
      at print (/home/kareem/Desktop/Coding/OOP-Design-Patterns-in-TS/S.ts:28:11)
      at <anonymous> (/home/kareem/Desktop/Coding/OOP-Design-Patterns-in-TS/S.ts:12:33)
      at /home/kareem/Desktop/Coding/OOP-Design-Patterns-in-TS/S.ts:33:3

Bun v1.1.43 (Linux x64)
```

<br>

also in validation of inputs

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

// usage
class S {
  @validateInput((args) => args[0] === "hi")
  public static print(input: any) {
    console.log(input);
  }
}

S.print("hi"); // hi
S.print("hello"); // Error: Invalid input
```

### Adapter Pattern

our user expects an interface, so instead of editing an existing interface, we can just use another class in the middle to adapt the user inputs to the class inputs

> adapter is usen when the diffirence in interface is not huge, otherwise it will be a _proxy_

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

### facade

The Facade Pattern is a structural design pattern that provides a simplified interface to a complex subsystem

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

### proxy

The Proxy Pattern is another structural design pattern that provides an object representing another object. The proxy acts as an intermediary and can control access to the real object, usually for purposes like lazy initialization, access control, logging, or additional functionality.

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

## Behavioral Patterns

### strategy

it is passing of the implementation as an argument rather than attaching it

```ts
// Strategy Interface
interface SortStrategy {
  sort(arr: number[]): number[];
}

// Concrete Strategy A: Bubble Sort
class BubbleSort implements SortStrategy {
  sort(arr: number[]): number[] {
    console.log("Using Bubble Sort");
    let n = arr.length;
    for (let i = 0; i < n - 1; i++) {
      for (let j = 0; j < n - i - 1; j++) {
        if (arr[j] > arr[j + 1]) {
          // Swap
          [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        }
      }
    }
    return arr;
  }
}

// Concrete Strategy B: Quick Sort
class QuickSort implements SortStrategy {
  sort(arr: number[]): number[] {
    console.log("Using Quick Sort");
    if (arr.length <= 1) return arr;
    let pivot = arr[0];
    let left = arr.slice(1).filter((x) => x < pivot);
    let right = arr.slice(1).filter((x) => x >= pivot);
    return [...this.sort(left), pivot, ...this.sort(right)];
  }
}

// Context: SortingContext
class SortingContext {
  private strategy: SortStrategy;

  constructor(strategy: SortStrategy) {
    this.strategy = strategy;
  }

  setStrategy(strategy: SortStrategy) {
    this.strategy = strategy;
  }

  executeSort(arr: number[]): number[] {
    return this.strategy.sort(arr);
  }
}

// Usage
const numbers = [5, 3, 8, 4, 2];

// Use Bubble Sort
const context = new SortingContext(new BubbleSort());
console.log(context.executeSort([...numbers])); // Output: Bubble Sort and sorted numbers

// Switch to Quick Sort
context.setStrategy(new QuickSort());
console.log(context.executeSort([...numbers])); // Output: Quick Sort and sorted numbers
```

### chain of risponsibility

it is the same concept of _monads_ in FP except it is OOp

like

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
infoLogger.log("INFO", "This is an info message.");
infoLogger.log("DEBUG", "This is a debug message.");
infoLogger.log("ERROR", "This is an error message.");
infoLogger.log("WARNING", "This is a warning message.");
```

or like middleware

```ts
import express from "express";

const app = express();

// Middleware 1: Logging
function logRequest(
  req: express.Request,
  res: express.Response,
  next: express.NextFunction
) {
  console.log(`Received request: ${req.method} ${req.url}`);
  next(); // Passes the request to the next middleware
}

// Middleware 2: Authentication check
function authenticate(
  req: express.Request,
  res: express.Response,
  next: express.NextFunction
) {
  if (req.headers["auth-token"]) {
    console.log("User authenticated");
    next(); // Passes the request to the next middleware
  } else {
    res.status(403).send("Forbidden");
  }
}

// Middleware 3: Final request handler
function handleRequest(req: express.Request, res: express.Response) {
  res.send("Request processed successfully");
}

// Chain of Middleware
app.use(logRequest);
app.use(authenticate);
app.use(handleRequest);

// Start the server
app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

### itirator

the itirator class is used to itirate over set of entities, like a loop but in statefull manner, this can be usefull in a checklist for example where you want to stop and continue based on factors

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

### observer

The Observer Pattern is a behavioral design pattern that defines a one-to-many dependency between objects, where a change in the state of one object (called the subject) is automatically notified to all dependent objects (called observers)

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
