
```objc
1.获得数据库文件的路径
    NSString *doc = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject];
    NSString *filename = [doc stringByAppendingPathComponent:@"students.sqlite"];

2.根据一个路径去加载数据库 ，一个FMDatabase就代表一个数据库
FMDatabase *db = [FMDatabasedatabaseWithPath:filename];

在FMDB中，除 查询 以为的所有操作，都称为 更新

create，drop，insert，update，delete等

使用 executeUpdate：方法执行更新

3.打开数据库
     if ([db open]) {
        // 4.创表
        BOOL result = [db executeUpdate:@"CREATE TABLE IF NOT EXISTS t_student (id integer PRIMARY KEY AUTOINCREMENT, name text NOT NULL, age integer NOT NULL);"];
        if (result) {
            NSLog(@"成功创表");
        } else {
            NSLog(@"创表失败");
        }
    }

删除数据

[self.db executeUpdate:@"DELETE FROM t_student"];


主键永远是自增长，就算把表里面的内容删了，就会从上次的表里面最后一个主键开始自增长
除非把整张表给删掉
[self.db executeUpdate:@"DROP TABLE IF EXISTS t_student;"];
删掉以后再建表
    [self.db executeUpdate:@"CREATE TABLE IF NOT EXISTS t_student (id integer PRIMARY KEY AUTOINCREMENT, name text NOT NULL, age integer NOT NULL);"];

查询数据：
// 1.执行查询语句
    FMResultSet *resultSet = [self.db executeQuery:@"SELECT * FROM t_student"];

    // 2.遍历结果
    while ([resultSet next]) {
        int ID = [resultSet intForColumn:@"id"];
        NSString *name = [resultSet stringForColumn:@"name"];
        int age = [resultSet intForColumn:@"age"];
        NSLog(@"%d %@ %d", ID, name, age);
    }
插入数据：

- (void)insert
{
    for (int i = 0; i<10; i++) {
        NSString *name = [NSString stringWithFormat:@"jack-%d", arc4random_uniform(100)];
        // executeUpdate : 不确定的参数用?来占位
        [self.db executeUpdate:@"INSERT INTO t_student (name, age) VALUES (?, ?);", name, @(arc4random_uniform(40))];
        // executeUpdateWithFormat : 不确定的参数用%@、%d等来占位
//        [self.db executeUpdateWithFormat:@"INSERT INTO t_student (name, age) VALUES (%@, %d);", name, arc4random_uniform(40)];

}
、

  对比FMDatabase多个block，只不过这个block是安全的，FMDatabaseQueue里面有一个FMDatabase，要拿queue来操作

    // 1.获得数据库文件的路径
    NSString *doc = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject];
    NSString *filename = [doc stringByAppendingPathComponent:@"persons.sqlite"];

    // 2.得到数据库
    FMDatabaseQueue *queue = [FMDatabaseQueue databaseQueueWithPath:filename];

    // 3.打开数据库
    [queue inDatabase:^(FMDatabase *db) {
        // 4.创表
        BOOL result = [db executeUpdate:@"CREATE TABLE IF NOT EXISTS t_person (id integer PRIMARY KEY AUTOINCREMENT, name text NOT NULL, age integer NOT NULL);"];
        if (result) {
            NSLog(@"成功创表");
        } else {
            NSLog(@"创表失败");
        }
    }];

查询数据

     [self.queue inDatabase:^(FMDatabase *db) {

        FMResultSet *resultSet = [db executeQuery:@"SELECT * FROM t_person;"];
        while ([resultSet next]) {
            int ID = [resultSet intForColumn:@"id"];
            NSString *name = [resultSet stringForColumn:@"name"];
            int age = [resultSet intForColumn:@"age"];
            NSLog(@"%d %@ %d", ID, name, age);
        }
    }];

两条语句必须一起成功，开启事务
    [self.queue inDatabase:^(FMDatabase *db) {
        [db beginTransaction];

        [db executeUpdate:@"INSERT INTO t_person (name, age) VALUES (?, ?);", @"jake", @30];
        [db executeUpdate:@"INSERT INTO t_person (name, age) VALUES (?, ?);", @"jake", @30];


        [db commit];
    }];
 或者主动回滚 rollback ＝ Yes 酒可以了
    [self.queue inTransaction:^(FMDatabase *db, BOOL *rollback) {
        [db executeUpdate:@"INSERT INTO t_person (name, age) VALUES (?, ?);", @"jake", @30];
        [db executeUpdate:@"INSERT INTO t_person (name, age) VALUES (?, ?);", @"jake", @30];

    }];

```
