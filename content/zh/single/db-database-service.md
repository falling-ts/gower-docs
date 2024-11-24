---
title: db-数据库服务
url: "/single/db-数据库服务"
---

## 使用的开源库

```yaml
"gorm.io/gorm"
```

## 服务提供者

### 服务标识符

```yaml
"db"
```

### 注册服务

```go
P.Register("db", func() (Depends, Resolve) {
    return Depends{"config", "logger"}, func(ss ...services.Service) services.Service {
        return db.New().Init(ss...)
    }
})
```

### 注册服务格式见 auth 服务

## 依赖的服务

```yaml
"config", "logger"
```

## ENV 配置

```yaml
DB_DRIVER=sqlite
DB_USER=root
DB_PASSWD=root
DB_NET=tcp
DB_HOST=tidb
DB_PORT=4000
DB_NAME=gower

# 最大数据库连接
DB_MAX_OPEN=100
# 最大数据库空闲连接
DB_MAX_IDLE_COUNT=25
# 数据库连接最大生存时间
DB_MAX_LIFE_TIME=30m
# 数据库空闲连接最大生存时间
DB_MAX_IDLE_TIME=10m

# 是否禁用自动迁移时, 创建外键, 默认禁用
DB_DISABLE_FOREIGN_KEY=true
# 是否跳过默认开启事务, 默认跳过
DB_SKIP_DEFAULT_TRANSACTION=true
# 时区: sys, utc, beijing
DB_TIMEZONE=beijing
# 是否开启预编译缓存, 默认开启
DB_PREPARE_STMT=true
# 是否启用 mysql_native_password, 默认启用
DB_MYSQL_NATIVE_PASSWORDS=true
```

## 服务接口方法

