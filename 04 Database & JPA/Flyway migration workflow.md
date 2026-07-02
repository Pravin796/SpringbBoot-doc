
how does the flyway migration work

Flyway is a **database version control tool**. Think of it like **Git for your database schema**.  
Instead of manually creating tables/columns, Flyway runs **SQL migration files in order** and keeps track of what has already been executed.

Let’s understand it step-by-step (Spring Boot perspective 👇).

---

# 1️⃣ Why Flyway exists

Problem without Flyway:

- Dev A creates table manually
    
- Dev B forgets column
    
- QA has different schema
    
- Production breaks 💥
    

Flyway solves this by:

- Keeping **all schema changes in SQL files**
    
- Running them automatically when app starts
    
- Tracking what ran and what didn’t
    

---

# 2️⃣ How Flyway works internally (Core idea)

When your Spring Boot app starts:

1. Flyway connects to DB
    
2. Checks for a special table called:
    

flyway_schema_history

3. Scans migration files from:
    

src/main/resources/db/migration

4. Runs **new migrations only**
    
5. Records them in history table
    

So Flyway always knows the DB version.

---

# 3️⃣ Migration file naming (VERY IMPORTANT)

Flyway reads files in **version order**.

Format:

V<version>__<description>.sql

Examples:

V1__create_user_table.sql  
V2__add_email_column.sql  
V3__create_order_table.sql

Rules:

- Must start with **V**
    
- Two underscores `__`
    
- Version must increase
    

Flyway sorts by version → executes sequentially.

---

# 4️⃣ Example — Real Flow 🚀

### Step 1 — First migration

Create file:

V1__create_user_table.sql

CREATE TABLE users (  
    id INT PRIMARY KEY AUTO_INCREMENT,  
    name VARCHAR(100)  
);

Start Spring Boot app.

Flyway will:

- Run this SQL
    
- Create history table
    
- Insert record:
    

|version|description|success|
|---|---|---|
|1|create user table|true|

---

### Step 2 — Add new column later

Create new file:

V2__add_email_column.sql

ALTER TABLE users ADD email VARCHAR(150);

Restart app → Flyway checks history table:

- V1 already executed ✔
    
- V2 not executed ❗ → run it
    

History becomes:

|version|description|
|---|---|
|1|create user table|
|2|add email column|

This is the magic 🔥

---

# 5️⃣ The Flyway History Table Explained

Flyway creates automatically:

flyway_schema_history

Important columns:

- version → migration number
    
- description → file description
    
- script → file name
    
- checksum → file hash
    
- success → did migration pass?
    

This prevents running same migration twice.

---

# 6️⃣ VERY IMPORTANT: Why you must NEVER edit old migrations

Flyway stores **checksum (hash)** of each file.

If you change an old migration:

Example:  
You edit V1 after it ran.

Next startup → Flyway compares checksum:

Expected: abc123  
Found: xyz999

Boom 💥 error:

Migration checksum mismatch

Because changing history breaks DB consistency.

👉 Rule:

- Never edit old migration
    
- Always create new migration
    

---

# 7️⃣ What happens in fresh vs existing DB

### 🆕 Fresh DB

Flyway runs:  
V1 → V2 → V3 → V4

### 🧓 Existing DB (till V2)

Flyway runs:  
V3 → V4 only

Smart incremental migration ✨

---

# 8️⃣ How Spring Boot auto runs Flyway

Just add dependency:

<dependency>  
  <groupId>org.flywaydb</groupId>  
  <artifactId>flyway-core</artifactId>  
</dependency>

Spring Boot auto-config does:

App Start → Flyway migrate() → Hibernate starts

Flyway runs **before Hibernate**.

This is important because:

- Tables exist before JPA loads entities.
    

---

# 9️⃣ Flyway vs Hibernate ddl-auto

Never use both ❌

Bad:

spring.jpa.hibernate.ddl-auto=update

Good:

spring.jpa.hibernate.ddl-auto=validate

Why?

- Hibernate auto update = uncontrolled changes
    
- Flyway = controlled versioned changes
    

Best practice:

Flyway → creates schema  
Hibernate → validates schema

---

# 🔟 Summary (Interview style)

Flyway migration process:

1. Scan migration folder
    
2. Check flyway_schema_history table
    
3. Compare executed versions
    
4. Run pending migrations in order
    
5. Store execution metadata
    

Flyway ensures:

- Version control for DB
    
- Safe team collaboration
    
- Reproducible environments
    

---

If you want, next I can teach:  
👉 Flyway **baseline, repair, clean, migrate lifecycle** (very important for interviews & real projects).