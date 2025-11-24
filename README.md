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
1. Create a new Node.js project (if you don't have one already):
```bash
mkdir my-prisma-project
cd my-prisma-project
npm init -y
```

2. Install Prisma CLI as a development dependency:
```bash
npm install prisma --save-dev
```
3. Initialize Prisma in your project:
```bash
npx prisma init
```

This command will create a new `prisma` directory in your project with a `schema.prisma` file and a `.env` file for your database connection.
4. Install Prisma Client as a dependency:
```bash
npm install @prisma/client
```
That's it! You have successfully installed Prisma ORM in your Node.js project. You can now start defining your database schema in the `schema.prisma` file and using Prisma Client to interact with your database.

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