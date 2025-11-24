<!-- markdownlint-disable MD012 MD026 MD001 MD022 MD032 MD029 MD019 MD034 MD031 MD047 MD040 MD009 MD058 MD024  -->

<div>

 <img src="https://i.ibb.co.com/mfDPTVn/prisma.jpg" style="width:100%; height:auto;">

</div>

# Prisma ORM

Prisma is an open-source database toolkit that simplifies database access and management for developers. It provides a type-safe and intuitive way to interact with databases, making it easier to build and maintain applications. Prisma consists of three main components: Prisma Client, Prisma Migrate, and Prisma Studio.

> ORM stands for Object-Relational Mapping, a programming technique used to convert data between incompatible type systems in object-oriented programming languages and relational databases.

**Prisma Client**
Prisma Client is an auto-generated query builder that allows developers to interact with their databases using a type-safe API. It supports various databases, including PostgreSQL, MySQL, SQLite, and MongoDB. Prisma Client provides a simple and efficient way to perform CRUD (Create, Read, Update, Delete) operations on database records.

**Prisma Migrate**
Prisma Migrate is a database migration tool that helps developers manage schema changes in their databases. It allows developers to define their database schema using a declarative syntax and automatically generates migration scripts to apply those changes to the database. Prisma Migrate ensures that database schema changes are versioned and can be easily rolled back if needed.

**Prisma Studio**
Prisma Studio is a web-based GUI that allows developers to explore and manipulate their database data visually. It provides an intuitive interface for viewing, editing, and deleting records in the database. Prisma Studio makes it easy for developers to inspect their data and perform operations without writing SQL queries.

Overall, Prisma ORM simplifies database access and management for developers by providing a type-safe and intuitive way to interact with databases. It streamlines the development process and helps developers build robust and scalable applications.

## Installation
To install Prisma ORM, you need to have Node.js and npm (Node Package Manager) installed on your machine. Once you have them set up, you can follow these steps to install Prisma:
**1. Create a new Node.js project (if you don't have one already):**
```bash
mkdir my-prisma-project
cd my-prisma-project
npm init -y
```

**2. Install Prisma CLI as a development dependency:**
```bash
npm install prisma --save-dev
```
**3. Initialize Prisma in your project:**
```bash
npx prisma init
```

This generates:
- A `prisma` directory with a `schema.prisma` file where you can define your database schema.
- A `.env` file for your database connection URL.
```bash
prisma/
  schema.prisma
.env
```

**4. Configure Database**

Before using Prisma, you need to configure your database connection in the `.env` file. Update the `DATABASE_URL` variable with your database connection string. For example, for a PostgreSQL database, it might look like this:
```
DATABASE_URL="postgresql://user:password@localhost:5432/mydatabase"
```



**5. Install Prisma Client as a dependency:**
```bash
npm install @prisma/client
```
That's it! You have successfully installed Prisma ORM in your Node.js project. You can now start defining your database schema in the `schema.prisma` file and using Prisma Client to interact with your database.

**6. Define Your Data Models**

Inside prisma/schema.prisma, add a model:
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql" // or "mysql", "sqlite"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  createdAt DateTime @default(now())
}
```

**7. Migrate Your Database**
After defining your data models, you need to create and apply a migration to set up your database schema:
```bash
npx prisma migrate dev --name init
```

This will:
- Create a new migration file in the `prisma/migrations` directory.
- Apply the migration to your database, creating the necessary tables based on your data models.
- Generate Prisma Client based on your schema.


## Usage
Once you have installed Prisma ORM and set up your database schema, you can start using Prisma Client to interact with your database. Here's a basic example of how to use Prisma Client in your Node.js application:
1. Import Prisma Client in your JavaScript or TypeScript file:
```javascript
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();
```

2. Use Prisma Client to perform CRUD operations. For example, let's say you have a `User` model defined in your `schema.prisma` file:
```prisma
model User {
  id    Int     @id @default(autoincrement())
  name  String
  email String  @unique
}
```

You can create, read, update, and delete users using Prisma Client as follows:
```javascript
// Create a new user
async function createUser() {
  const newUser = await prisma.user.create({
    data: {
      name: 'John Doe',
      email: 'john.doe@example.com',
    },
  });
  console.log('New user created:', newUser);
}
// Read all users
async function getUsers() {
  const users = await prisma.user.findMany();
  console.log('All users:', users);
}
// Update a user
async function updateUser(userId) {
  const updatedUser = await prisma.user.update({
    where: { id: userId },
    data: { name: 'Jane Doe' },
  });
  console.log('User updated:', updatedUser);
}
// Delete a user
async function deleteUser(userId) {
  const deletedUser = await prisma.user.delete({
    where: { id: userId },
  });
  console.log('User deleted:', deletedUser);
}
```

3. Don't forget to call the functions and handle errors appropriately:
```javascript
async function main() {
  try {
    await createUser();
    await getUsers();
    await updateUser(1); // Replace with the actual user ID
    await deleteUser(1); // Replace with the actual user ID
  } catch (error) {
    console.error('Error:', error);
  } finally {
    await prisma.$disconnect();
  }
}
main();
```

Overall, Prisma Client provides a simple and efficient way to interact with your database using a type-safe API. You can perform various operations on your database records with ease, making it a powerful tool for building robust applications.


#### Advanced Prisma Use Cases

**1. Advanced Filtering, Sorting & Pagination (Real Apps)**

Useful for building dashboards, admin panels, and APIs like `/products?search=...&sort=...&page=....`

**Example: Search + Pagination + Sorting**
```typescript
const { q, page = 1, limit = 10, sort = "createdAt", order = "desc" } = req.query;

