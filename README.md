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
1. Using a `trait` instead of just using a single struct is a good design choice in this case. Using an interface is good practice because different types of subscribers may handle notifications differently. Additionally, we might want to extend the notification system in the future. If we only use a single struct, it would limit flexibility, and make it harder to extend in the future when different subscriber behaviors are needed. While a single `struct` could work for a basic implementation, it is generally recommended to use a `trait` instead.
2. Using a `DashMap` is generally more recommended than a `Vec`. Using `DashMap` has multiple advantages compared to `Vec`. Using `DashMap` allows indexing in O(1) time when finding products by ID, whereas using `Vec` requires a worst-case time complexity of O(n). Using a `DashMap` also handles insertion and deletion of products more efficiently, since using `Vec` requires shifting elements, which may require a bigger time complexity. Using a `DashMap` also helps prevent duplicates, since one key can only map to one value. Using `Vec` requires a manual check.
3. `DashMap` is required in this case. `DashMap` is a custom implementation of `HashMap` optimized for high-performance multithreaded access. Using the Singleton pattern alone does not inherently provide thread safety. If Singleton is used without `DashMap`, we would need more boilerplate code to ensure thread safety. Singleton can be combined with `DashMap` to achieve both single-instance control and efficient thread-safe access. By using `DashMap`, we can have multiple threads reading simulatenously, and also multiple threads writing safely.

#### Reflection Publisher-2
1. Separating the Service and Repository layers from the Model aligns with key software design principles such as the Separation of Concern and the Single Responsibility Principle. By introducing the Service and Repository layers, this ensures that each layer has a clear and distinct role, improving its modularity. Additionally, the Model in MVC can become overloaded if it also needs to handle both business logic and data persistence. By creating a Repository, we delegate the database operations for data persistence to a separate class, and by creating a service, we ensure that business logic is also handled separately.
2. If we only use Model in our system, all the responsibilities would be concentrated within Model. In this approach, we would be putting all the logic, rules, and data access mechanisms inside the model. This could be problematic as this causes the model to be overloaded with too many responsibilities, as this model would manage the subscribers and trigger the notifications, all at the same time. The interactions between the `Product`, `Subcriber`, and `Notification` models will result in increased complexity due to too many codes being in one single place.
3. Postman allows an organized way to organize requests within directories. Postman allows sending many types of requests to APIS, such as GET, POST, PUT, and DELETE. Postman provides detailed views of the responses, such as the status code, headers, and body content, which allows us to identify and debug issues within the code. Postman also allows running all the tests automatically, all at once. Additionally, Postman also allows auto-generation of documentation for our API and share it, saving time on manually writing the documentation ourselves.

#### Reflection Publisher-3
1. This tutorial uses the **Push model**. This can be seen in the `src/model/subscriber.rs` and `src/service/notification.rs` files. The subscriber receives the data directly from the notification when `update` is called. In the `notify` function of `NotificationService`, the notification data is directly provided into the function, and a new `Notification` object is created with the provided data. The function retrieves all the subscribers subscribed for that product type and then sends the notification directly to the subscribers.
2. **Advantages:**  
   Using the Pull model provides better decoupling between the notification service and the subscribers, since the notification service only notifies the subscribers of a change, leaving them to pull the data themselves. The service doesn't have to be aware of how the subscribers handle the data. Using the Pull model also allows more efficient data retrieval, as the subscribers can fetch only exactly the data they need, while avoiding unnecessary updates from the service. This is efficient if the subscriber needs only a subset of the data, and pulling the data themselves allow them to control when to load the data. The subject also doesn't need to package data for the subscribers, as it just sends a notification. This reduces the complexity in cases where the subscribers have varied data requirements.  

   **Disadvantages:**  
   Since the subscribers pull the data themselves at a later time, there is a possibility that the data may be out of sync with the subject's current state. The observer may not always be aware of the exact moment a change occurs. If many observers request data simulatenously, it can lead to multiple method calls, causing an overhead and cause inefficiency, especially if large datasets are pulled. Observers must also implement logic to fetch the data (requiring knowing how to query the subject), leading to redundant code.
3. If we do not use multi-threading, many problems may occur in the long run. Without multi-threading, the notification sending process will be executed sequentially, meaning that it will only handle one subscriber at a time, making the program's runtime significantly longer and inefficient. This results in notifications being sent late to subscribers, leading to problems, especially if they need the information urgently. Additionally, the lack of multi-threading can block other important tasks of the application from running, and many parts of the system can become unresponsive. Therefore, using multi-threading is crucial in this case.
