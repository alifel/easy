# ioredis

**执行下面的代码，会创建1500个连接，然后`control+c`退出，连接自动断开。**

```typescript
import Redis from "ioredis";
import * as dotenv from "dotenv";
import path from "path";
dotenv.config({
  path: path.resolve(
    __dirname,
    process.env.NODE_ENV === "production" ? ".env.production" : ".env"
  ),
});
console.log("redis client instance created......");

const test = async (i: number) => {
  const redis = new Redis({
    port: parseInt(process.env.REDIS_PORT as string),
    host: process.env.REDIS_HOST as string,
    password: process.env.REDIS_PASSWORD as string,
    db: 0,
  });
  return await redis.incrby("test" + i, 1);
};

const main = async () => {
  for (let i = 0; i < 1500; i++) {
    test(i);
  }
};

main();
```

---

**`connectTimeout`是设置初始化连接的最大时间的，超过了，将会杀死socket。默认为`10000`**

---

**==`socketTimeout`搞不清楚干嘛的==**

下面代码大多数可以成功，偶尔不成功，也搞不清楚，如果是说连接空闲时间超过这个时间，连接就断开，那应该该代码一定失败，但是大多数会成功，我就不明白了。但是好在该值默认没有值，常规理解，就是在不设置的情况下，没有限制。

```typescript
const test = async (i: number) => {
  const redis = new Redis({
    port: parseInt(process.env.REDIS_PORT as string),
    host: process.env.REDIS_HOST as string,
    password: process.env.REDIS_PASSWORD as string,
    db: 0,
    socketTimeout: 10000,
  });
  //   await redis.incrby("test" + i, 1);
  setTimeout(async () => {
    console.log("inert");
    await redis.incrby("test" + i, 1);
  }, 20000);
};

const main = async () => {
  for (let i = 0; i < 1; i++) {
    test(i);
  }
};
```

---

**`keepAlive` 默认值为0，表示不开启keepAlive（==我自己的理解，因为该值为数字，文档描述又是开始和关闭keepAlive，所以我认为是0表示不开启，非0表示开启==）**