```go
type DBService interface {
    Service // 通用服务接口

    // 自动迁移数据库结构
    AutoMigrate(dst ...interface{}) error 
    
    // 用于处理关联数据, 它是用于加载、添加、替换和删除关联数据的高级 API.
    Association(column string) *gorm.Association 
    
    // 用于设置默认值, 当您尝试创建一个新的记录时, 如果未提供某些字段的值, Attrs 方法允许您为这些字段指定默认值.
    Attrs(attrs ...interface{}) (tx *gorm.DB) 
    
    // 用于在查询或创建记录时指定某些字段的值, 它常与 FirstOrXXX 系列方法（如 FirstOrCreate）配合使用, 以便在查找或创建记录时预先指定或修改某些字段的值.
    Assign(attrs ...interface{}) (tx *gorm.DB)
    
    // 用于向当前的 gorm.DB 实例中添加错误信息. 这在某些情况下很有用, 例如, 当您需要将自定义错误与 GORM 生成的错误一起处理时.
    AddError(err error) error 

    // 用于开始一个新的事务. 在数据库操作中, 事务用于确保一组操作在成功时被提交, 或在失败时被回滚, 以保持数据的一致性.
    Begin(opts ...*sql.TxOptions) *gorm.DB

    // 用于在数据库中创建新记录. 给定一个包含数据的结构体实例, GORM 会根据结构体的定义自动创建相应的 SQL 插入语句, 并执行插入操作.
    Create(value interface{}) (tx *gorm.DB)
    
    // 用于在数据库中批量创建新记录. 与 Create 方法类似, 但它允许您指定一个批处理大小, 以便将多个记录分组插入. 这对于插入大量数据时提高性能非常有帮助.
    CreateInBatches(value interface{}, batchSize int) (tx *gorm.DB)
    
    // 用于查询满足给定条件的记录数量. 当你需要获取数据库中满足某些条件的记录数时, 可以使用 Count 方法。
    Count(count *int64) (tx *gorm.DB)
    Connection(fc func(tx *gorm.DB) error) (err error)
    
    // 提交事务
    Commit() *gorm.DB
    
    // 用于在查询时添加自定义的 SQL 子句. 当您需要执行包含特殊子句的查询时, 可以使用 Clauses 方法.
    Clauses(conds ...clause.Expression) (tx *gorm.DB)

    // 用于在查询时添加 DISTINCT 子句. DISTINCT 子句用于返回不同（唯一）的结果, 当您需要获取某个字段的不重复值时, 可以使用 Distinct 方法.
    Distinct(args ...interface{}) (tx *gorm.DB)
    
    // 用于从数据库中删除满足给定条件的记录. Delete 方法会生成相应的 SQL 删除语句, 并执行删除操作.
    Delete(value interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // 用于在执行 SQL 语句之前将其输出到控制台. 这对于调试数据库查询和操作时查看实际执行的 SQL 语句非常有用.
    Debug() (tx *gorm.DB)
    
    // 获取 database/sql 的 DB
    SqlDB() (*sql.DB, error)

    // 用于执行原始 SQL 语句. 在某些情况下, 您可能需要执行一些不受 GORM 本身支持的 SQL 语句或操作, 此时可以使用 Exec 方法.
    Exec(sql string, values ...interface{}) (tx *gorm.DB)

    // 用于从数据库中查询满足给定条件的第一条记录. First 方法会生成相应的 SQL 查询语句, 并执行查询操作, 将查询结果填充到提供的结构体实例中.
    First(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // 用于从数据库中查询满足给定条件的所有记录. Find 方法会生成相应的 SQL 查询语句, 并执行查询操作, 将查询结果填充到提供的结构体实例或结构体实例的切片中.
    Find(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // 用于批量查询满足给定条件的所有记录. FindInBatches 方法在处理大量数据时特别有用, 因为它允许您按指定的批处理大小查询数据, 而不是一次性加载所有记录到内存中.
    FindInBatches(dest interface{}, batchSize int, fc func(tx *gorm.DB, batch int) error) *gorm.DB
    
    // 用于从数据库中查询满足给定条件的第一条记录, 如果没有找到记录, 则初始化一个新的结构体实例. 该方法在需要获取或初始化一个记录时非常有用.
    FirstOrInit(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // 用于从数据库中查询满足给定条件的第一条记录, 如果没有找到记录, 则创建一个新的记录. 该方法在需要获取或创建一个记录时非常有用.
    FirstOrCreate(dest interface{}, conds ...interface{}) (tx *gorm.DB)

    // 用于在查询时添加 GROUP BY 子句. GROUP BY 子句用于将结果集按照指定的列进行分组, 通常与聚合函数（如 COUNT、SUM、AVG 等）一起使用.
    Group(name string) (tx *gorm.DB)
    
    // 用于从当前 *gorm.DB 实例的存储中获取与给定键关联的值. 这可以用来在 GORM 中存储和检索与特定数据库操作相关的自定义数据.
    Get(key string) (interface{}, bool)
    
    // 获取 GormDB
    GormDB() *gorm.DB

    // 用于在查询时添加 HAVING 子句. HAVING 子句用于过滤聚合函数的结果, 通常与 GROUP BY 一起使用.
    Having(query interface{}, args ...interface{}) (tx *gorm.DB)

    // 用于在查询时添加 INNER JOIN 子句. INNER JOIN 子句用于将多个表的记录组合在一起, 基于指定的连接条件. 只有满足连接条件的记录会被包含在结果集中.
    InnerJoins(query string, args ...interface{}) (tx *gorm.DB)
    
    // 用于将一个值与给定的键关联并存储在当前 *gorm.DB 实例的存储中. 这可以用来在 GORM 中存储和检索与特定数据库操作相关的自定义数据.
    InstanceSet(key string, value interface{}) *gorm.DB
    
    // 用于从当前 *gorm.DB 实例的存储中获取与给定键关联的值.
    InstanceGet(key string) (interface{}, bool)

    // 用于在查询时添加 JOIN 子句. JOIN 子句用于将多个表的记录组合在一起, 基于指定的连接条件. Joins 方法允许您自定义连接类型, 如 INNER JOIN、LEFT JOIN 等.
    Joins(query string, args ...interface{}) (tx *gorm.DB)

    // 用于从数据库中查询满足给定条件的最后一条记录. Last 方法会生成相应的 SQL 查询语句, 并执行查询操作, 将查询结果填充到提供的结构体实例中.
    Last(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // 用于在查询时添加 LIMIT 子句. LIMIT 子句用于限制查询结果集中的记录数量. 在分页查询或仅需要查询固定数量的记录时, 可以使用 Limit 方法.
    Limit(limit int) (tx *gorm.DB)

    // 用于返回一个 gorm.Migrator 接口实现. 通过 Migrator, 您可以执行数据库迁移操作, 例如创建表、删除表、修改表结构等.
    Migrator() gorm.Migrator
    
    // 用于设置当前查询或操作的主要表. 该方法将当前 *gorm.DB 实例的模型设置为给定的结构体实例或结构体指针. 在执行查询、插入、更新或删除操作时, 这个模型将作为主要表.
    Model(value interface{}) (tx *gorm.DB)

    // 用于在查询时添加 NOT 条件. Not 方法用于在查询中添加一个否定的条件, 即查询满足非给定条件的记录. 它可以用于过滤不满足某些条件的记录.
    Not(query interface{}, args ...interface{}) (tx *gorm.DB)

    // 用于在执行创建（Create）或更新（Update）操作时忽略指定的列. 当您不希望将某些字段插入或更新到数据库中时, 可以使用 Omit 方法.
    Omit(columns ...string) (tx *gorm.DB)
    
    // 用于在查询时添加 OR 条件. Or 方法用于在查询中添加一个或多个附加条件, 即查询满足给定条件之一的记录. 它可以用于组合多个条件, 以便查询具有更宽松的约束.
    Or(query interface{}, args ...interface{}) (tx *gorm.DB)
    
    // 用于在查询时添加 ORDER BY 子句. ORDER BY 子句用于按指定的列对结果集进行排序. 在需要对查询结果进行排序时, 可以使用 Order 方法.
    Order(value interface{}) (tx *gorm.DB)
    
    // 用于在查询时添加 OFFSET 子句. OFFSET 子句用于从结果集中的指定位置开始返回记录. 在分页查询或需要跳过前几条记录时, 可以使用 Offset 方法.
    Offset(offset int) (tx *gorm.DB)

    // 用于从数据库中获取指定列的值列表, 并将其存储到指定的切片中. Pluck 方法可以用于快速获取指定列的值, 而不必返回整个记录. 它通常与 Select 方法一起使用, 以便同时获取多个列的值.
    Pluck(column string, dest interface{}) (tx *gorm.DB)
    
    // 用于在查询相关联的记录时预加载（eager loading）关联的记录. 通过预加载关联的记录, 可以避免在使用关联数据时产生额外的查询操作. 预加载通常使用在查询具有多对一或一对多关联的记录时.
    Preload(query string, args ...interface{}) (tx *gorm.DB)

    // 用于在查询或操作中直接使用原始 SQL 语句. Raw 方法可以让您使用 SQL 语句执行更高级的操作, 例如联接查询、子查询、事务等.
    Raw(sql string, values ...interface{}) (tx *gorm.DB)
    
    // 用于在执行查询时返回单行记录的结果. Row 方法仅返回匹配条件的第一行记录, 如果未找到任何记录, 则返回一个空行.
    Row() *sql.Row
    
    // 用于执行查询并返回多行结果. Rows 方法返回的是 *sql.Rows 类型, 可以通过迭代 Rows 中的记录来获取查询结果集中的每一行.
    Rows() (*sql.Rows, error)
    
    // 用于在事务中回滚之前执行的操作. 在使用事务进行数据库操作时, 如果出现错误或需要取消当前操作, 可以使用 Rollback 方法回滚事务, 撤销之前执行的所有操作.
    Rollback() *gorm.DB
    
    // 用于回滚事务中到指定保存点之间的所有更改. 保存点是事务处理中一个逻辑上的标记, 用于标记当前事务中的某个位置, 从而在之后的操作中可以方便地回滚到该位置.
    RollbackTo(name string) *gorm.DB

    // 用于将给定的结构体实例插入到数据库中或更新数据库中已有的记录. 如果给定结构体实例的主键字段的值为空, 则 Save 方法将执行插入操作. 如果给定结构体实例的主键字段的值非空, 则 Save 方法将执行更新操作.
    Save(value interface{}) (tx *gorm.DB)
    
    // 用于将查询结果的第一行扫描到给定的结构体实例中. Scan 方法用于获取查询结果的第一行, 例如计算查询结果集中的总行数、查询某个特定值等.
    Scan(dest interface{}) (tx *gorm.DB)
    
    // 用于将查询结果中的所有行扫描到给定的结构体实例中. ScanRows 方法可以用于从查询结果中检索多行数据.
    ScanRows(rows *sql.Rows, dest interface{}) error
    
    // 用于在事务中创建一个保存点（Save Point）. 保存点是事务处理中一个逻辑上的标记, 用于标记当前事务中的某个位置, 从而在之后的操作中可以方便地回滚到该位置.
    SavePoint(name string) *gorm.DB
    
    // 用于在查询时指定要选择的列. 通过 Select 方法, 可以从查询结果中只选择所需的列, 以便减少网络流量和提高查询性能.
    Select(query interface{}, args ...interface{}) (tx *gorm.DB)
    
    // 用于在查询时应用指定的一组作用域（Scope）. 作用域是一组指定了一组查询条件或行为的函数, 可以使用 Scopes 方法将它们应用到查询中.
    Scopes(funcs ...func(*gorm.DB) *gorm.DB) (tx *gorm.DB)
    
    // 用于为当前的数据库操作创建一个新的会话（Session）. 在 GORM 中, Session 是一个抽象的概念, 代表着一系列的数据库操作, 它包括开始事务、执行查询、插入数据等操作. 通过 Session, 您可以灵活地控制每个操作的一些行为, 例如事务隔离级别、查询缓存、错误处理等.
    Session(config *gorm.Session) *gorm.DB
    
    // 用于设置 GORM 的全局选项. GORM 提供了许多全局选项, 可以控制数据库连接池的大小、SQL 日志记录、查询缓存等.
    Set(key string, value interface{}) *gorm.DB
    
    // 用于在多对多关联中设置关联表的表名以及外键的名称.
    SetupJoinTable(model interface{}, field string, joinTable interface{}) error

    // 用于指定要在哪个数据库表上执行操作. 在 GORM 中, 每个模型都与一个数据库表相关联. 通过 Table 方法, 可以更改当前操作的默认表, 以便在其他表上执行数据库操作.
    Table(name string, args ...interface{}) (tx *gorm.DB)
    
    // 用于从数据库中获取一条记录, 并将其扫描到指定的结构体实例中. Take 方法类似于 First 方法, 但它不会对查询结果进行排序, 而是直接返回查询结果集中的第一条记录.
    Take(dest interface{}, conds ...interface{}) (tx *gorm.DB)
    
    // 用于开启一个新的数据库事务, 并在该事务中执行指定的函数. 如果函数执行成功, 则事务将被提交, 否则将回滚.
    Transaction(fc func(tx *gorm.DB) error, opts ...*sql.TxOptions) (err error)
    
    // 用于将查询转换为 SQL 语句和参数. 该方法返回查询的 SQL 语句和参数列表, 以便您可以直接使用它们执行原生的 SQL 查询.
    ToSQL(queryFn func(tx *gorm.DB) *gorm.DB) string

    // 用于注册指定的插件. 插件是 GORM 中用于增强或扩展功能的可插拔组件.
    Use(plugin gorm.Plugin) error
    
    // 用于取消当前查询的软删除过滤器. 在 GORM 中, 软删除是一种标记删除的方式, 它将数据的删除标记设置为 true, 而不是从数据库中永久删除数据. 当执行查询时, GORM 会默认使用软删除过滤器来排除被标记为已删除的数据. 通过 Unscoped 方法, 可以取消软删除过滤器, 并返回所有记录, 包括被标记为已删除的记录.
    Unscoped() (tx *gorm.DB)
    
    // 用于更新数据库表中的记录. 通过指定要更新的列名和新的值, 可以更新符合指定条件的一组记录.
    Update(column string, value interface{}) (tx *gorm.DB)
    
    // 用于将一个结构体实例中的字段值映射到数据库表中的列, 并更新符合指定条件的一组记录.
    Updates(values interface{}) (tx *gorm.DB)
    
    // 用于更新数据库表中的单个列. 与 Updates 方法不同, UpdateColumn 方法只会更新指定的列, 而不会将结构体中的其他字段值映射到数据库表中.
    UpdateColumn(column string, value interface{}) (tx *gorm.DB)
    
    // 用于将一个结构体实例中的字段值映射到数据库表中的列, 并更新符合指定条件的一组记录.
    UpdateColumns(values interface{}) (tx *gorm.DB)

    // 用于将指定的上下文对象与数据库操作相关联. 在某些情况下, 您可能需要在数据库操作中使用上下文对象, 例如在处理 Web 请求时.
    WithContext(ctx context.Context) *gorm.DB
    
    // 用于指定一个查询条件. 使用 Where 方法, 您可以指定一个 SQL 表达式或结构体, 它将被转换为 SQL 表达式, 以及与该表达式一起使用的参数.
    Where(query interface{}, args ...interface{}) (tx *gorm.DB)
}
```
