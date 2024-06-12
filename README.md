# springboot-mongodb-poc


To build a Spring Boot application with MongoDB that performs CRUD (Create, Read, Update, Delete) operations, follow these steps:
1. Set Up Your Spring Boot Project
   Initialize the Project:
   You can use Spring Initializr (https://start.spring.io/) to generate a Spring Boot project with the necessary dependencies:
   Spring Web
   Spring Data MongoDB
   Project Structure: Your project structure should look like this:
   css
   Copy code
   ├── src
   │   ├── main
   │   │   ├── java
   │   │   │   └── com
   │   │   │       └── example
   │   │   │           └── demo
   │   │   │               ├── DemoApplication.java
   │   │   │               ├── controller
   │   │   │               ├── model
   │   │   │               ├── repository
   │   │   │               └── service
   │   │   └── resources
   │   │       └── application.properties
   └── pom.xml


2. Add Dependencies
   In your pom.xml, add the following dependencies:
   <dependencies>
   <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-mongodb</artifactId>
   </dependency>
   <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-test</artifactId>
   <scope>test</scope>
   </dependency>
   </dependencies>

3. Configure MongoDB
   In your application.properties file, configure the MongoDB connection:
   spring.data.mongodb.host=localhost
   spring.data.mongodb.port=27017
   spring.data.mongodb.database=your_database_name

4. Create the Model
   Create a model class representing the data structure you want to work with. For example, a simple User class:
   package com.example.demo.model;

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

@Document(collection = "users")
public class User {
@Id
private String id;
private String name;
private String email;

    // Getters and Setters
}

5. Create the Repository
   Create a repository interface extending MongoRepository to interact with MongoDB:
   package com.example.demo.repository;

import com.example.demo.model.User;
import org.springframework.data.mongodb.repository.MongoRepository;

public interface UserRepository extends MongoRepository<User, String> {
}

6. Create the Service
   Create a service class to handle business logic:
   package com.example.demo.service;

import com.example.demo.model.User;
import com.example.demo.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class UserService {
@Autowired
private UserRepository userRepository;

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    public Optional<User> getUserById(String id) {
        return userRepository.findById(id);
    }

    public User createUser(User user) {
        return userRepository.save(user);
    }

    public User updateUser(String id, User user) {
        if (userRepository.existsById(id)) {
            user.setId(id);
            return userRepository.save(user);
        } else {
            return null;
        }
    }

    public void deleteUser(String id) {
        userRepository.deleteById(id);
    }
}

7. Create the Controller
   Create a REST controller to handle HTTP requests:
   package com.example.demo.controller;

import com.example.demo.model.User;
import com.example.demo.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/api/users")
public class UserController {
@Autowired
private UserService userService;

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable String id) {
        Optional<User> user = userService.getUserById(id);
        return user.map(ResponseEntity::ok).orElseGet(() -> ResponseEntity.notFound().build());
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.createUser(user);
    }

    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable String id, @RequestBody User user) {
        User updatedUser = userService.updateUser(id, user);
        if (updatedUser != null) {
            return ResponseEntity.ok(updatedUser);
        } else {
            return ResponseEntity.notFound().build();
        }
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable String id) {
        if (userService.getUserById(id).isPresent()) {
            userService.deleteUser(id);
            return ResponseEntity.noContent().build();
        } else {
            return ResponseEntity.notFound().build();
        }
    }
}

8. Running the Application
   Main Application Class: Ensure your main application class is properly set up:

package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
public static void main(String[] args) {
SpringApplication.run(DemoApplication.class, args);
}
}
Run the Application:
Use your IDE to run the application or run the following command in your terminal:
bash
Copy code
./mvnw spring-boot:run


9. Testing the Application
   Using cURL or Postman:
   Create a User:
   bash
   Copy code
   curl -X POST -H "Content-Type: application/json" -d '{"name": "John Doe", "email": "john@example.com"}' http://localhost:8080/api/users


Get All Users:
bash
Copy code
curl http://localhost:8080/api/users


Get User by ID:
bash
Copy code
curl http://localhost:8080/api/users/{id}


Update a User:
bash
Copy code
curl -X PUT -H "Content-Type: application/json" -d '{"name": "Jane Doe", "email": "jane@example.com"}' http://localhost:8080/api/users/{id}

Delete a User:
bash
Copy code
curl -X DELETE http://localhost:8080/api/users/{id}
Summary
You have created a Spring Boot application with MongoDB integration that supports CRUD operations. The steps included setting up the project, configuring MongoDB, creating the model, repository, service, and controller layers, and finally running and testing the application.












Basic MongoDB Queries

1. Insert Documents
   Insert documents into a collection:
   db.collectionName.insertOne({ key: "value" });

2. Find Documents
   Retrieve documents from a collection:
   db.collectionName.find();

Find documents matching a specific condition:
db.collectionName.find({ key: "value" });

Find documents with conditions using logical operators:

db.collectionName.find({ $and: [{ key1: "value1" }, { key2: "value2" }] });
db.collectionName.find({ $or: [{ key1: "value1" }, { key2: "value2" }] });

3. Update Documents
   Update documents in a collection:

db.collectionName.updateOne({ key: "value" }, { $set: { newKey: "newValue" } });

Update multiple documents:
db.collectionName.updateMany({ key: "value" }, { $set: { newKey: "newValue" } });

4. Delete Documents
   Delete documents from a collection:
   db.collectionName.deleteOne({ key: "value" });

Delete multiple documents:

db.collectionName.deleteMany({ key: "value" });

5. Aggregation
   Perform aggregation operations:
   db.collectionName.aggregate([
   { $match: { key: "value" } },
   { $group: { _id: "$key", count: { $sum: 1 } } },
   { $sort: { count: -1 } },
   ]);

6. Indexing
   Create indexes for better query performance:
   db.collectionName.createIndex({ key: 1 }); // Ascending index
   db.collectionName.createIndex({ key: -1 }); // Descending index

7. Sorting
   Sort documents based on a field:
   db.collectionName.find().sort({ key: 1 }); // Ascending order
   db.collectionName.find().sort({ key: -1 }); // Descending order


8. Limiting Results
   Limit the number of documents returned:
   db.collectionName.find().limit(10); // Limit to 10 documents

9. Skipping Results
   Skip a specified number of documents:
   db.collectionName.find().skip(5); // Skip first 5 documents

These are some of the basic MongoDB queries. You can combine them to perform more complex operations as needed for your application.

