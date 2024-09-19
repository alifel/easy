# jest总结

## 异步

---

describe()中的所有异步test()，只要返回promise，或者给方法标记asnyc，都会等待，直到promise完成，或者方法执行完成，再进行下一个test()。(==官方文档：<https://jestjs.io/docs/asynchronous>==)

---

afterAll(), beforeAll()，如果返回promise，也会等待promise，resolve后，再执行后续代码。(==官方文档：<https://jestjs.io/docs/api#beforeeachfn-timeout>==)

---

2个 describe()，如果一个 describe() 中所有异步 test() 都执行完毕，才会执行下一个 describe() 中的 test()。(==自己测试的，代码见下==)

```typescript
import { describe, test, expect } from "@jest/globals";
import { getCipherIndex } from "../../src/redis/cipherIndex";
import { v4 as uuid } from "uuid";
import { redis } from "../../src/redis/connection";

describe("getCipherIndex", () => {
  test("getCipherIndex", async () => {
    const liveId = uuid();
    try {
      const r = await getCipherIndex(liveId);
      expect(r).toBe(1);
      await new Promise((resolve) => setTimeout(resolve, 4000));
      console.log("first test", Date.now());
    } finally {
      await redis.del("cipher_index_" + liveId);
    }
  });
});

describe("last test", () => {
  test("last test", async () => {
    console.log("last test", Date.now());
  });
});

afterAll(async () => {
  await redis.quit();
});
```

结果：

```shell
yudigege@192 live-game-server % node 'node_modules/.bin/jest' '/Users/yudigege/
Documents/liubing/nles/live-game-server/test/redis/cipherIndex.test.ts'
  console.log
    redis client instance created......

      at Object.<anonymous> (src/redis/connection.ts:10:9)

  console.log
    first test 1726706843916

      at Object.<anonymous> (test/redis/cipherIndex.test.ts:13:15)

  console.log
    last test 1726706843953

      at Object.<anonymous> (test/redis/cipherIndex.test.ts:22:13)

 PASS  test/redis/cipherIndex.test.ts (5.584 s)
  getCipherIndex
    ✓ getCipherIndex (4168 ms)
  last test
    ✓ last test (2 ms)
```

可以看到first测试虽然延迟了4s，但是last测试仍然输出的时间点在其后面，时间戳也比first大。

---
