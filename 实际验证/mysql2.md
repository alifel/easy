# mysql2

1. **使用pool后，如果掉用了`pool.getConnection()`，之后如果不掉用`pool.end()`，进程一直不会退出**
2. **使用pool后，直接终止进程，`control+c`或者直接关闭terminal，pool与mysql server的连接会自动断开**
3. **使用pool后，从pool中获取的`connection`，当掉用`connection.release()`后，连接会回到连接池中，并不会断开，与使用`pool.releaseConnection(connection)`效果一致**
4. **直接使用`mysql.createConnection()`创建的connection，直接终止进程，`control+c`或者直接关闭terminal，连接会自动断开**
5. **`connection.destroy()`会立即断开mysql server的连接，无论该连接是直接创建还是从pool中获取到的**
