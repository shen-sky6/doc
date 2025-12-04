## Redisson加锁方式

Redisson 提供的分布式锁方式挺多的，根据不同业务场景选择不同的锁就行。我给你总结一下常用的几种：
### 🔑 1. **普通可重入锁（RLock）**

```java
RLock lock = redissonClient.getLock("lock:key");
try {
    lock.lock(); // 阻塞直到加锁成功
    // 执行业务逻辑
} finally {
    lock.unlock();
}

```

- 和 `ReentrantLock` 类似，支持同一个线程重复加锁。
- 默认有 **看门狗机制**（自动续期，默认 30 秒）。

---

### 🔑 2. **可重入锁（tryLock 非阻塞/超时）**

```java
RLock lock = redissonClient.getLock("lock:key");
if (lock.tryLock(5, 30, TimeUnit.SECONDS)) { 
    try {
        // 5秒内没抢到锁就放弃
        // 抢到锁后30秒自动释放
    } finally {
        lock.unlock();
    }
}
```

- `tryLock(waitTime, leaseTime, unit)`
    - `waitTime` 等待时间，超过就返回 `false`。
    - `leaseTime` 锁持有时间，到期自动释放（无看门狗续期）。

---

### 🔑 3. **公平锁（RLock）**

```java
RLock fairLock = redissonClient.getFairLock("lock:key");
fairLock.lock();
```

- 保证先来先得，公平队列。
- 开销比普通锁大，性能稍差。

---

### 🔑 4. **读写锁（RReadWriteLock）**

```java
RReadWriteLock rwLock = redissonClient.getReadWriteLock("lock:rw");
RLock readLock = rwLock.readLock();
RLock writeLock = rwLock.writeLock();
```

- 多个读锁可以并发；
- 写锁独占，防止并发写。

适合 **读多写少** 场景。

---

### 🔑 5. **联锁（RedLock / MultiLock）**

适合 **多 Redis 节点** 部署，提高可靠性。

```java
RLock lock1 = redissonClient.getLock("lock:key1");
RLock lock2 = redissonClient.getLock("lock:key2");
RLock lock3 = redissonClient.getLock("lock:key3");

RedissonRedLock redLock = new RedissonRedLock(lock1, lock2, lock3);
redLock.lock();
```

- 需要多数节点加锁成功才算成功（Redlock 算法）。    
- 适合对可靠性要求特别高的分布式系统。
---
### 🔑 6. **联锁（MultiLock）**

```java
RLock lock1 = redissonClient.getLock("lock:key1");
RLock lock2 = redissonClient.getLock("lock:key2");

RedissonMultiLock multiLock = new RedissonMultiLock(lock1, lock2);
multiLock.lock();
```

- 必须 **所有锁** 都加成功才算成功。
- 适合多个资源必须同时锁定的场景。

---

### 🔑 7. **信号量（RSemaphore）**

```java
RSemaphore semaphore = redissonClient.getSemaphore("semaphore");
semaphore.trySetPermits(5); // 最大并发数
semaphore.acquire();        // 占用一个许可
// 业务逻辑
semaphore.release();        // 释放
```


- 控制并发数，适合限流、线程池容量控制等场景。
    

---

### 🔑 8. **闭锁（RCountDownLatch）**

```java
RCountDownLatch latch = redissonClient.getCountDownLatch("latch");
latch.trySetCount(3);
latch.await();  // 等待计数归零
latch.countDown(); // 任务完成，减一
```

- 类似 Java 的 `CountDownLatch`。
- 适合多个任务并行执行，等待全部完成后再继续。

---

⚡ **常用场景推荐：**

- **任务调度、缓存更新** → `tryLock` 普通可重入锁
- **高并发读写缓存** → 读写锁
- **多个资源要么一起锁住，要么一起释放** → MultiLock
- **跨机房/多 Redis 节点可靠锁** → RedLock
- **限流/资源并发控制** → Semaphore