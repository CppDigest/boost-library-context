# #645 improve mode of the non central chi squared algorithm [Merged]

> Username: vindex10  
> Created at: 2021-06-24 10:26:46 UTC  
> Updated at: 2021-08-03 16:43:51 UTC  
> Merged at: 2021-08-03 16:43:51 UTC  
> Closed at: 2021-08-03 16:43:51 UTC  
> Url: https://github.com/boostorg/math/pull/645  

Hello! I made a little research on the mode of the non-central chi2, and it turned out that the boost library could benefit from it. The main change is aimed at tuning the starting point of the iterative scheme that searches for the mode. Please have a look at the theoretical bases of the change and also at some benchmarking plots in the preprint: https://arxiv.org/abs/2106.12276  
  
The corresponding code that I used for benchmarking you can find on the github repo here:  
https://github.com/vindex10/ncxmax/tree/master/boost-test

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2021-06-25 16:46:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/645#pullrequestreview-693014137  

📁 include/boost/math/distributions/non_central_chi_squared.hpp

```diff
 840 |                return (RealType)r;
 841 |-          return detail::generic_find_mode(dist, 1 + k, function);
 841 |+          bool asymptotic_mode = k < l/4;
```

> Username: mborland  
> Created_at: 2021-06-25 16:46:14 UTC  
> Updated_at: 2021-06-25 16:46:38 UTC  
> Url: https://github.com/boostorg/math/pull/645#discussion_r658902750  

Did you mean `(k/l) < RealType(1)/4`? Your paper mentions using (k/l) < 1/4 as the conservative threshold. As written `asymptotic_mode` will always evaluate to false, and the ternary operator will always evaluate `starting_point = 1 + k` which is what the original code had.

> Username: vindex10  
> Created_at: 2021-06-25 16:52:38 UTC  
> Url: https://github.com/boostorg/math/pull/645#discussion_r658906599  

Hi @mborland, thank you for your attention here!  
  
Well, mathematically `k < l/4` and `k/l < 1/4` is the same :) Mainly, I tried to avoid dividing by the parameter, because it might turn out to be zero.  
  
Could you please elaborate a bit more on why would the asymptotic mode always evaluate to `false`? Is it something to do with `RealType` divided by `int`?

> Username: mborland  
> Created_at: 2021-06-25 17:05:26 UTC  
> Url: https://github.com/boostorg/math/pull/645#discussion_r658913897  

You are correct, and can ignore my comment. I read as `k < 1/4` not `k < l/4`.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-06-25 16:48:36 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-868696798  

