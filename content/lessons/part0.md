---
title: "Part 0: Prerequisites"
subtitle: "Foundational concepts you should know before diving into architecture"
linkTitle: "Part 0: Prerequisites"
weight: 0
type: "docs"
quiz:
  - question: "A client sends a GET request to /api/users and receives a 200 status code. What does this mean?"
    concepts:
      - label: "HTTP Status Codes"
        terms:
          - "200 OK"
          - "GET method"
          - "Request/response cycle"
  - question: "You have a 'customers' table and an 'orders' table. Each order has a customer_id column. What type of key is customer_id in the orders table, and how would you retrieve all orders with their customer names?"
    concepts:
      - label: "Relational Databases"
        terms:
          - "Foreign key"
          - "JOIN"
          - "SELECT"
  - question: "Why does dependency injection matter in software architecture? What would happen if every class created its own dependencies internally?"
    concepts:
      - label: "OOP and Dependency Injection"
        terms:
          - "Interfaces"
          - "Dependency injection"
          - "Loose coupling"
  - question: "A function call inside your application takes nanoseconds. A call to another service over the network takes milliseconds. How does this difference influence architecture decisions?"
    concepts:
      - label: "Networking and Latency"
        terms:
          - "Latency"
          - "Client-server model"
          - "Network overhead"
  - question: "Your team uses Git with feature branches and pull requests. How does this workflow connect to CI/CD and deployment strategies?"
    concepts:
      - label: "Version Control and Deployment"
        terms:
          - "Branching"
          - "Pull requests"
          - "CI/CD"
---

# Part 0: Prerequisites

This is a quick refresher on the foundational concepts you will need throughout this course. If you already know this material, skim the checkpoints at the end of each section. If you can answer them, move on.

---

## HTTP Basics

HTTP is the protocol that powers the web. Every interaction between a client (browser, mobile app, CLI tool) and a server follows the same pattern: the client sends a **request**, the server returns a **response**.

### Methods

| Method   | Purpose                          |
|----------|----------------------------------|
| `GET`    | Retrieve a resource              |
| `POST`   | Create a new resource            |
| `PUT`    | Replace a resource entirely      |
| `PATCH`  | Update part of a resource        |
| `DELETE` | Remove a resource                |

### Status Codes

| Code | Meaning                                      |
|------|----------------------------------------------|
| 200  | OK. The request succeeded.                   |
| 201  | Created. A new resource was created.         |
| 204  | No Content. Success, but nothing to return.  |
| 400  | Bad Request. The client sent invalid data.   |
| 401  | Unauthorized. Authentication is required.    |
| 403  | Forbidden. You are authenticated but not allowed. |
| 404  | Not Found. The resource does not exist.      |
| 500  | Internal Server Error. Something broke on the server. |

### Headers

Three headers you will see constantly:

- **Content-Type**: tells the receiver what format the body is in (e.g. `application/json`).
- **Authorization**: carries credentials, typically a Bearer token.
- **Accept**: tells the server what format the client wants back.

### JSON

JSON is the standard format for API communication. It is human-readable and maps directly to objects in most languages.

### Example Request and Response

<span class="label label-ts">TypeScript</span>

```typescript
// Request
const response = await fetch('https://api.example.com/users/42', {
  method: 'GET',
  headers: {
    'Accept': 'application/json',
    'Authorization': 'Bearer token123'
  }
});

// Response: 200 OK
const user = await response.json();
// { "id": 42, "name": "Alice", "email": "alice@example.com" }
```

<div class="callout tip">

Every architecture you build will communicate over HTTP. Understanding the request/response cycle, status codes, and headers is non-negotiable.

</div>

### Checkpoint

1. What is the difference between PUT and PATCH?
2. A server returns a 401 status code. What should the client do?
3. What does the `Content-Type` header tell the receiver?

---

## Database Fundamentals

Most applications store data in a relational database. Here are the core concepts.

### Tables, Rows, and Columns

A **table** is a collection of related data. Each **row** is a single record. Each **column** is a field on that record.

```
customers
---------
id | name    | email
1  | Alice   | alice@example.com
2  | Bob     | bob@example.com
```

### Keys

- **Primary key**: uniquely identifies each row. Typically `id`.
- **Foreign key**: a column that references the primary key of another table, creating a relationship between them.

### Basic SQL

```sql
-- Read
SELECT name, email FROM customers WHERE id = 1;

-- Create
INSERT INTO customers (name, email) VALUES ('Charlie', 'charlie@example.com');

-- Update
UPDATE customers SET email = 'newalice@example.com' WHERE id = 1;

-- Delete
DELETE FROM customers WHERE id = 2;
```

### JOINs

A JOIN combines rows from two tables based on a related column.

```sql
SELECT customers.name, orders.total
FROM orders
JOIN customers ON orders.customer_id = customers.id;
```

This returns each order alongside the customer name. Without the JOIN, you would need two separate queries and stitch the data together manually.

### Indexes

An index is like the index at the back of a book. Instead of scanning every row to find a match, the database jumps directly to the relevant rows. Without an index on a frequently queried column, performance degrades as the table grows.

### Normalization

Normalization means structuring your tables so that data is not repeated. Instead of storing a customer's name on every order row, you store a `customer_id` that points to the customers table. This avoids inconsistencies (what if the name is spelled differently on two orders?) and makes updates simpler: change the name in one place.

### Transactions

A transaction groups multiple operations into an all-or-nothing unit. If you are transferring money between two accounts, you need the debit and credit to both succeed or both fail. A transaction guarantees this. If any step fails, everything rolls back.

<div class="callout info">

You do not need to be a database expert for this course, but you should be comfortable reading SQL and understanding how tables relate to each other.

