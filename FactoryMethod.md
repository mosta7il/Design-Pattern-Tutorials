# Factory Method Pattern

## Overview
- The Factory Method is a creational design pattern that provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created. The purpose of this pattern is to allow a class to delegate object creation to its subclasses, which allows for greater flexibility and easier maintenance.
- The Factory Method solves the problem of hard-coding the creation of objects in a class. By delegating object creation to subclasses, the Factory Method makes it easier to change the objects that are created, without having to change the code in the main class.

## Motivation
### Suppose you develop a LinkedIn-alike app, you need to generate different types of notifications for users such as connection requests, message notifications, and post updates. The problem is to determine the best way to create these notifications.

### Solution without factory method pattern:
You can create individual classes for each type of notification, such as `ConnectionRequestNotification`, `MessageNotification`, and `PostUpdateNotification`. Each class would contain the logic to display the notification. However, if a new type of notification is added in the future, you would need to create a new class and update the code in multiple places.

```java

    class ConnectionRequestNotification {
        void display() {
            System.out.println("Displaying Connection Request Notification");
        }
    }

    class MessageNotification {
        void display() {
            System.out.println("Displaying Message Notification");
        }
    }

    class PostUpdateNotification {
        void display() {
            System.out.println("Displaying Post Update Notification");
        }
    }

    class NotificationHandler {
        ConnectionRequestNotification connectionRequestNotification;
        MessageNotification messageNotification;
        PostUpdateNotification postUpdateNotification;
        
        void displayNotification(String type) {
            switch (type) {
                case "connectionRequest":
                    connectionRequestNotification = new ConnectionRequestNotification();
                    connectionRequestNotification.display();
                    break;
                case "message":
                    messageNotification = new MessageNotification();
                    messageNotification.display();
                    break;
                case "postUpdate":
                    postUpdateNotification = new PostUpdateNotification();
                    postUpdateNotification.display();
                    break;
            }
        }
    }
```


### Deficit: 
This solution violates the open/closed principle, which states that a class should be open for extension but closed for modification. Every time a new type of notification is added, you would need to modify the existing code, which can lead to errors and increase the maintenance cost.

### Solution with factory method pattern:
You can use the factory method pattern to create notifications. The pattern involves creating an abstract class `Notification` with a factory method `createNotification` that returns the required notification type. Concrete classes such as `ConnectionRequestNotification`, `MessageNotification`, and `PostUpdateNotification` can inherit from the `Notification` class.

```java
interface Notification {
    void display();
}

class ConnectionRequestNotification implements Notification {
    @Override
    public void display() {
        System.out.println("Displaying Connection Request Notification");
    }
}

class MessageNotification implements Notification {
    @Override
    public void display() {
        System.out.println("Displaying Message Notification");
    }
}

class PostUpdateNotification implements Notification {
    @Override
    public void display() {
        System.out.println("Displaying Post Update Notification");
    }
}

abstract class NotificationFactory {
    abstract Notification createNotification(String type);
    void displayNotification(String type) {
        Notification notification = createNotification(type);
        notification.display();
    }
}

class LinkedInNotificationFactory extends NotificationFactory {
    @Override
    Notification createNotification(String type) {
        switch (type) {
            case "connectionRequest":
                return new ConnectionRequestNotification();
            case "message":
                return new MessageNotification();
            case "postUpdate":
                return new PostUpdateNotification();
            default:
                return null;
        }
    }
}
```

### Advantages of using the Factory Method Pattern solution

- Encapsulates object creation and abstracts it from client code
- Provides a clear separation of concerns by isolating object creation code in a separate class
- Makes code more flexible and adaptable to change
- Can be easily extended to support the creation of different types and variations of objects


