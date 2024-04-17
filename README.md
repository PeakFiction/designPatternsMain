# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [V] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [V] Commit: `Create Subscriber model struct.`
    -   [V] Commit: `Create Notification model struct.`
    -   [V] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [V] Commit: `Implement add function in Subscriber repository.`
    -   [V] Commit: `Implement list_all function in Subscriber repository.`
    -   [V] Commit: `Implement delete function in Subscriber repository.`
    -   [V] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [V] Commit: `Create Notification service struct skeleton.`
    -   [V] Commit: `Implement subscribe function in Notification service.`
    -   [V] Commit: `Implement subscribe function in Notification controller.`
    -   [V] Commit: `Implement unsubscribe function in Notification service.`
    -   [V] Commit: `Implement unsubscribe function in Notification controller.`
    -   [V] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [V] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [V] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [V] Commit: `Implement publish function in Program service and Program controller.`
    -   [V] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [V] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

*In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?*

The Observer pattern lets one object (the publisher) notify others (subscribers) when its state changes. Subscribers come in all shapes and sizes, each reacting uniquely to the publisher's updates. To keep things flexible, the publisher ideally shouldn't care about the specific subscriber types.

In the BambangShop app, if there's only one kind of subscriber with a set way of handling updates, you might not need a separate subscriber interface. Rust's ownership system can handle loose coupling here. A single model struct would suffice. However, if you expect different subscriber types or want more flexibility for future additions, consider using an interface (trait in Rust). This defines a common update method for all subscribers, allowing for polymorphism and easier addition of new subscriber types without modifying the publisher itself.

*id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?*

While storing data in a Vec lets you add elements freely, ensuring uniqueness becomes your responsibility. This means looping through the entire Vec to check for duplicates before adding anything new. This gets slower and error-prone with larger datasets.

For cases like this one, where Product IDs and Subscriber URLs must be unique, using a DashMap (or similar structures like HashSet) is much better. These data structures automatically enforce uniqueness, saving you the hassle and improving efficiency. DashMap is a special bonus because it's a concurrent hash map in Rust, allowing for safe access from multiple threads at once.

*When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?*

The Singleton pattern ensures only one instance of a class exists at a time, with a single access point. In Rust, this is typically achieved through lazy_static. However, directly implementing it for shared state introduces complexities like ensuring thread safety with mutexes. DashMap offers a thread-safe HashMap implementation, abstracting away these complexities. It's widely used and well-tested, making it preferable for managing shared state like the List of Subscribers (SUBSCRIBERS) in Rust due to its simplicity, reliability, and alignment with Rust's emphasis on safety and performance.

#### Reflection Publisher-2
*In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?*

In order to achieve cleaner and more manageable code, we can separate the concerns of a Model in MVC by following the Single Responsibility Principle (SRP). SRP dictates that a class should have a single purpose. Traditionally, a Model holds the application's data structures and logic, but this can lead to complex code. By utilizing the Repository pattern, data storage and retrieval become isolated from the core logic, allowing for easier switching between databases. Furthermore, a Service layer can be introduced to encapsulate the business logic, promoting reusability and clear separation between the different functionalities within the system. This approach ensures each component has a well-defined role, making the application more maintainable and flexible.

*What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?*

Separating Services and Repositories from Models offers significant advantages in managing complexity. When everything resides in the Model (Product, Subscriber, Notification), responsibilities balloon.  This includes core product logic, data access like database calls, and potentially even external interactions like sending notifications.  Imagine an Order model that not only calculates the total price but also persists the order details to a database.  This tight coupling makes changes to the order calculation logic difficult and ties the Order model to a specific data storage solution.  By separating concerns, Services can manage complex logic like order calculation, while Repositories handle clean data access, keeping each Model focused and maintainable.

*Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.*

I've been looking into Postman as a tool to test our group project's API functionality. It seems like a great way to send simulated requests and see the server's response, helping us identify bugs early on. I'm particularly interested in features like building collections to organize different API calls and environment variables to easily switch between test configurations. This could be a real time saver for our project and future ones, allowing us to focus on core development instead of writing repetitive test scripts.

#### Reflection Publisher-3
*Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?*

The tutorial's code leverages the Observer Pattern's Push model. Here, the NotificationService acts as the publisher (or subject) actively pushing data (notification payload) to its subscribers (observers). This is evident in the notify method's for-loop, where it iterates through subscribed observers and calls their update method with the notification details. This eliminates the need for subscribers to constantly pull data – instead, the publisher takes the initiative to broadcast updates whenever a relevant event happens.

*What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)*

Unlike the Push model where updates are broadcasted, the Pull model in the Observer Pattern has subscribers actively fetching data from the publisher. This tighter coupling exposes the publisher's internal state to subscribers. However, it offers advantages: subscribers control update frequency and data retrieval, ideal for scenarios with varying needs or limited bandwidth. Additionally, network traffic is minimized since updates are only requested, not constantly pushed.  The downside is increased complexity.  Subscribers need logic to request and handle updates, and latency can arise if updates aren't pulled frequently, potentially leading to stale data.

*Explain what will happen to the program if we decide to not use multi-threading in the notification process.*

If we decide not to use multi-threading in the notification process, the program will likely experience blocking behavior during notification sending. In the current implementation, each notification is sent to subscribers using a separate thread, allowing the program to continue executing without waiting for each notification to be delivered. Without multi-threading, sending notifications to subscribers would occur sequentially, meaning that the program would wait for each notification to be sent before moving on to the next one. This could result in significant delays, especially if there are many subscribers or if sending notifications involves network requests that take a non-trivial amount of time. As a consequence, the overall performance and responsiveness of the program would be negatively impacted. Users may experience delays in response times, and the program's ability to handle concurrent requests or events could be limited. In scenarios where timely notification delivery is critical, such as real-time systems or applications with high concurrency requirements, not using multi-threading for notification processing could lead to suboptimal performance and user experience.

