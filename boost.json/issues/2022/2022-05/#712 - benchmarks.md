# #712 - benchmarks [Closed]

> Username: vinniefalco  
> Created at: 2022-05-28 19:20:08 UTC  
> Updated at: 2022-06-01 01:47:08 UTC  
> Closed at: 2022-06-01 01:47:08 UTC  
> Url: https://github.com/boostorg/json/issues/712  

We haven't run the benchmarks in a while. On controlled, properly set up hardware we should run the benchmarks for every Boost release of JSON and keep track of any regressions or improvements. We should also investigate how the per-commit benchmarks work, what kind of hardware they are running on, and make a determination if they are providing us value. There is the possibility that they are not accurate.

---

## Comment 1

> Username: sdarwin  
> Created at: 2022-05-29 14:23:26 UTC  
> Url: https://github.com/boostorg/json/issues/712#issuecomment-1140459640  

> how do the per-commit benchmarks work  
  
The jenkins server has a job to monitor pull requests on boostorg/json. When a pull request is submitted it checks out the code and runs this script:  
https://github.com/CPPAlliance/Infrastructure-Docs/blob/master/benchmarks/json/runbenchmarks.py  
  
Parameters of runbenchmarks.py are configurable. The command it runs is overridden:  
```  
benchcommand = "sudo cset shield --exec -- nice -n -20 sudo -u nodejenkins ./bench -i:b *.json"  
```  
cset isolates the process on a separate CPU core.  
  
The overall job takes approximately 1 hour to run benchmarks on the develop branch and the PR branch and compare them.  
  
> how about the accuracy of benchmarks  
  
The benchmark servers are set up using techniques to minimize interference from other programs. Many services are disabled and stopped. IRQ interrupts are moved to another cpu. Hyperthreading and turbo boost are disabled.  cpu frequency is set to not vary.  These techniques are discussed at https://github.com/CPPAlliance/Infrastructure-Docs/blob/master/benchmarks/json/README.md   
- prepare-hardware.sh adjusts cpu settings and interrupts.   
- prepare-server.sh cleans up services and cronjobs.  
- prepare-json.sh installs boost, git, and other prerequisites.  
  
There is "invisible" evidence, hiding in plain sight, about how accurate the benchmarks are, which is the following. Consider that in the early iterations of the benchmarks, before many optimizations were added, the results might show bright red or green, with variation of 1%, 2%, 3%, even though nothing had changed in the codebase. If a benchmark varied by 2% for no reason, that was the test environment's fault.  Each run of the tests was unpredictable, due to random variations on the benchmark server, caused by interference from other processes using memory, cpu, shared busses on the hardware, and other random factors.   However, the current benchmarks display a remarkable number of 0.0% results.  Not 0.1% , 0.2%, 0.3%.  A 0.0% might seem unimportant but in fact it verifies the consistency between multiple test runs.  
  
One might harbor a suspicion though, "Are the benchmarks really working? Perhaps they are spitting out a bunch of 0% results every time". To investigate that, there is also a cronjob which runs benchmarks once per day, comparing recent git commits with selected older commits from 2020. Here is an example output https://benchmark.cppalliance.org/jsonbenchmarks-intel/merged-results-across-commits/charts/2022-05-28-05-00-02.html   Notice at the top of the page under "Parse apache_builds.json", how the times from 2020 are worse than recent 2022 results. These results are computed from ./runbenchmarks.py and are recomputed each time, not relying on cached values.  They vary by 10% or more.    
  
> what kind of hardware they are running on  
  
Server hosting at knownhost.com. The specs:  
  
CPU Dual Intel Xeon E5620 @ 2.4GHz (8 Cores + HT)  
Memory (RAM) 32GB DDR3 ECC  
RAID None  
Drive 1 240GB Enterprise SSD

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-29 14:46:45 UTC  
> Url: https://github.com/boostorg/json/issues/712#issuecomment-1140464308  

Wow