## Structure
![Factory Method Pattern class diagram](https://user-images.githubusercontent.com/17255813/216735161-ee60df3c-60f6-4031-8b91-2787e4ca9be8.png)

- **Creator abstract class**: An abstract class that includes a factory method for creating objects of a certain type. The factory method returns an object of the type specified by a sub-class.

- **Concrete Creator sub-classes**: Sub-classes of the Creator class that implement the factory method and return objects of a specific type. These sub-classes determine which type of objects are created by the factory method.

- **Product interface**: An interface that defines the type of objects that are created by the factory method. The interface includes a set of methods that must be implemented by all concrete product classes.

- **Concrete Product classes**: Classes that implement the Product interface and provide concrete implementations of the methods defined by the interface. These classes represent the objects that are created by the factory method.
- Despite its name, product creation is not the primary
responsibility of the **creator**. Usually, the **creator** class already has some core business logic related to products. The factory method helps to decouple this logic from the concrete product classes.

## Examples
- _In this section we will introduce some scenarios where using factory method pattern could be helpful._

1. Object Initialization: Factory Methods can be used to initialize objects with specific configurations, such as setting default values, performing calculations, or retrieving data from a database.

    ```java
    interface DataSource {
        void connect();
        void disconnect();
        List<String> fetchData();
    }

    class MySQLDataSource implements DataSource {
        private String host;
        private int port;
        private String username;
        private String password;

        public MySQLDataSource(String host, int port, String username, String password) {
            this.host = host;
            this.port = port;
            this.username = username;
            this.password = password;
        }

        @Override
        public void connect() {
            // Connect to MySQL database
        }

        @Override
        public void disconnect() {
            // Disconnect from MySQL database
        }

        @Override
        public List<String> fetchData() {
            // Fetch data from MySQL database
            return new ArrayList<>();
        }
    }

    class APIDataSource implements DataSource {
        private String endpoint;
        private String apiKey;

        public APIDataSource(String endpoint, String apiKey) {
            this.endpoint = endpoint;
            this.apiKey = apiKey;
        }

        @Override
        public void connect() {
            // Connect to API
        }

        @Override
        public void disconnect() {
            // Disconnect from API
        }

        @Override
        public List<String> fetchData() {
            // Fetch data from API
            return new ArrayList<>();
        }
    }

    class DataSourceFactory {
        public static DataSource createDataSource(Map<String, String> configuration) {
            String type = configuration.get("type");
            if (type.equals("mysql")) {
                return new MySQLDataSource(
                    configuration.get("host"),
                    Integer.parseInt(configuration.get("port")),
                    configuration.get("username"),
                    configuration.get("password")
                );
            } else if (type.equals("api")) {
                return new APIDataSource(
                    configuration.get("endpoint"),
                    configuration.get("api_key")
                );
            } else {
                throw new IllegalArgumentException("Invalid data source type: " + type);
            }
        }
    }

    class DataCollector {
        private DataSource dataSource;

        public DataCollector(Map<String, String> configuration) {
            dataSource = DataSourceFactory.createDataSource(configuration);
        }

        public List<String> collectData() {
            dataSource.connect();
            List<String> data = dataSource.fetchData();
            dataSource.disconnect();
            return data;
        }
    }

    ```
   - Definition of an interface `DataSource` with methods for connecting, disconnecting and fetching data.
   - Implementation of two concrete classes `MySQLDataSource` and `APIDataSource` for the interface.
   - Definition of a factory class `DataSourceFactory` with a method to create an instance of either `MySQLDataSource` or `APIDataSource` based on the configuration.
   - Definition of a class `DataCollector` that uses the factory method to create the `DataSource` object and connect to the data source.
   - The Factory Method pattern allows for easy addition of new data sources and reduces coupling between the client code and the concrete classes.
  


2. Extend internal components of a library: Provide users of a library with a way to extend its internal components. The basic idea is to create a factory class that includes methods for creating objects that implement the necessary interfaces. This factory class can be extended by users of the library, who can provide their own implementation of the factory methods to create objects that extend or replace the default implementation.
   

    ```java
    public interface Component {
        void doSomething();
    }

    public class DefaultComponent implements Component {
        @Override
        public void doSomething() {
            // Default implementation
        }
    }

    public class Library {
        public static class ComponentFactory {
            public static Component createComponent() {
            return new DefaultComponent();
            }
        }
    
        public void doSomethingWithComponent() {
            Component component = ComponentFactory.createComponent();
            component.doSomething();
        }
    }
    ```
    - In this example, the `Library` class includes a `ComponentFactory` class that provides a method for creating `Component` objects.
    - The `Component` interface and `DefaultComponent` class provide a default implementation of the `Component` interface.
    - Users of the library can extend the `ComponentFactory` class to provide their own implementation of the `createComponent` method.

    ```java
    public class CustomComponent implements Component {
        @Override
        public void doSomething() {
            // Custom implementation
        }
    }

    public class CustomComponentFactory extends Library.ComponentFactory {
        @Override
        public static Component createComponent() {
            return new CustomComponent();
        }
    }
    ```
    - The user can then use the `CustomComponentFactory` class to create `CustomComponent` objects, which will be used by the `Library` class instead of the default `DefaultComponent` objects.

3. Dependency Injection: Factory Methods can be used to provide objects to client code as dependencies, allowing for easy decoupling and testing.
    - A software application needs to log messages to either the console or a file. The logging mechanism should be flexible, allowing for the possibility of adding new loggers in the future.
    ```java
    interface Logger {
        void logMessage(String message);
    }

    class ConsoleLogger implements Logger {
        @Override
        public void logMessage(String message) {
            System.out.println(message);
        }
    }

    class FileLogger implements Logger {
        private String fileName;

        public FileLogger(String fileName) {
            this.fileName = fileName;
        }

        @Override
        public void logMessage(String message) {
            // Implementation to log message to file
        }
    }

    abstract class LoggerFactory {
        abstract Logger createLogger();
    }

    class ConsoleLoggerFactory extends LoggerFactory {
        @Override
        Logger createLogger() {
            return new ConsoleLogger();
        }
    }

    class FileLoggerFactory extends LoggerFactory {
        private String fileName;

        public FileLoggerFactory(String fileName) {
            this.fileName = fileName;
        }

        @Override
        Logger createLogger() {
            return new FileLogger(fileName);
        }
    }

    class Application {
        private Logger logger;

        public Application(LoggerFactory factory) {
            this.logger = factory.createLogger();
        }

        public void logMessage(String message) {
            logger.logMessage(message);
        }
    }
    ```
   - The `Factory` Method pattern can be used to provide the objects needed for logging as dependencies to the client code.
   - The `Logger` interface is defined, along with implementations such as `ConsoleLogger` and `FileLogger`.
   - The `LoggerFactory` abstract class is defined, with concrete implementations such as `ConsoleLoggerFactory` and `FileLoggerFactory`.
   - The `Application` class uses a `LoggerFactory` object to create the appropriate `Logger` implementation. This allows for easy substitution of the logger without affecting the existing code.
   - This approach provides a flexible mechanism for creating objects, making it easier to change the implementation or add new loggers in the future.
   - It also makes it easier to test the `Application` class by providing different `LoggerFactory` objects during testing.

4. Object Pooling: Factory Methods can be used to manage a pool of objects, allowing for efficient reuse of objects rather than constantly creating and destroying them.

    - When connecting to databases, creating a new connection for each request can consume a lot of system resources and slow down the application.
    - By using the Factory Method pattern, we can optimize the use of system resources by reusing existing connections instead of creating new ones for each request.

    ```java
    interface Connection {
        void connect();
        void close();
    }

    class MySQLConnection implements Connection {
        @Override
        public void connect() {
            // logic to connect to a MySQL database
        }
        @Override
        public void close() {
            // logic to close the connection to the MySQL database
        }
    }

    class PostgreSQLConnection implements Connection {
        @Override
        public void connect() {
            // logic to connect to a PostgreSQL database
        }
        @Override
        public void close() {
            // logic to close the connection to the PostgreSQL database
        }
    }

    class ConnectionFactory {
        private List<Connection> availableConnections = new ArrayList<>();

        public Connection getConnection(Class<? extends Connection> clazz) {
            for (Connection connection : availableConnections) {
            if (connection.getClass() == clazz) {
                availableConnections.remove(connection);
                return connection;
            }
            }
            try {
            Connection connection = clazz.newInstance();
                return connection;
            } catch (InstantiationException | IllegalAccessException e) {
                return null;
            }
        }

        public void releaseConnection(Connection connection) {
            availableConnections.add(connection);
        }
    }
    ```

## Advantages
- You Avoid tight coupling between the creator and the concrete products.
- _Single Responsibility Principle_. You can move the product creation code into one place in the program, making the easier to support.
- _Open/Closed Principle_. You can introduce new types of products into the program without breaking existing clint code. (They Can Subtype).
- Methods can cache, but constructors can't.
- Methods have names so they are more readable than using constructors.
  
## Drawbacks
- The code may become more complicated since you need to introduce a lot of new subclasses to implement the pattern.