</div>

### Checkpoint

1. What is the purpose of a foreign key?
2. Why would you add an index to a column?
3. What happens if a transaction fails halfway through?

---

## Object-Oriented Programming Basics

Architecture patterns rely heavily on OOP concepts, specifically interfaces and dependency injection.

### Classes and Objects

A **class** is a blueprint. An **object** is an instance of that blueprint. A class defines the shape and behavior; an object holds actual data.

### Interfaces

An interface defines a contract: a set of methods that any implementing class must provide. It says *what* something does, not *how*.

<span class="label label-ts">TypeScript</span>

```typescript
interface UserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
}
```

Any class that implements `UserRepository` must provide `findById` and `save`. The caller does not need to know whether the implementation uses a SQL database, an in-memory store, or an external API.

### Dependency Injection

Instead of creating dependencies inside a class, you pass them in from the outside.

<span class="label label-ts">TypeScript</span>

```typescript
// Bad: creates its own dependency
class OrderService {
  private repo = new PostgresOrderRepository();
}

// Good: dependency is injected
class OrderService {
  constructor(private repo: OrderRepository) {}
}
```

With injection, you can swap `PostgresOrderRepository` for `InMemoryOrderRepository` in tests, or replace it entirely without changing `OrderService`.

### Why This Matters for Architecture

Patterns you will encounter throughout this course, such as Repository, Strategy, and Gateway, all rely on interfaces and dependency injection. They let you separate *what* your system does from *how* it does it. This separation is the foundation of flexible, testable architecture.

<div class="callout tip">

If you take one thing from this section: program to an interface, not an implementation. You will see this principle everywhere.

</div>

### Checkpoint

1. What is the difference between an interface and a class?
2. Why is dependency injection preferable to creating dependencies inside a class?
3. How do interfaces enable patterns like Repository and Strategy?

---

## Basic Networking

### Client-Server Model

A **client** makes requests. A **server** listens for requests and sends responses. This is the foundation of nearly every networked application.

### IP Addresses, Ports, and DNS

- An **IP address** identifies a machine on a network (e.g. `192.168.1.10`).
- A **port** identifies a specific service on that machine (e.g. port 443 for HTTPS).
- **DNS** translates a human-readable domain name (`example.com`) into an IP address.

### TCP

TCP (Transmission Control Protocol) provides reliable, ordered delivery of data. If a packet is lost, TCP retransmits it. HTTP runs on top of TCP.

### Latency

Latency is the time it takes for a round trip between two points.

- **Local function call**: nanoseconds.
- **Network call to another service**: milliseconds (often 1-100ms or more).

This difference, often a factor of 1,000,000x, drives most architecture decisions. It is why caching exists, why you batch requests, why you think carefully about service boundaries, and why "just add another network call" is never free.

<div class="callout info">

The single most important number in architecture: a network call is roughly a million times slower than a local function call. Every time you split something across a network boundary, you pay this cost.

</div>

### What Happens When You Type a URL

1. **DNS lookup**: your browser asks a DNS server to resolve the domain to an IP address.
2. **TCP connection**: your browser opens a TCP connection to that IP address on port 443 (HTTPS).
3. **HTTP request**: your browser sends an HTTP GET request for the page.
4. **Response**: the server processes the request and sends back HTML, which the browser renders.

### Checkpoint

1. What does DNS do?
2. Why is the latency difference between local and network calls important for architecture?
3. What are the four steps that happen when you type a URL into a browser?

---

## Version Control (Git)

### Core Concepts

- A **repository** is a project's codebase along with its full history.
- A **commit** is a snapshot of changes at a point in time.
- A **branch** is an independent line of development.

### Basic Workflow

1. Create a **branch** from main.
2. Make changes and **commit** them.
3. **Push** the branch to the remote repository.
4. Open a **pull request** for review.
5. **Merge** into main after approval.

### Why It Matters for Architecture

CI/CD pipelines, deployment strategies (blue-green, canary), and team collaboration workflows all assume you are using Git. You cannot discuss deployment architecture without understanding branching and merging.

### Checkpoint

1. What is the purpose of a pull request?
2. How does branching enable CI/CD pipelines?

---

## Command Line Basics

### Navigation and Execution

- `pwd`: print the current directory.
- `ls`: list files in the current directory.
- `cd <dir>`: change to a directory.
- Run scripts with `node script.js`, `python script.py`, or `./run.sh`.

### Environment Variables

Environment variables are key-value pairs available to any process running in your shell. They are used for configuration: database URLs, API keys, feature flags.

```bash
# Set a variable
export DATABASE_URL="postgres://localhost:5432/mydb"

# Use it in your application
echo $DATABASE_URL
```

### Why It Matters

Infrastructure, deployment, and configuration all happen on the command line. Docker, Kubernetes, Terraform, CI/CD scripts: you will interact with all of them through a terminal.

### Checkpoint

1. What is an environment variable, and why is it used for configuration?
2. How would you check which directory you are currently in?

---

## Ready to Start?

If you can answer the checkpoint questions in each section above, you have the prerequisites covered. Here is a quick checklist:

- [ ] I understand HTTP methods, status codes, and headers.
- [ ] I can read basic SQL and understand table relationships.
- [ ] I know what interfaces and dependency injection are.
- [ ] I understand the client-server model and why latency matters.
- [ ] I know the basic Git workflow.
- [ ] I am comfortable navigating the command line.

If any section felt unfamiliar, spend 30 minutes with a tutorial on that topic before continuing. This course builds on these foundations constantly.

**Next up: [Part 1: What is Software Architecture?]({{< ref "part1" >}})**

{{< quiz >}}
