# splitasm

A command line tool to break down BenchmarkDotNet asm markdown files into a single file per benchmark method. Output in this format enables use of file diffs to assess impact of code changes on disassembler output.

E.g.
`splitasm.exe C:\repo\BitFaster.Caching\BenchmarkDotNet.Artifacts\results`

## Output

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

## Github action integration
  
[Example github action](https://github.com/bitfaster/BitFaster.Caching/blob/main/.github/workflows/benchpr.yml) that clones the repo, builds splitasm then uses it to process benchmark output:
  
  ```
 jobs:
  bench:

    runs-on: windows-latest

    steps:
    - uses: actions/checkout@v2
    - name: Setup .NET Core
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 6.0.x
    - name: Install dependencies
      run: dotnet restore
    - name: Build
      run: dotnet build --configuration Release --no-restore
    - name: Clone splitasm repo
      uses: actions/checkout@v2
      with:
        repository: bitfaster/splitasm
        path: splitasm
        ref: ''
    - name: Build split asm
      run: dotnet build splitasm --configuration Release
    - name: Benchmark
      run: dotnet run --project "BitFaster.Caching.Benchmarks" -f net6.0 -c Release --filter *Lru*
    - name: Post process disassembly
      run: splitasm\splitasm\bin\Release\net6.0\splitasm.exe %GITHUB_WORKSPACE%\BenchmarkDotNet.Artifacts\results
      shell: cmd
    - name: Publish Results
      uses: actions/upload-artifact@v1
      with:
        name: Benchmark Artifacts
        path: BenchmarkDotNet.Artifacts```
