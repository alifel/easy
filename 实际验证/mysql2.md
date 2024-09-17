# mysql2

1. **使用pool后，如果不掉用`pool.end()`，进程会一直挂起**
2. **使用pool后，直接终止进程，`control+c`或者直接关闭terminal，pool与mysql server的连接会自动断开**
3. **使用pool后，从pool中获取的`connection`，当掉用`connection.release()`后，连接会回到连接池中，并不会断开，与使用`pool.releaseConnection(connection)`效果一致**
