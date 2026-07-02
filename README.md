<div align="center">

# ⚡ Neon + Drizzle + Next.js 16

### A minimal, production-ready setup guide for connecting **Drizzle ORM** to a **Neon Postgres** database in a **Next.js 16** app.

[![Next.js](https://img.shields.io/badge/Next.js-16-000000?style=for-the-badge&logo=next.js&logoColor=white)](https://nextjs.org/)
[![Drizzle ORM](https://img.shields.io/badge/Drizzle_ORM-C5F74F?style=for-the-badge&logo=drizzle&logoColor=black)](https://orm.drizzle.team/)
[![Neon](https://img.shields.io/badge/Neon-Postgres-00E599?style=for-the-badge&logo=postgresql&logoColor=white)](https://neon.tech/)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)

[![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](#license)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](#)
[![Made with ❤️](https://img.shields.io/badge/made%20with-%E2%9D%A4%EF%B8%8F-red?style=flat-square)](#)

</div>

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Installation](#-installation)
- [Project Structure](#-project-structure)
- [Setup](#-setup)
  - [1. Create a Neon Project](#1-create-a-neon-project)
  - [2. Configure Environment Variables](#2-configure-environment-variables)
  - [3. Drizzle Config](#3-drizzle-config)
  - [4. Connect Drizzle ORM](#4-connect-drizzle-orm-to-your-database)
  - [5. Database Schema](#5-database-schema)
- [Migrations](#-applying-changes-to-the-database)
- [License](#-license)

---

## 🧩 Overview

This guide walks through wiring up a **Next.js 16** application with **Drizzle ORM** and a **Neon serverless Postgres** database — from installation to running your first migration.

---

## 📦 Installation

Install Drizzle ORM and the Drizzle Kit CLI:

```bash
npm i drizzle-orm
npm i -D drizzle-kit
```

Install the Neon serverless driver:

```bash
npm i @neondatabase/serverless
```

Install `dotenv` for environment variable management:

```bash
npm i dotenv
```

---

## 🗂 Project Structure

```
.
├── app
│   └── api
│       └── books
│           ├── route.ts
│           └── [id]
│               └── route.ts
├── db
│   ├── index.ts
│   └── schema.ts
├── drizzle.config.ts
└── .env
```

---

## ⚙️ Setup

### 1. Create a Neon Project

Log in to the [Neon Console](https://console.neon.tech/app/projects) and navigate to the **Projects** section. Select an existing project or click **New Project** to create one.

**Example connection string:**

```
postgres://username:password@ep-cool-darkness-123456.us-east-2.aws.neon.tech/neondb
```

### 2. Configure Environment Variables

Add the `DATABASE_URL` variable to your `.env` or `.env.local` file — this is used to connect to your Neon database.

```env
DATABASE_URL=NEON_DATABASE_CONNECTION_STRING
```

> ⚠️ **Never commit your `.env` file.** Make sure it's included in `.gitignore`.

### 3. Drizzle Config

Create `drizzle.config.ts` at the root of your project:

```ts
import "dotenv/config";
import { defineConfig } from "drizzle-kit";

export default defineConfig({
  schema: "./db/schema.ts",
  out: "./drizzle",
  dialect: "postgresql",
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
});
```

### 4. Connect Drizzle ORM to Your Database

Create `db/index.ts` and set up your database client:

```ts
import { drizzle } from "drizzle-orm/neon-http";
import { neon } from "@neondatabase/serverless";

if (!process.env.DATABASE_URL) {
  throw new Error("DATABASE_URL is not defined in the environment variables.");
}

const sql = neon(process.env.DATABASE_URL!);
export const db = drizzle({ client: sql });
```

### 5. Database Schema

Define your tables in `db/schema.ts`:

```ts
import {
  pgTable,
  serial,
  text,
  integer,
} from "drizzle-orm/pg-core";

export const books = pgTable("books", {
  id: serial("id").primaryKey(),

  title: text("title")
    .notNull(),

  author: text("author")
    .notNull(),

  price: integer("price")
    .notNull(),
});
```

---

## 🔄 Applying Changes to the Database

Generate SQL migrations from your schema, then apply them to your Neon database:

```bash
npx drizzle-kit generate
```

```bash
npx drizzle-kit migrate
```

| Command | Description |
|---|---|
| `drizzle-kit generate` | Generates SQL migration files based on your schema changes |
| `drizzle-kit migrate` | Applies pending migrations to the connected database |

---

## 📄 License

This project is licensed under the **MIT License**.
