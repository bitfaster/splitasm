# splitasm

A command line tool to break down BenchmarkDotNet asm markdown files into a single file per benchmark method to compare impact of changes.

E.g.
`splitasm.exe C:\repo\BitFaster.Caching\BenchmarkDotNet.Artifacts\results`

Output:

- Am asm.md file per benchmarked method (assembly code per benchmarked method)
- A summary of disassembled code size per benchmarked method

Output is grouped into directory by target benchmark then target framework:

![image](https://user-images.githubusercontent.com/12851828/141734076-e8b12c93-d1b7-47b0-9018-2da1b75d35f8.png)


Example summary of disassembled code size per benchmarked method:

| #  | Method      | Size (bytes) |
| -- | ----------- | ------------ |
| 0 | BitFaster.Caching.Benchmarks.Lru.LruCycleBench.ConcurrentLruEvent | 175 |
| 1 | BitFaster.Caching.Benchmarks.Lru.LruCycleBench+<>c.<ConcurrentLruEvent>b__11_0(Int32 | 3 |
| 2 | BitFaster.Caching.Lru.TemplateConcurrentLru`5[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib],[BitFaster.Caching.Lru.LruPolicy`2[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib]], BitFaster.Caching],[BitFaster.Caching.Lru.HitCounter`2[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib]], BitFaster.Caching]].Cycle | 905 |
| 3 | BitFaster.Caching.Lru.TemplateConcurrentLru`5[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib],[BitFaster.Caching.Lru.LruPolicy`2[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib]], BitFaster.Caching],[BitFaster.Caching.Lru.HitCounter`2[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib]], BitFaster.Caching]].GetOrAdd(Int32, System.Func`2<Int32,Int32> | 306 |
| 4 | BitFaster.Caching.Lru.TemplateConcurrentLru`5[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib],[BitFaster.Caching.Lru.LruPolicy`2[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib]], BitFaster.Caching],[BitFaster.Caching.Lru.HitCounter`2[[System.Int32, System.Private.CoreLib],[System.Int32, System.Private.CoreLib]], BitFaster.Caching]].Move(System.__Canon, BitFaster.Caching.Lru.ItemDestination | 508 |
| 5 | System.Collections.Concurrent.ConcurrentDictionary`2[[System.Int32, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib]].GrowTable(Tables<Int32,System.__Canon> | 1229 |
| 6 | System.Collections.Concurrent.ConcurrentDictionary`2[[System.Int32, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib]].TryAddInternal(Int32, System.Nullable`1<Int32>, System.__Canon, Boolean, Boolean, System.__Canon ByRef | 1039 |
| 7 | System.Collections.Concurrent.ConcurrentDictionary`2[[System.Int32, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib]].TryGetValue(Int32, System.__Canon ByRef | 259 |
| 8 | System.Collections.Concurrent.ConcurrentDictionary`2+Tables[[System.__Canon, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib]].GetBucketAndLock(Int32, UInt32 ByRef | 97 |
| 9 | System.Collections.Concurrent.ConcurrentDictionary`2+Tables[[System.Int32, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib]].GetBucket(Int32 | 109 |
| 10 | System.Collections.Concurrent.ConcurrentDictionary`2+Tables[[System.Int32, System.Private.CoreLib],[System.__Canon, System.Private.CoreLib]].GetBucketAndLock(Int32, UInt32 ByRef | 123 |
| 11 | System.Collections.Concurrent.ConcurrentQueueSegment`1[[System.__Canon, System.Private.CoreLib]].TryDequeue(System.__Canon ByRef | 270 |
| 12 | System.Collections.Concurrent.ConcurrentQueueSegment`1[[System.__Canon, System.Private.CoreLib]].TryEnqueue(System.__Canon | 125 |
