SharedArrayBuffer needs Atomic operations in multi-threading scenario.

```JavaScript=
const { isMainThread, Worker, workerData } = require('worker_threads');

if (isMainThread) {
  // main thread, create shared memory to share between threads
  const shm = new SharedArrayBuffer(Int32Array.BYTES_PER_ELEMENT);

  process.on('exit', () => {
    // print final counter
    const res = new Int32Array(shm);
    console.log(res[0]); // expected 100 * 500 = 50,000
  });
  Array(100).fill(null).map(() => new Worker(__filename, { workerData: shm }));
} else {
  // worker thread, iteratres 500 and doing i++
  const arr = new Int32Array(workerData);
  for (let i = 0; i < 500; i++) {
    arr[0]++;
  }
}
```
