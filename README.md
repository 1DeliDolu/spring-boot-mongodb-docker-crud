
# Spring Boot MongoDB Library API (Docker)

A Spring Boot REST API demonstrating how to use **MongoDB** with **Spring Data MongoDB** to manage a simple `Book` collection. The project includes a `MongoRepository` with a custom query method and REST endpoints for CRUD-like operations.

## Features
- MongoDB connection via `spring.data.mongodb.uri` :contentReference[oaicite:2]{index=2}
- Optional Java-based MongoDB configuration with `MongoConfig` (`AbstractMongoClientConfiguration`) :contentReference[oaicite:3]{index=3}
- `Book` document stored in the `books` collection :contentReference[oaicite:4]{index=4}
- Repository layer using `MongoRepository<Book, String>`
- Custom query: `findByGenre(String genre)` :contentReference[oaicite:5]{index=5}
- REST endpoints for create, filter by genre, update, and delete :contentReference[oaicite:6]{index=6}

## Tech Stack
- Java + Spring Boot (Web)
- Spring Data MongoDB
- MongoDB (Docker)
- Maven

## Domain Model
### Book
Stored as documents in `books` collection. :contentReference[oaicite:7]{index=7}

Fields:
- `id` (String, Mongo `_id`)
- `title` (String)
- `author` (String)
- `price` (double)
- `genre` (String) :contentReference[oaicite:8]{index=8}

## Prerequisites
- Java 17+ (or your project’s Java version)
- Maven
- Docker + Docker Compose
- (Optional) `mongosh` for local checks

---

## Quick Start (MongoDB with Docker)

### Option A — docker run
```bash
docker run -d --name mongodb \
  -p 27017:27017 \
  -e MONGO_INITDB_ROOT_USERNAME=root \
  -e MONGO_INITDB_ROOT_PASSWORD='D0cker!' \
  -v mongo_data:/data/db \
  mongo:7


Test connection:

```bash
mongosh "mongodb://root:D0cker!@localhost:27017/admin"
```

### Option B — docker compose (recommended)

Create `docker-compose.yml`:

```yaml
services:
  mongodb:
    image: mongo:7
    container_name: mongodb
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: "D0cker!"
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```

Run:

```bash
docker compose up -d
```

---

## Application Configuration

### 1) application.properties

Set MongoDB connection URI like this (DB: `library`, auth DB: `admin`): 

```properties
spring.application.name=code
spring.data.mongodb.uri=mongodb://root:D0cker!@localhost:27017/library?authSource=admin
```

### 2) (Optional) Java config: MongoConfig.java

If you use `MongoConfig extends AbstractMongoClientConfiguration`, it can take precedence over properties. 

---

## Build & Run

```bash
mvn clean install
mvn spring-boot:run
```

App will run at:

* `http://localhost:8080`

---

## API Endpoints

| Method | Endpoint           | Description                   |
| ------ | ------------------ | ----------------------------- |
| POST   | `/createBook`      | Create a new book             |
| GET    | `/genre/{genre}`   | List books by genre           |
| PUT    | `/updateBook/{id}` | Update an existing book by id |
| DELETE | `/deleteBook/{id}` | Delete a book by id           |

Endpoints are implemented in `BookController` and use `BookRepository` (`MongoRepository`).  

---

## Example Requests (cURL)

### Create a Book

```bash
curl --location 'http://localhost:8080/createBook' \
  --header 'Content-Type: application/json' \
  --data '{ "title": "Spring Boot Guide", "author": "John Doe", "price": 29.99, "genre": "Programming" }'
```

Sample response includes a generated `id`. 

### Read Books by Genre

```bash
curl --location 'http://localhost:8080/genre/Programming'
```

Returns an array of matching books. 

### Update a Book

```bash
curl --location --request PUT 'http://localhost:8080/updateBook/<BOOK_ID>' \
  --header 'Content-Type: application/json' \
  --data '{ "title": "Spring Boot Guide", "author": "John", "price": 19.99, "genre": "Programming" }'
```

Update flow sets the `id` from the path and saves the document. 

### Delete a Book

```bash
curl --location --request DELETE 'http://localhost:8080/deleteBook/<BOOK_ID>'
```

On success, no response body is returned. 

---

## Checking the Database (mongosh)

```bash
mongosh "mongodb://root:D0cker!@localhost:27017/admin"
use library
show collections
db.books.find()
```

The lab verifies data inside `library` and `books` collection like this. 

---

## Project Structure (Typical)

```txt
src/main/java/com/project/code
├── Book.java
├── BookController.java
├── BookRepository.java
└── MongoConfig.java   (optional)

src/main/resources
└── application.properties
```

## Security Note

Credentials are hardcoded here for learning/demo. For real deployments, use environment variables or secrets.

## License

MIT (or your preferred license)

```

İstersen bunun yanına:
- **`.gitignore` (IntelliJ + Maven + Java)**  
- ve repo için **LICENSE (MIT)** dosyası içeriklerini de hemen ekleyeyim.
```
