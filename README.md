## Design Pattern
Design patterns are solutions to software design problems you find again and again in real-world application development. Patterns are about reusable designs and interactions of objects.

The 23 Gang of Four (GoF) patterns are generally considered the foundation for all other patterns. They are categorized in three groups: Creational, Structural, and Behavioral (for a complete list see below).

To give you a head start, We will propose some problems in a hypothetical scenario which help you understand these principles of Pattern Design. The php source code for each pattern is provided in 2 forms: structural and real-world. Structural code uses type names as defined in the pattern definition and UML diagrams. Real-world(hypothetical scenario) code provides real-world programming situations where you may use these patterns. You can see most of these hypothetical scenario in 《Head First设计模式》.

- Creational Patterns

|design pattern | description|
|:----    |-----   |
|[Abstract Factory](https://github.com/sdwang1/design-pattern/blob/master/Abstract_Factory/Abstract_Factory_Pattern.md)|	Creates an instance of several families of classes|
|Builder	|Separates object construction from its representation|
|[Factory Method](https://github.com/sdwang1/design-pattern/blob/master/Factory_Method/Factory_Pattern.md)	|Creates an instance of several derived classes|
|Prototype	|A fully initialized instance to be copied or cloned|
|[Singleton](https://github.com/sdwang1/design-pattern/blob/master/Singleton_Pattern/Singleton_Pattern.md)	|A class of which only a single instance can exist|

- Structural Patterns

|design pattern | description|
|:----    |-----   |
|[Adapter](https://github.com/sdwang1/design-pattern/blob/master/Adapter/Adapter_Pattern.md)	|Match interfaces of different classes|
|Bridge	| Separates an object’s interface from its implementation|
|Composite	|A tree structure of simple and composite objects|
|Decorator	|Add responsibilities to objects dynamically|
|Facade	|A single class that represents an entire subsystem|
|Flyweight	|A fine-grained instance used for efficient sharing|
|Proxy	|An object representing another object|

- Behavioral Patterns

|design pattern | description|
|:----    |-----   |
|Chain of Resp.	|A way of passing a request between a chain of objects|
|Command	|Encapsulate a command request as an object|
|Interpreter	|A way to include language elements in a program|
|Iterator	|Sequentially access the elements of a collection|
|Mediator	|Defines simplified communication between classes|
|Memento	|Capture and restore an object's internal state|
|Observer	|A way of notifying change to a number of classes|
|State	|Alter an object's behavior when its state changes|
|Strategy	|Encapsulates an algorithm inside a class|
|Template	|Method Defer the exact steps of an algorithm to a subclass|
|Visitor	|Defines a new operation to a class without change|
