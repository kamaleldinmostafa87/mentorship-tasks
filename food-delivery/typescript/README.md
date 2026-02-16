## Retry Mechanism

```typescript
async function retry(fn: Function, maxRetries: number): Promise<void> {
  for (let index = 0; index < maxRetries; index++) {
    try {
      await fn()
        .then((result: string) => {
          console.log(result);
        })
        .catch((err: string) => {
          console.log("catch", err);
        });
    } catch (e) {
      console.error(`Attempt ${index + 1} failed: ${e}`);
    }
  }
}

// - call endpoint promise
// - cache it for specific time

async function fetchData() {
  const response = new Promise((resolve, reject) => {
    setTimeout(() => {
      reject("failed");
    }, 1000);
  });

  return response;
}

console.log(retry(fetchData, 3));

// - call endpoint promise
// - cache it for specific time
```

## Promise-Based Cache with Expiration

```typescript
// implement a promise-based cache with expiration using nodejs typescript
// return new promise if expiration time finished

type CacheEntry<T> = {
  promise: Promise<T>;
  expiresAt: number;
};

function createPromiseCache() {
  // ky: { cacheEntry}
  const cache = new Map<string, CacheEntry<any>>();

  // pass promise and its key here
  return function cachedFn<Args extends any[], Result>(
    key: string,
    factory: (...args: Args) => Promise<Result>,
    ttlMs: number,
  ) {
    return async (...args: Args): Promise<Result> => {
      const entry = cache.get(key);

      if (entry && Date.now() < entry.expiresAt) {
        return entry.promise;
      }

      const newPromise = factory(...args);

      cache.set(key, {
        promise: newPromise,
        expiresAt: Date.now() + ttlMs,
      });

      newPromise.catch(() => cache.delete(key));

      return newPromise;
    };
  };
}

const cache = createPromiseCache();

const getUser = cache(
  "user-data",
  async (userId: string) => {
    const res = await fetch(`https://api.example.com/users/${userId}`);
    return res.json();
  },
  5 * 60 * 1000,
);

const p1 = getUser("123");
```

## Rate-Limited API with Express

```js
import RateLimiter from "express-rate-limiter";
import express from "express";

const app = express();
const PORT = 3000;

const limiter = new RateLimiter({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: "too many requests",
});

// This route is only protected by the global limiter
app.get("/api/public-data", (req, res) => {
  res.send("This is public data.");
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```
