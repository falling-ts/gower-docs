---
title: db-Database Service
url: "/en/single/db-database-service"
---

## Open Source Libraries Used

```yaml
"gorm.io/gorm"
```


## Service Provider

### Service Identifier

```yaml
"db"
```


### Register Service

```go
P.Register("db", func() (Depends, Resolve) {
    return Depends{"config", "logger"}, func(ss ...services.Service) services.Service {
        return db.New().Init(ss...)
    }
})
```


### Registration Format Refer to auth Service

## Dependent Services

```yaml
"config", "logger"
```


## ENV Configuration

```yaml
DB_DRIVER=sqlite
DB_USER=root
DB_PASSWD=root
DB_NET=tcp
DB_HOST=tidb
DB_PORT=4000
DB_NAME=gower

# Maximum database connections
DB_MAX_OPEN=100
# Maximum idle database connections
DB_MAX_IDLE_COUNT=25
# Maximum lifetime of a database connection
DB_MAX_LIFE_TIME=30m
# Maximum lifetime of an idle database connection
DB_MAX_IDLE_TIME=10m

# Whether to disable foreign key creation during auto migration, default is disabled
DB_DISABLE_FOREIGN_KEY=true
# Whether to skip default transaction, default is to skip
DB_SKIP_DEFAULT_TRANSACTION=true
# Timezone: sys, utc, beijing
DB_TIMEZONE=beijing
# Whether to enable prepared statement caching, default is enabled
DB_PREPARE_STMT=true
# Whether to enable mysql_native_password, default is enabled
DB_MYSQL_NATIVE_PASSWORDS=true
```


## Service Interface Methods

