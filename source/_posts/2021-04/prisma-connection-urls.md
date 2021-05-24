---
title: Prisma connection URLs
date: 2021-04-23 13:35:05
tags: [ prisma ]
---

### PostgreSQL

```prisma
1datasource db {
2  provider = "postgresql"
3  url      = "postgresql://janedoe:mypassword@localhost:5432/mydb"
4}
```

### MySQL

```prisma
1datasource db {
2  provider = "mysql"
3  url      = "mysql://janedoe:mypassword@localhost:3306/mydb"
4}
```

### Microsoft SQL Server (Preview)

```prisma
1datasource db {
2  provider = "sqlserver"
3  url      = "sqlserver://localhost:1433;initial catalog=sample;user=sa;password=mypassword;"
4}
```

> **Note**: You must [enable the `microsoftSqlServer` Preview feature](https://www.prisma.io/docs/concepts/components/preview-features/sql-server/#enable-sql-server-preview-feature) in order to use the Microsoft SQL Server connector.

### SQLite

```prisma
1datasource db {
2  provider = "sqlite"
3  url      = "file:./dev.db"
4}
```

Note that you can also provide the connection URL as an environment variable like so:

```prisma
1datasource db {
2  provider = "postgresql"
3  url      = env("DATABASE_URL")
4}
```

You can then either set the environment variable in your terminal or by providing a [dotenv](https://github.com/motdotla/dotenv) file called `.env`. This will automatically be picked up by the Prisma CLI.

**.env**

```undefined
DATABASE_URL=postgresql://janedoe:mypassword@localhost:5432/mydb
```

