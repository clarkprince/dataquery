# DataQuery

DataQuery is a Go library that enables SQL querying capabilities for various data sources through SQLite virtual tables. It allows you to seamlessly query APIs, files, and other data sources using familiar SQL syntax, making data exploration and integration simpler and more intuitive.

## Overview

DataQuery leverages SQLite's virtual table mechanism to create a bridge between SQL and various data sources. This allows you to:

- Query REST APIs using SQL
- Explore files (CSV, JSON, etc.) with SQL statements
- Combine data from multiple sources in a single query
- Use standard SQL operations (WHERE, JOIN, GROUP BY, etc.) on any data source

## How It Works

DataQuery implements SQLite virtual tables for each data source, providing a translation layer between SQL operations and the underlying data source's native interface. When you execute a query:

1. DataQuery parses the SQL statement
2. Translates SQL operations into appropriate calls to the data source
3. Handles data fetching and pagination automatically
4. Returns results in a consistent tabular format

## Examples

### Querying a REST API

```sql
-- Query GitHub API for repositories
SELECT name, stars, language 
FROM github_repos 
WHERE owner = 'golang' 
  AND stars > 1000 
ORDER BY stars DESC;
```

### Reading CSV Files

```sql
-- Query a CSV file with sales data
SELECT 
    region,
    SUM(revenue) as total_revenue 
FROM sales_data 
WHERE year = 2024 
GROUP BY region;
```

### Combining Multiple Sources

```sql
-- Join GitHub repository data with local git statistics
SELECT 
    r.name,
    r.stars,
    s.commit_count 
FROM github_repos r
JOIN git_stats s ON r.name = s.repo_name 
WHERE r.language = 'Go';
```

## Currently Supported Data Sources

- REST APIs (with JSON responses)
- Local files:
  - CSV
  - JSON
  - Line-delimited JSON (JSONL)
- Git repositories
- More coming soon...

## Installation

```bash
go get github.com/yourusername/dataquery
```

## Usage

```go
package main

import (
    "database/sql"
    "github.com/yourusername/dataquery"
)

func main() {
    // Initialize DataQuery
    dq := dataquery.New()

    // Register data sources
    dq.RegisterSource("github_repos", &GitHubSource{
        BaseURL: "https://api.github.com",
    })

    // Execute query
    rows, err := dq.Query(`
        SELECT name, stars 
        FROM github_repos 
        WHERE language = 'Go' 
        LIMIT 10
    `)
    if err != nil {
        panic(err)
    }
    defer rows.Close()

    // Process results...
}
```

## Architecture

DataQuery is built with the following components:

1. **Core Engine**: Manages SQLite virtual table implementations and query execution
2. **Source Adapters**: Translate between SQL operations and data source APIs
3. **Query Optimizer**: Pushes down operations to data sources when possible
4. **Result Processor**: Handles data transformation and streaming

## Roadmap

- [ ] Add support for more data sources
- [ ] Add support for S3/Cloud Storage
- [ ] Implement query caching
- [ ] Add authentication mechanisms
- [ ] Support for custom data source implementations
- [ ] Query optimization improvements

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
