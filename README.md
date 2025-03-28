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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
Reflection Publisher-1 Questions
Observer Pattern Interface vs. Struct:
In the Observer pattern, an interface (or trait in Rust) defines a contract for subscribers. In this case, a single Subscriber struct suffices because the notification logic is centralized in the NotificationService. However, a trait could be useful if subscribers needed varied update behaviors (e.g., email vs. SMS notifications).

Data Structure Choice (Vec vs. DashMap):
DashMap (a concurrent hashmap) is necessary here because:

It ensures thread-safe access to subscribers during concurrent operations (e.g., adding/deleting subscribers while notifying).

It provides O(1) lookups by id/url, unlike Vec, which would require O(n) searches.

Thread Safety (DashMap vs. Singleton):
Rust’s ownership model enforces thread safety at compile time. DashMap is chosen over a Singleton pattern because:

Singletons in Rust are cumbersome to implement safely (requiring Arc<Mutex<T>> or similar).

DashMap handles concurrent access natively, aligning with Rust’s zero-cost abstractions for concurrency.

#### Reflection Publisher-2
Separation of Service and Repository:
Separating "Service" and "Repository" from the Model aligns with the Single Responsibility Principle.

Repository: Handles data access (e.g., CRUD operations).

Service: Encapsulates business logic (e.g., validation, workflow).
This separation reduces code duplication and makes the system more maintainable.

Impact of Using Only Models:
If we used only Models:

Models would become bloated with both data and business logic.

Code complexity would increase (e.g., a Product model handling HTTP requests).

Tight coupling would make it harder to modify or test components independently.
#### Reflection Publisher-3
1. This tutorial uses the Push Model of the Observer Pattern. The NotificationService in the Main app actively pushes data (e.g., product_title, action) to subscribers via HTTP requests (e.g., subscriber.update(payload)). Subscribers do not request data; they passively receive updates when events (create/delete/promote) occur.

2. Pros/Cons of the Pull Model
Advantages:

- Subscribers could fetch data on-demand, reducing unnecessary network traffic.

- Publisher (Main app) avoids managing subscriber-specific data formatting.

Disadvantages:

- Latency: Subscribers might miss real-time updates, requiring polling (e.g., periodic checks).

- Complexity: Subscribers need to implement logic to query the Main app for updates.

- Resource Overhead: Frequent polling could strain the Main app (e.g., /product/updates endpoint).

3. Impact of Removing Multi-Threading
- Performance Degradation: Synchronous HTTP requests would block the Main app’s execution until all subscribers acknowledge the notification.

- Scalability Issues: With many subscribers, the Main app could become unresponsive.

- User Experience: Slow response times for CRUD operations (e.g., POST /product).