For your bench-marking I highly recommend looking at [Google Benchmark](https://github.com/google/benchmark). There are a ton of examples in the reporting/performance folder.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-07-22 17:49:02 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-885098706  

Looks like we let this kinda stagnate for a while; just got the ball rolling again by approving the CI.

---

## Comment 4

> Username: vindex10  
> Created_at: 2021-07-30 16:29:18 UTC  
> Updated_at: 2021-07-30 16:37:37 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890006149  

Hi all! Thank you for giving attention to the PR. I'm coming back with the plots!  
  
## Few comments:  
  
* Below `baseline` is: 67d25f4a40c56dbf3c49438c3e62f813e09d2e77 and `new` is: 78df4a61a820153ecb0cd91ef9f6ea235c7c014b  
* Vertical line denotes the location where switch between approx and default mode is happening  
* errorbars - standard deviation among 200 runs  
* central value - mean among 200 runs  
* this benchmark is conducted on `long double`s on the contrary to `double`s used in the publication. Therefore, the location where the baseline starts failing appears a bit later  
  
NOTE: each run is actually ~5-7000 runs conducted by google benchmark internally, it doesn't provide stddev itself.  
  
## Plots  
  
**Fixed number of d.o.f and we increase noncentrality λ:**  
  
![fixed_k_2](https://user-images.githubusercontent.com/1962652/127681344-572eaa0b-13a1-404b-9538-252ebd061425.png)  
![fixed_k_15](https://user-images.githubusercontent.com/1962652/127681348-98160adb-3b05-43b5-9eaa-759eec0697be.png)  
![fixed_k_50](https://user-images.githubusercontent.com/1962652/127681349-74c2ee25-2d86-485d-a1fe-b83d2ef035a9.png)  
  
**Fixed noncentrality and we increase number of d.o.f k:**  
  
![fixed_nc_1](https://user-images.githubusercontent.com/1962652/127681350-ca82e3b3-fd05-48f8-b6c7-f18437811c56.png)  
![fixed_nc_30](https://user-images.githubusercontent.com/1962652/127681352-2ef06822-c43d-4b9b-978e-066ed0a9dfd3.png)  
![fixed_nc_100](https://user-images.githubusercontent.com/1962652/127681354-3f3dcfaa-be75-415e-bf04-cf7fe033c2d4.png)  
![fixed_nc_500](https://user-images.githubusercontent.com/1962652/127681356-38fefe73-6502-453b-b743-4813e3575aaa.png)  
  
**We fix scale k/λ and plot the performance along k:**  
  
![fixed_scale_0_05](https://user-images.githubusercontent.com/1962652/127681358-0ecb7768-5b0c-4d74-9f60-714f36c7d661.png)  
![fixed_scale_0_15](https://user-images.githubusercontent.com/1962652/127681359-f620bc07-adb8-4ff6-a1b2-d93f8a545927.png)  
![fixed_scale_0_25](https://user-images.githubusercontent.com/1962652/127681360-15d5d018-ed99-4909-9792-5315cfa7104e.png)  
  
  
## Technical info:  
  
* Raw json files of the benchmarks:  
[report.baseline.json.gz](https://github.com/boostorg/math/files/6908789/report.baseline.json.gz)  
[report.new.json.gz](https://github.com/boostorg/math/files/6908864/report.new.json.gz)  
  
* compilation command for the `test_distributions_mode.cpp`:  
  
```  
g++ -std=c++17 -I$HOME/opt/benchmark/include -L$HOME/opt/benchmark/lib64 -I../../include/ test_distributions_mode.cpp -pthread -lbenchmark  
```  
custom include paths for benchmark were used because I compiled the recent benchmark library. it had few arguments that I needed that were not present in the released version (though they claim their master is stable, so I think it is fine :)) Specifically:  
  
`benchmark::CreateRange` and CLI argument: `--benchmark_context`  
  
* The command I used to run the compiled executable:  
  
```  
./a.out --benchmark_enable_random_interleaving=true --benchmark_context=boost_math_revision=`git rev-parse HEAD` --benchmark_repetitions=200 --benchmark_report_aggregates_only=true --benchmark_format=json > report.json  
```  
  
Therefore, you can find the exact revision that was used for benchmarking in the file itself

---

## Comment 5

> Username: vindex10  
> Created_at: 2021-07-30 16:32:58 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890008293  

Just in case someone would like to play with plots  
[visualize_benchmarks.ipynb.gz](https://github.com/boostorg/math/files/6908906/visualize_benchmarks.ipynb.gz)

---

## Comment 6

> Username: vindex10  
> Created_at: 2021-07-30 17:12:35 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890035293  

error bars look suspicious. I recompiled executables and reran on one particular test, they appeared to be fixed. so I reran full benchmarks, it takes several hours on 200 runs.. but I don't think error bars here play any role :) they are 100 times smaller even in the worst case.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-07-30 19:00:21 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890092432  

I'll example this in more detail later, but it looks good to me.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-07-30 19:21:02 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890103801  

@jzmaddock: Should we ask for an ULPs plot as well? If not @vindex10 might well enjoy learning that concept anyway.  
  
(And agreed, this does look good.)

---

## Comment 9

> Username: vindex10  
> Created_at: 2021-07-30 22:16:49 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890214827  

interesting read about ULPs, thanks for the reference: https://www.boost.org/doc/libs/1_77_0_beta1/libs/math/doc/html/math_toolkit/ulps_plots.html  
  
In the meanwhile I have more consistent plots.  
  
![fixed_k_2](https://user-images.githubusercontent.com/1962652/127716209-02017175-862e-443b-8d1f-7cfb900282fc.png)  
![fixed_k_15](https://user-images.githubusercontent.com/1962652/127716212-838deded-e5df-48a7-a403-93361b5f05e5.png)  
![fixed_k_50](https://user-images.githubusercontent.com/1962652/127716213-42139f92-d648-49b9-b8ab-86771c8b9155.png)  
  
![fixed_nc_1](https://user-images.githubusercontent.com/1962652/127716215-8d847b05-2679-4396-87e4-cfc7710a2c91.png)  
![fixed_nc_30](https://user-images.githubusercontent.com/1962652/127716216-be84d31c-91be-410d-917b-8e8417d6353e.png)  
![fixed_nc_100](https://user-images.githubusercontent.com/1962652/127716218-3066ccbd-8191-4197-a062-14eba7e26d41.png)  
![fixed_nc_500](https://user-images.githubusercontent.com/1962652/127716219-51a9b75d-cb79-4711-ba04-00e545a9573f.png)  
  
![fixed_scale_0_05](https://user-images.githubusercontent.com/1962652/127716220-59bd3e17-032f-48c9-8dbe-fc6286e09edc.png)  
![fixed_scale_0_15](https://user-images.githubusercontent.com/1962652/127716221-cdce9f54-eb08-4112-8652-4d1d639f6b40.png)  
![fixed_scale_0_25](https://user-images.githubusercontent.com/1962652/127716222-edee1a7c-2597-4c61-8817-9667b1b79622.png)  
  
  
and sources:  
[report.baseline.json.gz](https://github.com/boostorg/math/files/6910152/report.baseline.json.gz)  
[report.new.json.gz](https://github.com/boostorg/math/files/6910153/report.new.json.gz)

---

## Comment 10

> Username: NAThompson  
> Created_at: 2021-07-31 00:06:03 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890260024  

Very cool. The build failure is a network error so you might want to make a meaningless diff to get it clean.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2021-07-31 08:54:40 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890314663  

> Should we ask for an ULPs plot as well?  
  
No real need, all this does is improve the initial "guess" for the mode, before it gets refined, so the result will be unchanged whatever.

---

## Comment 12

> Username: vindex10  
> Created_at: 2021-07-31 10:25:18 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890326261  

if somebody killed the previous build on purpose, please let me know ) otherwise I keep pushing empty commits before the green tick appears :)

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2021-07-31 12:25:39 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890340884  

I've approved the run: not sure why we need to keep approving stuff... but it is what it is I guess!

---

## Comment 14

> Username: mborland  
> Created_at: 2021-07-31 14:53:38 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890360004  

> I've approved the run: not sure why we need to keep approving stuff... but it is what it is I guess!  
  
For first time contributors it is the [default setting for GHA.](https://docs.github.com/en/actions/managing-workflow-runs/approving-workflow-runs-from-public-forks#about-workflow-runs-from-public-forks) You can [change it](https://docs.github.com/en/github/administering-a-repository/managing-repository-settings/disabling-or-limiting-github-actions-for-a-repository#configuring-required-approval-for-workflows-from-public-forks) if you so choose.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2021-07-31 16:06:30 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890369478  

> For first time contributors it is the default setting for GHA.  
  
Nod.  I thought perhaps once we'd approved the first run we wouldn't have to do so for later runs, but apparently not.

---

## Comment 16

> Username: NAThompson  
> Created_at: 2021-07-31 16:35:00 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890372753  

>  I thought perhaps once we'd approved the first run we wouldn't have to do so for later runs, but apparently not.  
  
That would be a better default.

---

## Comment 17

> Username: vindex10  
> Created_at: 2021-07-31 22:29:25 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890411300  

@NAThompson now it is clean! :)

---

## Comment 18

> Username: NAThompson  
> Created_at: 2021-07-31 22:43:59 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-890412541  

Looks good to me. @jzmaddock?

---

## Comment 19

> Username: vindex10  
> Created_at: 2021-08-03 12:10:43 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-891795291  

Hi! Is there anything we can do to move forward?

---

## Comment 20

> Username: jzmaddock  
> Created_at: 2021-08-03 16:43:42 UTC  
> Url: https://github.com/boostorg/math/pull/645#issuecomment-891998825  

Yes.... nag me ;)

---