```go
type DBService interface {
    Service // General service interface

    // Automatically migrate the database schema
    AutoMigrate(dst ...interface{}) error 
    
    // Used to handle associated data, it is an advanced API for loading, adding, replacing, and deleting associated data.
    Association(column string) *gorm.Association 
    
    // Used to set default values, when you try to create a new record, if some fields are not provided, the Attrs method allows you to specify default values for these fields.
    Attrs(attrs ...interface{}) (tx *gorm.DB) 
    
    // Used to specify certain field values when querying or creating records, it is often used with FirstOrXXX series methods (such as FirstOrCreate) to pre-specify or modify certain field values when finding or creating records.
    Assign(attrs ...interface{}) (tx *gorm.DB)
    
    // Used to add error information to the current gorm.DB instance. This is useful in some cases, for example, when you need to handle custom errors along with GORM-generated errors.
    AddError(err error) error 

    // Used to start a new transaction. In database operations, transactions ensure that a set of operations are committed on success or rolled back on failure to maintain data consistency.
    Begin(opts ...*sql.TxOptions) *gorm.DB

    // Used to create new records in the database. Given a struct instance containing data, GORM will automatically generate the corresponding SQL insert statement and execute the insertion operation.
    Create(value interface{}) (tx *gorm.DB)
    
    // Used to create new records in the database in batches. Similar to the Create method, but it allows you to specify a batch size to group multiple records for insertion. This is very helpful for inserting large amounts of data.
    CreateInBatches(value interface{}, batchSize int) (tx *gorm.DB)
    
    // Used to query the number of records that meet the given conditions. When you need to get the number of records in the database that meet certain conditions, you can use the Count method.
    Count(count *int64) (tx *gorm.DB)
    Connection(fc func(tx *gorm.DB) error) (err error)
    
    // Commit the transaction
    Commit() *gorm.DB
    
    // Used to add custom SQL clauses when querying. When you need to execute queries with special clauses, you can use the Clauses method.
    Clauses(conds ...clause.Expression) (tx *gorm.DB)

    // Used to add a DISTINCT clause when querying. The DISTINCT clause returns unique results, which is useful when you need to get non-repeating values for a specific field.
    Distinct(args ...interface{}) (tx *gorm.DB)
    
    // Used to delete records from the database that meet the given conditions. The Delete method generates the corresponding SQL delete statement and executes the deletion operation.
    Delete(value interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // Used to output the SQL statement to the console before execution. This is very useful for debugging database queries and operations to see the actual SQL statements being executed.
    Debug() (tx *gorm.DB)
    
    // Get the database/sql's DB
    SqlDB() (*sql.DB, error)

    // Used to execute raw SQL statements. In some cases, you may need to execute SQL statements or operations not supported by GORM itself, at which point you can use the Exec method.
    Exec(sql string, values ...interface{}) (tx *gorm.DB)

    // Used to query the first record that meets the given conditions from the database. The First method generates the corresponding SQL query statement, executes the query operation, and fills the query result into the provided struct instance.
    First(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // Used to query all records that meet the given conditions from the database. The Find method generates the corresponding SQL query statement, executes the query operation, and fills the query results into the provided struct instance or slice of struct instances.
    Find(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // Used to query all records that meet the given conditions from the database in batches. The FindInBatches method is particularly useful when handling large amounts of data because it allows you to query data in specified batch sizes instead of loading all records into memory at once.
    FindInBatches(dest interface{}, batchSize int, fc func(tx *gorm.DB, batch int) error) *gorm.DB
    
    // Used to query the first record that meets the given conditions from the database, and if no record is found, initializes a new struct instance. This method is very useful when you need to get or initialize a record.
    FirstOrInit(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // Used to query the first record that meets the given conditions from the database, and if no record is found, creates a new record. This method is very useful when you need to get or create a record.
    FirstOrCreate(dest interface{}, conds ...interface{}) (tx *gorm.DB)

    // Used to add a GROUP BY clause when querying. The GROUP BY clause groups the result set by specified columns, usually used with aggregate functions (such as COUNT, SUM, AVG).
    Group(name string) (tx *gorm.DB)
    
    // Used to get the value associated with the given key from the storage of the current *gorm.DB instance. This can be used to store and retrieve custom data related to specific database operations in GORM.
    Get(key string) (interface{}, bool)
    
    // Get GormDB
    GormDB() *gorm.DB

    // Used to add a HAVING clause when querying. The HAVING clause filters the results of aggregate functions, usually used with GROUP BY.
    Having(query interface{}, args ...interface{}) (tx *gorm.DB)

    // Used to add an INNER JOIN clause when querying. The INNER JOIN clause combines records from multiple tables based on specified join conditions. Only records that satisfy the join conditions are included in the result set.
    InnerJoins(query string, args ...interface{}) (tx *gorm.DB)
    
    // Used to associate a value with the given key and store it in the storage of the current *gorm.DB instance. This can be used to store and retrieve custom data related to specific database operations in GORM.
    InstanceSet(key string, value interface{}) *gorm.DB
    
    // Used to get the value associated with the given key from the storage of the current *gorm.DB instance.
    InstanceGet(key string) (interface{}, bool)

    // Used to add a JOIN clause when querying. The JOIN clause combines records from multiple tables based on specified join conditions. The Joins method allows you to customize the join type, such as INNER JOIN, LEFT JOIN, etc.
    Joins(query string, args ...interface{}) (tx *gorm.DB)

    // Used to query the last record that meets the given conditions from the database. The Last method generates the corresponding SQL query statement, executes the query operation, and fills the query result into the provided struct instance.
    Last(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // Used to add a LIMIT clause when querying. The LIMIT clause limits the number of records in the query result set. It is useful for pagination queries or when you need to query a fixed number of records.
    Limit(limit int) (tx *gorm.DB)

    // Used to return a gorm.Migrator implementation. Through Migrator, you can perform database migration operations, such as creating tables, dropping tables, modifying table structures, etc.
    Migrator() gorm.Migrator
    
    // Used to set the primary table for the current query or operation. This method sets the model of the current *gorm.DB instance to the given struct instance or struct pointer. In executing queries, inserts, updates, or deletes, this model will be the primary table.
    Model(value interface{}) (tx *gorm.DB)

    // Used to add a NOT condition when querying. The Not method adds a negated condition to the query, i.e., it queries records that do not meet the given conditions. It can be used to filter out records that do not meet certain conditions.
    Not(query interface{}, args ...interface{}) (tx *gorm.DB)

    // Used to ignore specified columns when performing create (Create) or update (Update) operations. When you do not want to insert or update certain fields to the database, you can use the Omit method.
    Omit(columns ...string) (tx *gorm.DB)
    
    // Used to add OR conditions when querying. The Or method adds one or more additional conditions to the query, i.e., it queries records that meet any of the given conditions. It can be used to combine multiple conditions to make the query more flexible.
    Or(query interface{}, args ...interface{}) (tx *gorm.DB)
    
    // Used to add an ORDER BY clause when querying. The ORDER BY clause sorts the result set by specified columns. It is useful when you need to sort the query results.
    Order(value interface{}) (tx *gorm.DB)
    
    // Used to add an OFFSET clause when querying. The OFFSET clause starts returning records from a specified position in the result set. It is useful for pagination queries or when you need to skip the first few records.
    Offset(offset int) (tx *gorm.DB)

    // Used to query the list of values for a specified column from the database and store them in the specified slice. The Pluck method can be used to quickly get the values of a specified column without retrieving the entire record. It is often used with the Select method to get values from multiple columns simultaneously.
    Pluck(column string, dest interface{}) (tx *gorm.DB)
    
    // Used to preload (eager load) associated records when querying. By preloading associated records, you can avoid additional queries when using associated data. Preloading is typically used when querying records with one-to-many or many-to-one associations.
    Preload(query string, args ...interface{}) (tx *gorm.DB)

    // Used to directly use raw SQL statements in queries or operations. The Raw method allows you to execute more advanced operations using SQL statements, such as join queries, subqueries, transactions, etc.
    Raw(sql string, values ...interface{}) (tx *gorm.DB)
    
    // Used to return the first row of the query result. The Row method returns only the first row that matches the conditions, and returns an empty row if no records are found.
    Row() *sql.Row
    
    // Used to execute a query and return multiple rows. The Rows method returns a *sql.Rows type, and you can iterate over the Rows to get each row of the query result set.
    Rows() (*sql.Rows, error)
    
    // Used to roll back previously executed operations in a transaction. When using transactions for database operations, if an error occurs or you need to cancel the current operation, you can use the Rollback method to roll back the transaction and undo all previous operations.
    Rollback() *gorm.DB
    
    // Used to roll back all changes to a specified savepoint in a transaction. A savepoint is a logical marker in a transaction, used to mark a specific position in the transaction, so you can easily roll back to that position later.
    RollbackTo(name string) *gorm.DB

    // Used to insert a struct instance into the database or update an existing record in the database. If the primary key field of the given struct instance is empty, the Save method will perform an insert operation. If the primary key field of the given struct instance is non-empty, the Save method will perform an update operation.
    Save(value interface{}) (tx *gorm.DB)
    
    // Used to scan the first row of the query result into the given struct instance. The Scan method is used to get the first row of the query result, for example, to calculate the total number of rows in the query result set or to query a specific value.
    Scan(dest interface{}) (tx *gorm.DB)
    
    // Used to scan all rows of the query result into the given struct instance. The ScanRows method can be used to retrieve multiple rows of data from the query result.
    ScanRows(rows *sql.Rows, dest interface{}) error
    
    // Used to create a savepoint (Save Point) in a transaction. A savepoint is a logical marker in a transaction, used to mark a specific position in the transaction, so you can easily roll back to that position later.
    SavePoint(name string) *gorm.DB
    
    // Used to specify the columns to select when querying. By using the Select method, you can select only the required columns from the query result to reduce network traffic and improve query performance.
    Select(query interface{}, args ...interface{}) (tx *gorm.DB)
    
    // Used to apply a set of scopes (Scope) to the query. Scopes are a set of functions that specify a set of query conditions or behaviors, and can be applied to the query using the Scopes method.
    Scopes(funcs ...func(*gorm.DB) *gorm.DB) (tx *gorm.DB)
    
    // Used to create a new session (Session) for the current database operation. In GORM, a Session is an abstract concept representing a series of database operations, including starting transactions, executing queries, inserting data, etc. Through Session, you can flexibly control the behavior of each operation, such as transaction isolation levels, query caching, error handling, etc.
    Session(config *gorm.Session) *gorm.DB
    
    // Used to set global options for GORM. GORM provides many global options to control the size of the database connection pool, SQL logging, query caching, etc.
    Set(key string, value interface{}) *gorm.DB
    
    // Used to set the table name and foreign key names for a many-to-many association.
    SetupJoinTable(model interface{}, field string, joinTable interface{}) error

    // Used to specify the table on which to perform the operation. In GORM, each model is associated with a database table. By using the Table method, you can change the default table for the current operation to perform database operations on other tables.
    Table(name string, args ...interface{}) (tx *gorm.DB)
    
    // Used to get a record from the database and scan it into the specified struct instance. The Take method is similar to the First method, but it does not sort the query results and directly returns the first record in the query result set.
    Take(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // Used to start a new database transaction and execute the specified function within that transaction. If the function executes successfully, the transaction will be committed; otherwise, it will be rolled back.
    Transaction(fc func(tx *gorm.DB) error, opts ...*sql.TxOptions) (err error)
    
    // Used to convert the query to an SQL statement and parameters. This method returns the SQL statement and parameter list of the query, so you can directly use them to execute native SQL queries.
    ToSQL(queryFn func(tx *gorm.DB) *gorm.DB) string

    // Used to register the specified plugin. Plugins are pluggable components in GORM used to enhance or extend functionality.
    Use(plugin gorm.Plugin) error
    
    // Used to cancel the soft delete filter in the current query. In GORM, soft delete is a way to mark deleted data by setting the delete flag to true, rather than permanently deleting the data from the database. When executing queries, GORM defaults to using the soft delete filter to exclude data marked as deleted. By using the Unscoped method, you can cancel the soft delete filter and return all records, including those marked as deleted.
    Unscoped() (tx *gorm.DB)
    
    // Used to update records in the database table. By specifying the column name and new value, you can update a set of records that meet the specified conditions.
    Update(column string, value interface{}) (tx *gorm.DB)
    
    // Used to map the field values of a struct instance to columns in the database table and update a set of records that meet the specified conditions.
    Updates(values interface{}) (tx *gorm.DB)
    
    // Used to update a single column in the database table. Unlike the Updates method, the UpdateColumn method only updates the specified column and does not map other field values of the struct to the database table.
    UpdateColumn(column string, value interface{}) (tx *gorm.DB)
    
    // Used to map the field values of a struct instance to columns in the database table and update a set of records that meet the specified conditions.
    UpdateColumns(values interface{}) (tx *gorm.DB)

    // Used to associate a specified context object with the database operation. In some cases, you may need to use a context object in database operations, such as when handling web requests.
    WithContext(ctx context.Context) *gorm.DB
```
