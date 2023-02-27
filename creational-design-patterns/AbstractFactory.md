# Abstract Factory Pattern

- _Note: Code snippets in this tutorial are not complete._

## Overview
- The Abstract Factory design pattern is a creational pattern that provides an interface for creating families of related or dependent objects without specifying their concrete classes. The pattern is useful when you want to create objects that are related or work together in a system, and you want to ensure that they are compatible with each other.
- In the Abstract Factory pattern, there are several related object families, and each family has multiple concrete implementations. The abstract factory defines a set of methods for creating each object in the families, and each concrete factory implements these methods to create the corresponding objects.

## Motivation
 Let's say you're building a game that involves different types of enemies. Each enemy has a specific set of attributes (e.g. health, damage, speed), and you need to create instances of these enemies dynamically as the game progresses.

### Solution without abstract factory pattern:
One way to create these enemies is to use a factory method for each type of enemy. For example, you could create a `GoblinFactory` that creates instances of `Goblin` enemies with specific attributes. Similarly, you could create a `TrollFactory` that creates instances of `Troll` enemies. 

![Game-Class-Diagram](https://user-images.githubusercontent.com/17255813/221466766-d2fc0870-6705-4919-ac10-79d87618e61b.png)

```java
    public interface EnemyFactory {
        public Enemy createEnemy();
    }

    public class GoblinFactory implements EnemyFactory {
        public Enemy createEnemy() {
            return new Goblin();
        }
    }

    public class TrollFactory implements EnemyFactory {
        public Enemy createEnemy() {
            return new Troll();
        }
    }

    public abstract class Enemy {
        // Common attributes and methods for all enemies
    }

    public class Goblin extends Enemy {
        // Attributes and methods specific to goblins
    }

    public class Troll extends Enemy {
        // Attributes and methods specific to trolls
    }
```


### Deficit: 
However, this approach can lead to several issues:

- It can be difficult to add a new type of enemy.
  For every new type of enemy, we need to add a concrete class for this enemy and also we need a factory class that implement `EnemyFactory`.
  Example:
    ```java
    public class Orc{
        // Attributes and methods specific to Orc
    }

    public class OrcFactory implements EnemyFactory {
        public Enemy createEnemy (){
            return new Orc();
        }
    }
    ```
- If you need to change the way enemies are created, you'll need to modify each factory method separately, which violate Single Responsibility Principle (SRP).
  
- suppose we have a change in requirement happened and every enemy have a new style called _modern_ fashioned enemies.
  - So we need to extend the enemy classes like that:

    ![New Enemy Family](https://user-images.githubusercontent.com/17255813/221466824-6aa2ee94-3260-4820-aac0-fa8d2bffbfa2.png)

  - And we need 2 new factories, `ModernTrollFactory` and `ModernGoblinFactory`:
    ![New Factories](https://user-images.githubusercontent.com/17255813/221466875-3901bbae-5611-4f44-a409-27688272f820.png)
    
  -  The code became more complicated since you need to introduce a lot of new subclasses and adding new types would make it more worse.

### Solution with abstract factory pattern:
An alternative solution is to use the Abstract Factory pattern. Instead of creating separate factory methods for each type of enemy, you create a single `EnemyFactory` interface with methods for creating different types of enemies. You then implement this interface with concrete factory classes.

With the Abstract Factory pattern, you can easily switch between different families of related objects by creating a new set of concrete factory classes. You can also change the way enemies are created by modifying a single set of concrete factory classes, rather than modifying each factory method separately. And if you need to test your code, you can easily create mock factory classes that implement the EnemyFactory interface.

 ![game with abstract](https://user-images.githubusercontent.com/17255813/221466921-7a76fb9c-5aa4-49c3-9c63-b4893020061e.png)


```java
public interface EnemyFactory {
  public Goblin createGoblin();
  public Troll createTroll();
}

public class DefaultEnemyFactory implements EnemyFactory {
  public Goblin createGoblin() {
    return new DefaultGoblin();
  }
  public Troll createTroll() {
    return new DefaultTroll();
  }
}
public class ModernEnemyFactory implements EnemyFactory {
  public Goblin createGoblin() {
    return new ModernGoblin();
  }
  public Troll createTroll() {
    return new ModernTroll();
  }
}
```

### Advantages of using the Factory Method Pattern solution
Overall, using the Abstract Factory pattern can provide several advantages over creating separate factory methods for each type of enemy:
- code reuse and abstraction, making it easier to switch between families of related objects and modify the way objects are created.
- improve the testability of your code by allowing you to create mock factory classes.
- help you write more maintainable and scalable code by encapsulating the creation of related objects.

## Structure
![Abstract structure](https://user-images.githubusercontent.com/17255813/221466974-39009fca-1ca3-4d49-bac1-b21524a90ee3.png)


- **Abstract Factory**: This interface defines the factory methods that create the abstract product objects. It's responsible for defining the signature of these methods and providing a way for the Concrete Factory classes to create their specific Concrete Product objects.

- **Concrete Factory**: This class implements the Abstract Factory interface and creates the Concrete Product objects. It's responsible for implementing the factory methods of the Abstract Factory, and creating the specific instances of the Concrete Products.

- **Abstract Product**: This interface declares a type of product object. It's responsible for defining the common interface that all Concrete Products must implement.

- **Concrete Product**: This class implements the Abstract Product interface and provides the specific implementation for the product. It's responsible for providing the specific functionality of the product.
  
## Examples
- _In this section we will introduce some scenarios where using abstract factory pattern could be helpful._

1. Decoupled architecture: The abstract factory pattern provides a way to create objects through an interface, allowing the client code to remain agnostic to the concrete classes and improving the overall modularity and flexibility of the system.

    Example: When working with databases, the Abstract Factory pattern can be used to create database connection objects, command objects, and data adapter objects for different database management systems (e.g., MySQL, Oracle, SQL Server, etc.). The client code only needs to call the appropriate factory method for the desired database, and the factory will create the appropriate objects.

    ![Database access example](https://user-images.githubusercontent.com/17255813/221467024-c20f3fec-3c74-48ab-b58a-47bbf2da0bb6.png)


    ```java
    public interface DatabaseFactory {
        Connection createConnection();
        Command createCommand();
        DataAdapter createDataAdapter();
    }


    public class MySQLFactory implements DatabaseFactory {
        public Connection createConnection() {
            // Code to create a MySQL connection
        }
        
        public Command createCommand() {
            // Code to create a MySQL command object
        }
        
        public DataAdapter createDataAdapter() {
            // Code to create a MySQL data adapter object
        }
    }

    public class OracleFactory implements DatabaseFactory {
        public Connection createConnection() {
            // Code to create an Oracle connection
        }
        
        public Command createCommand() {
            // Code to create an Oracle command object
        }
        
        public DataAdapter createDataAdapter() {
            // Code to create an Oracle data adapter object
        }
    }

    public class SQLServerFactory implements DatabaseFactory {
        public Connection createConnection() {
            // Code to create a SQL Server connection
        }
        
        public Command createCommand() {
            // Code to create a SQL Server command object
        }
        
        public DataAdapter createDataAdapter() {
            // Code to create a SQL Server data adapter object
        }
    }
    ```
   - First, we define an abstract factory interface that defines the methods for creating the different types of objects: `DatabaseFactory`
   - Next, we define concrete factory classes that implement this interface for each specific database management system: `MySQLFactory`, `OracleFactory` and `SQLServerFactory`.
   - Then, we can define a client class that uses the abstract factory interface to create the appropriate objects based on the desired database management system:
        ```java
        public class DatabaseClient {
            private DatabaseFactory factory;
            
            public DatabaseClient(DatabaseFactory factory) {
                this.factory = factory;
            }
            
            public List<DataObject> executeQuery(String query) {
                Connection connection = factory.createConnection();
                Command command = factory.createCommand();
                DataAdapter adapter = factory.createDataAdapter();
                
                connection.open();
                command.setConnection(connection);
                command.setQuery(query);
                ResultSet resultSet = command.execute();
                adapter.setSource(resultSet);
                List<DataObject> data = adapter.getData();
                connection.close();

                return data;
            }
        }
        ```
        
    - One another advantage we get using abstract factory is Testability.
    - The use of the Abstract Factory pattern can make it easier to write unit tests for the client code.
      - Now, Suppose we need to unit test the `executeQuery()` method.
      - we can create a test implementation of the `DatabaseFactory` interface that returns mock objects with predefined behavior

        ```java
        public class TestDatabaseFactory implements DatabaseFactory {
            @Override
            public Connection createConnection() {
                return new TestConnection();
            }
            
            @Override
            public Command createCommand() {
                return new TestCommand();
            }
            
            @Override
            public DataAdapter createDataAdapter() {
                return new TestDataAdapter();
            }
        }

        public class TestConnection implements Connection {
            private boolean isOpen = false;
            
            @Override
            public void open() {
                isOpen = true;
            }
            
            @Override
            public void close() {
                isOpen = false;
            }
            
            @Override
            public boolean isOpen() {
                return isOpen;
            }
        }

        public class TestCommand implements Command {
            private Connection connection;
            private String query;
            
            @Override
            public void setConnection(Connection connection) {
                this.connection = connection;
            }
            
            @Override
            public void setQuery(String query) {
                this.query = query;
            }
            
            @Override
            public ResultSet execute() {
                // return a mock result set
                return new TestResultSet();
            }
        }

        public class TestDataAdapter implements DataAdapter {
            private ResultSet source;
            
            @Override
            public void setSource(ResultSet source) {
                this.source = source;
            }
            
            @Override
            public List<DataObject> getData() {
                // return a list of mock data objects
                return Arrays.asList(
                    new DataObject("id", 1),
                    new DataObject("name", "John"),
                    new DataObject("age", 30)
                );
            }
        }
        ```
        
      - We can then use the `TestDatabaseFactory` to create a `Client` instance for testing:

        ```java
        @Test
        public void testExcuteQuery() {
            DatabaseFactory factory = new TestDatabaseFactory();
            Client client = new Client(factory);
            List<DataObject> data = client.executeQuery("SELECT * from DataSource.Table");

            assertEquals(3, data.size());
            assertEquals(1, data.get(0).getValue());
            assertEquals("John", data.get(1).getValue());
            assertEquals(30, data.get(2).getValue());
        }
        ```


2. Dynamic Object Creation: If you need to create objects dynamically at runtime, the Abstract Factory pattern can be helpful. By using a factory interface, you can create new objects based on user input or other runtime conditions. This can be useful if you need to create objects that vary based on user preferences, for example.
   
   - In e-commerce websites, the Abstract Factory pattern can be used to create different types of payment processing objects (e.g., credit card, PayPal, etc.) for different payment gateways. The client code only needs to call the appropriate factory method for the desired payment method, and the factory will create the appropriate object for processing the payment.
   
    - we'll define an abstract factory class that provides methods for creating different payment processing  and create concrete factory classes that extend the abstract factory class and implement the factory methods:
        ![processors](https://user-images.githubusercontent.com/17255813/221467147-ec2f2ff5-fa19-4d0f-b3de-d4c3e4d1a722.png)


        ```java
        public interface PaymentFactory {
            PaymentGateway createPaymentGateway();
            PaymentRequest createPaymentRequest();
            PaymentResponse createPaymentResponse();
        }

        public class CreditCardPaymentFactory implements PaymentFactory {
            @Override
            public PaymentGateway createPaymentGateway() {
                // implementation details for credit card payment gateway
            }
            
            @Override
            public PaymentRequest createPaymentRequest() {
                // implementation details for credit card payment request
            }
            
            @Override
            public PaymentResponse createPaymentResponse() {
                // implementation details for credit card payment response
            }
        }

        public class PayPalPaymentFactory implements PaymentFactory {
            @Override
            public PaymentGateway createPaymentGateway() {
                // implementation details for PayPal payment gateway
            }
            
            @Override
            public PaymentRequest createPaymentRequest() {
                // implementation details for PayPal payment request
            }
            
            @Override
            public PaymentResponse createPaymentResponse() {
                // implementation details for PayPal payment response
            }
        }

        public class ApplePayPaymentFactory implements PaymentFactory {
            @Override
            public PaymentGateway createPaymentGateway() {
                // implementation details for Apple Pay payment gateway
            }
            
            @Override
            public PaymentRequest createPaymentRequest() {
                // implementation details for Apple Pay payment request
            }
            
            @Override
            public PaymentResponse createPaymentResponse() {
                // implementation details for Apple Pay payment response
            }
        }
        ```
   
3. Testing: The Abstract Factory pattern can also be useful for testing. By using a factory interface, you can create mock objects that mimic the behavior of the objects you are testing. This can make it easier to test your code in isolation, without having to worry about the behavior of other parts of your code.

    - suppose we have a class called `MyClass` that depends on a service of type `IService`:

        ```java
        public interface IService {
            public void doSomething();
        }

        public class ConcreteService implements IService{
            public void doSomething(){
                // do something...
            }
        }

        public interface ServiceFactory {
            public IService create();
        }

        public class MyClass {
            private IService service;

            public MyClass(IService factory) {
                this.service = factory.create();
            }
            public void doSomethingWithService() {
                // use IService to do something
                service.doSomething();
            }
        }
        ```
    - Here how we can test `MyClass` class in isolation from IService:

        ```java    
        public class MyClassTest {
            private static class MockService implements IService {
                public void doSomething() {
                    // do nothing
                }
            }

            private static class MockServiceFactory implements ServiceFactory {
                public IService create() {
                    return new MockService();
                }
            }

            @Test
            public void testDoSomethingWithService() {
                MyClass myClass = new MyClass(new MockServiceFactory());
                // test the behavior of myClass
                // ...
            }
        }
        ```
        
    - By injecting a mock implementation of `IService` during testing, we can isolate the behavior of `MyClass` and test it independently of the actual service implementation.
    - This makes it easier to identify and fix bugs in `MyClass` without being distracted by bugs in the service.


## Advantages
- Avoid tight coupling between concrete products and client code.
- _Single Responsibility Principle_. You can extract the product creation code into one place, making the code easier to support.
- _Open/Closed Principle_. You can introduce new types of products into the program without breaking existing clint code. (They Can Subtype).
- Can be sure that the products you’re getting from a factory are compatible with each other.
  
## Drawbacks
- The code may become more complicated than it should be, since a lot of new interfaces and classes are introduced along with the pattern.


## Refrences
- [Dive into Design Patterns Book](https://refactoring.guru/design-patterns/book)
- [Pluralsight course](https://app.pluralsight.com/library/courses/design-patterns-java-creational)