const products = await prisma.product.findMany({
  where: {
    OR: [
      { name: { contains: q, mode: "insensitive" } },
      { description: { contains: q, mode: "insensitive" } },
    ],
  },
  orderBy: { [sort]: order },
  skip: (page - 1) * limit,
  take: Number(limit),
  select: {
    id: true,
    name: true,
    price: true,
    stock: true,
  },
});
```

**2. Prisma Transactions (Atomic Operations)**

Critical when working with financial apps, wallet systems, or anything requiring ACID compliance.

**Example: Payment Transaction (Atomic)**
```typescript
await prisma.$transaction(async (prisma) => {
  const user = await prisma.user.update({
    where: { id: userId },
    data: { balance: { decrement: amount } },
  });

  await prisma.transaction.create({
    data: {
      userId: user.id,
      amount,
      type: "DEBIT",
    },
  });
});
```

**3. Optimistic Locking (Concurrency Control)**

Prevents race-conditions in high-traffic apps.

**Example: Updating a product inventory safely**

```typescript
const product = await prisma.product.update({
  where: {
    id_version: {
      id: productId,
      version: currentVersion,
    },
  },
  data: {
    stock: { decrement: 1 },
    version: { increment: 1 },
  },
});
```

**4. Nested Writes (Complex Relations in One Query)**

Professional apps often require creating related entities in one atomic operation.

**Example: Create User with Address + Orders at once**

```typescript
const user = await prisma.user.create({
  data: {
    name: "Alice",
    email: "alice@example.com",
    address: {
      create: {
        street: "123 Main St",
        city: "New York",
      },
    },
    orders: {
      create: [
        { total: 40 },
        { total: 120 },
      ],
    },
  },
});
```

**5. Complex Relation Queries (Joins)**

Used in analytics dashboards and admin APIs.

**Example: Get user with orders & order items**

```typescript
const user = await prisma.user.findUnique({
  where: { id: userId },
  include: {
    orders: {
      include: {
        items: true,
      },
    },
  },
});
```

**6. Bulk Operations (High Performance)**

Used in e-commerce, data imports, and report generation.

**Example: Create many records at once**

```typescript
await prisma.orderItem.createMany({
  data: items.map((i) => ({
    productId: i.id,
    quantity: i.qty,
    price: i.price,
    orderId,
  })),
});
```

Example: Bulk Update
```typescript
await prisma.product.updateMany({
  where: { stock: { lt: 5 } },
  data: { isLowStock: true },
});
```

**7. Partial Selects (Performance Optimization)**

Used to reduce database load—best practice in large systems.

**Example: Select only what you need**

```typescript
const minimalUser = await prisma.user.findUnique({
  where: { id },
  select: {
    id: true,
    email: true,
  },
});
```

**8. Soft Deletes (Enterprise Requirement)**

Instead of deleting data → mark as deleted.

**Model**

```prisma
model User {
  id        Int      @id @default(autoincrement())
  name      String
  deletedAt DateTime?
}
```
**Soft Delete Query**

```typescript
await prisma.user.update({
  where: { id: userId },
  data: { deletedAt: new Date() },
});
```

**Global filter middleware**
```typescript
prisma.$use(async (params, next) => {
  if (params.model === "User" && params.action === "findMany") {
    params.args.where = {
      ...params.args.where,
      deletedAt: null,
    };
  }
  return next(params);
});
```

**9. Prisma Middleware (Logging, Validation, Metrics)**

Used in production APIs to log performance or block invalid requests.

**Example: Query Logger**
```typescript
prisma.$use(async (params, next) => {
  const start = Date.now();
  const result = await next(params);
  const duration = Date.now() - start;
  console.log(`Query ${params.model}.${params.action} took ${duration}ms`);
  return result;
});
```

**10. Handling Failures with try/catch + PrismaError**

Critical for resilient, fault-tolerant systems.

**Example: Unique constraint handling**
```typescript
try {
  await prisma.user.create({
    data: { email },
  });
} catch (e) {
  if (e.code === "P2002") {
    throw new Error("Email already exists");
  }
}
```

**11. Raw SQL (When You Need Ultimate Control)**

Used for analytics, complex aggregations, or performance-heavy tasks.

**Example: Execute custom SQL**
```typescript
const result = await prisma.$queryRaw`SELECT COUNT(*) FROM "User" WHERE "createdAt" > NOW() - INTERVAL '7 days'`;
console.log('New users in last 7 days:', result);
```

**12. Prisma in Microservices / Serverless Architecture**

Used in AWS Lambda, Vercel, Cloud Functions.

**Example: Reuse Prisma Client in Serverless APIs**
```typescript
let prisma;

if (!global.prisma) {
  global.prisma = new PrismaClient();
}

prisma = global.prisma;

export default prisma;
```

**13. Advanced Aggregations (Reporting, Analytics)**
**Example: Sales Report**
```typescript
const report = await prisma.order.aggregate({
  _sum: { total: true },
  _avg: { total: true },
  _count: true,
  where: {
    createdAt: {
      gte: new Date("2025-01-01"),
    },
  },
});
```

**14. Many-to-Many Relations (Tags, Categories)**
**Example: Assign tags to a blog post**
```typescript
await prisma.post.update({
  where: { id: postId },
  data: {
    tags: {
      connect: tagIds.map((id) => ({ id })),
    },
  },
});
```

**15. Seeding Your Database (Production & Dev)**
**Example: `seed.ts`**
```typescript
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();
async function main() {
  await prisma.user.createMany({
    data: [
      { name: 'Alice', email: 'alice@example.com' },
      { name: 'Bob', email: 'bob@example.com' },
     ],
  });
}
main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

**Run seeding:**
```bash
npx prisma db seed
```
