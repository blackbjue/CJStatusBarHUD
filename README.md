# CJStatusBarHUD
请问请问去问请问去期望 

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
