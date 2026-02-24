# #1026 - benchmark logging [Open]

> Username: sdarwin  
> Created at: 2024-07-18 00:39:04 UTC  
> Updated at: 2024-07-18 12:40:12 UTC  
> Url: https://github.com/boostorg/json/issues/1026  

Testing a new benchmark machine. "The (Hetzner) EX44 Dedicated server’s hidden gem is the Intel® Core™ i5-13500 processor."    
  
Ubuntu 24.04. CPU year 2023.    
  
After numerous optimizations, this is an example benchmark:    
  
![Screenshot from 2024-07-17 17-44-40](https://github.com/user-attachments/assets/c8fafd92-0b53-4a68-9ae9-ac85656883d6)  
  
It appears there are two instances of unknown disruptions even though most services have been locked down.  Nothing else would be using that processor since it's isolated by cgroups.   The newest intel processors may only be taking the scaling frequencies as a suggestion, and occasionally override the choice.  
  
The benchmark command is `./bench -i:b *.json`.     
  
---  
  
The following idea may or may not help, it would be an experiment.  Could the benchmark executable `bench` itself be enhanced, by calling external shell commands, before and after each file (apache_builds, canada, citm_catalog, etc.)  ?  
  
Log output to /tmp/json-benchmarks.log  
  
Before processing a json file.  
  
```  
date +'%Y/%m/%d %H:%M:%S:%3N' >> /tmp/json-benchmarks.log  
echo "Starting to process canada.json with clang" >> /tmp/json-benchmarks.log  
lscpu -e >> /tmp/json-benchmarks.log  
top -b -n 1 | head -n 20 >> /tmp/json-benchmarks.log  
```  
  
(run benchmark)  
  
After processing a json file.  
```  
date +'%Y/%m/%d %H:%M:%S' >> /tmp/json-benchmarks.log  
echo "Finished processing canada.json with clang" >>  /tmp/json-benchmarks.log  
lscpu -e >> /tmp/json-benchmarks.log  
top -b -n 1 | head -n 20 >> /tmp/json-benchmarks.log  
```

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-07-18 12:16:44 UTC  
> Url: https://github.com/boostorg/json/issues/1026#issuecomment-2236351148  

If it's simpler to implement, not all of the above commands are required.    
  
Imagine another shell script running in the background on the server, that once-per-second repeatedly measures the cpu frequency, `top` output (to view processes), and other diagnostics.  Then, `bench` doesn't need to be able to run those.    What is important is to correlate the start and stop times of the individual benchmarks with the diagnostics.  
  
- write start/stop timestamps to log file (millsecond accuracy)  
- along with each timestamp, include details that `bench` already knows about: gcc or clang. serialize or parse, pool or memory store, name of the json file or benchmark that's being processed.  
  
Then `bench` doesn't need to call any external scripts.   (Well, if it could mention the current git commit, that might be interesting.  But not critical at all.)  
  
Considering further, perhaps the output is similar to what it's already outputting:  
  
```  
Parse mesh.json,clang x64/sse2,boost (pool),3200,5006,441  
Parse mesh.json,clang x64/sse2,boost (pool),3339,5225,441  
Parse mesh.json,clang x64/sse2,boost (pool),3339,5221,441  
Parse mesh.json,clang x64/sse2,boost (pool),3339,5220,441  
Parse mesh.json,clang x64/sse2,boost (pool),3339,5218,442  
```  
  
except,  
  
- add timestamps also  
- dump to a log file, rather than stdout/stderr  
  
```  
2024-07-18-08:10:32:725 Starting Parse mesh.json,clang x64/sse2,boost (pool)  
2024-07-18-08:10:33:822 Completed Parse mesh.json,clang x64/sse2,boost (pool),3200,5006,441  
2024-07-18-08:10:34:321 Starting Parse mesh.json,clang x64/sse2,boost (pool)  
2024-07-18-08:10:35:425 Completed Parse mesh.json,clang x64/sse2,boost (pool),3339,5225,441  
```

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-07-18 12:40:11 UTC  
> Url: https://github.com/boostorg/json/issues/1026#issuecomment-2236395972  

Ok, I can do that.
