# #1263 Fix: Corrected the external archive error in the jSO algorithm [Merged]

> Username: Tomato-in  
> Created at: 2025-05-13 05:10:26 UTC  
> Updated at: 2025-05-15 04:03:17 UTC  
> Merged at: 2025-05-15 00:51:39 UTC  
> Closed at: 2025-05-15 00:51:39 UTC  
> Url: https://github.com/boostorg/math/pull/1263  

Problem Description: In the jSO algorithm, the external archive was storing successful trials, but according to the original paper, it should store eliminated individuals. The external archive is meant to maintain diversity, and storing successful trials fails to achieve this effect.  
  
Solution: Modified the jSO algorithm's external archive update to store eliminated individuals.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-05-13 13:55:16 UTC  
> Updated_at: 2025-05-15 00:52:20 UTC  
> Url: https://github.com/boostorg/math/pull/1263#issuecomment-2876630840  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1263?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.91%. Comparing base [(`9f03f29`)](https://app.codecov.io/gh/boostorg/math/commit/9f03f2985c0db963050be4d968cee4389ee745d3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`9c329c0`)](https://app.codecov.io/gh/boostorg/math/commit/9c329c0c99651d68b0c751fd4ce4484300859cbb?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1263/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1263?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1263   +/-   ##  
========================================  
  Coverage    93.91%   93.91%             
========================================  
  Files          661      661             
  Lines        54866    54866             
========================================  
  Hits         51526    51526             
  Misses        3340     3340             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1263?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/optimization/jso.hpp](https://app.codecov.io/gh/boostorg/math/pull/1263?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Foptimization%2Fjso.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL29wdGltaXphdGlvbi9qc28uaHBw) | `81.21% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1263?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1263?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9f03f29...9c329c0](https://app.codecov.io/gh/boostorg/math/pull/1263?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: NAThompson  
> Created_at: 2025-05-13 13:55:35 UTC  
> Url: https://github.com/boostorg/math/pull/1263#issuecomment-2876631931  

@Tomato-in : You are correct-this was a mistake on my part. Run is approved.  
  
Do you have any ideas how to test this particular diff?

---

## Comment 3

> Username: Tomato-in  
> Created_at: 2025-05-14 13:44:09 UTC  
> Url: https://github.com/boostorg/math/pull/1263#issuecomment-2880305988  

@NAThompson Yes, we can design a set of experiments to verify whether 'storing eliminated individuals to increase diversity can prevent premature convergence'. The experimental code can be found [here](https://github.com/Tomato-in/jSO-Algorithm-Fix-Validation).  
  
1. **Experiment Design**:  
  
   - **Objective Function**  
     ```cpp  
     double rosenbrock(std::vector<double> const & x) {  
        double result = 0;  
        for (size_t i = 0; i < x.size() - 1; ++i) {  
            double tmp = x[i+1] - x[i]*x[i];  
            result += 100*tmp*tmp + (1-x[i])*(1-x[i]);   
        }  
        return result;  
     }  
     ```  
   - **Solution Space**: [0, 100]^dimension  
   - **Dimensions**: 10, 20, 30, 40, 50, 60, 70, 80, 90, 100  
   - **Repetitions**: 20 experiments per dimension  
  
2. **Experimental Environment**:  
  
   - **Operating System**: Ubuntu 22.04.5 LTS (Jammy Jellyfish)  
   - **C++ Compiler**: GCC 13.1.0 (g++ (Ubuntu 13.1.0-8ubuntu1~22.04))  
  
3. **Experimental Results**:  
    In the graphs, red represents the convergence of jSO_FIX, where the lower bound of the red fill shows the best results from 20 experiments, the upper bound shows the worst results, and the red solid line represents the average of all 20 experiments. Blue represents jSO's convergence in a similar manner.  
  
    - Results for Dimensions 10-30:  
![convergence_plot_d10](https://github.com/user-attachments/assets/f3863df7-83c9-4fc2-9ef3-3f46824df6f7)  
![convergence_plot_d20](https://github.com/user-attachments/assets/96efac41-e668-4e03-b1e3-5b8455e182c6)  
![convergence_plot_d30](https://github.com/user-attachments/assets/29a23b0f-4c5e-4d93-81c1-8d595e8c6ec1)  
        Observing the convergence comparison graphs for dimensions 10-30, we can draw two conclusions:  
        (1). jSO_FIX outperforms jSO in terms of average results, best results, and worst results.  
        (2). jSO_FIX's convergence graphs consistently show two "inflection points," demonstrating its ability to escape local optima.  
  
    - Results for Dimension 40:  
![convergence_plot_d40](https://github.com/user-attachments/assets/93fe99d0-889d-4078-a1c8-0a87b3aacbbd)  
        Conclusion: jSO_FIX outperforms jSO in terms of average results, best results, and worst results.  
  
    - Results for Dimensions 50-70:  
![convergence_plot_d50](https://github.com/user-attachments/assets/06d4813a-804e-456a-b4d2-32b750936aa4)  
![convergence_plot_d60](https://github.com/user-attachments/assets/2be554c7-1367-4a90-a5f6-96d5eb921912)  
![convergence_plot_d70](https://github.com/user-attachments/assets/23217650-d1aa-4692-a3e7-e31954913683)  
        Conclusion: jSO_FIX underperforms compared to jSO in terms of average results, best results, and worst results.  
  
    - Results for Dimensions 80-100:  
![convergence_plot_d80](https://github.com/user-attachments/assets/1c7414c9-f02f-452c-8503-5889ea8ddd09)  
![convergence_plot_d90](https://github.com/user-attachments/assets/3a679896-f368-423d-b948-630c84dba64d)  
![convergence_plot_d100](https://github.com/user-attachments/assets/e087bbcf-1727-42c9-bb8e-1d8f044202b8)  
  
        Conclusion: jSO_FIX underperforms compared to jSO in terms of average results, but achieves better best results.  
  
    **Summary of Findings**  
  
    | Metric\Dimension | 10 | 20 | 30 | 40 | 50 | 60 | 70 | 80 | 90 | 100 |  
    |------------------|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:---:|  
    | Average Results  | ✓  | ✓  | ✓  | ✓  | ✗  | ✗  | ✗  | ✗  | ✗  | ✗   |  
    | Best Results     | ✓  | ✓  | ✓  | ✓  | ✗  | ✗  | ✗  | ✓  | ✓  | ✓   |  
    | Worst Results    | ✓  | ✓  | ✓  | ✓  | ✗  | ✗  | ✗  | -  | -  | -   |  
    | Escaping Local Optima | ✓ | ✓ | ✓ | - | - | - | - | - | - | -  |  
  
    _Note: ✓ indicates jSO_FIX performs better, ✗ indicates jSO performs better, - indicates no significant difference._  
  
4. **Additional Experiments**  
  
    From the experiments above, we can see that for dimensions 10-40, jSO_FIX can obtain better solutions than jSO, and demonstrates the ability to escape local optima in dimensions 10-30.  
  
    However, when the dimension exceeds 40, jSO_FIX no longer shows significant advantages. I believe the main reason is that jSO_FIX fails to demonstrate the ability to escape local optima in dimensions above 30.  
  
    Therefore, I supplemented with an additional set of experiments. Compared to the previous experimental setup, only the algorithm's `max_function_evaluations` was changed from the default value of `10000 * D` to `10000 * D * ln(D)`. The experimental results are as follows:  
  
![convergence_plot_d10](https://github.com/user-attachments/assets/650f92ed-52b0-44f9-91b0-4bf1eaff7897)  
![convergence_plot_d20](https://github.com/user-attachments/assets/e5e42794-33d3-4fbd-a7aa-a2eff8307d11)  
![convergence_plot_d30](https://github.com/user-attachments/assets/a4d2e24c-5918-4088-b9e5-a9496d6928b2)  
![convergence_plot_d40](https://github.com/user-attachments/assets/f2c529bc-5445-4310-aebc-7e0f0ec86e70)  
![convergence_plot_d50](https://github.com/user-attachments/assets/d7c52e6e-c2de-4491-93a3-9f0e2ab60bc0)  
![convergence_plot_d60](https://github.com/user-attachments/assets/159675ef-4ec7-43ba-ad0a-de1edc163b52)  
![convergence_plot_d70](https://github.com/user-attachments/assets/0dccf101-dcf0-4c1d-bcaa-040c805405e4)  
![convergence_plot_d80](https://github.com/user-attachments/assets/1a2f1028-f2d2-40b0-ae41-141b102564fc)  
![convergence_plot_d90](https://github.com/user-attachments/assets/e473053f-0e52-41f9-a3fd-77fa9697c4a0)  
![convergence_plot_d100](https://github.com/user-attachments/assets/be9cf1c8-8222-457b-88a1-c4f6f173216a)  
  
As can be seen, after increasing the number of algorithm evaluations, jSO_FIX can consistently escape local optima, which is extremely evident in dimensions 10-80.  
  
Therefore, we can draw the following conclusion:  
When `max_function_evaluations` is set appropriately, **storing eliminated individuals to increase diversity can prevent premature convergence**.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2025-05-14 14:22:40 UTC  
> Url: https://github.com/boostorg/math/pull/1263#issuecomment-2880444098  

Very detailed analysis; thanks!  
  
@mborland : Do you know if the CI failure is "known" or spurious somehow?

---

## Comment 5

> Username: mborland  
> Created_at: 2025-05-14 15:02:20 UTC  
> Url: https://github.com/boostorg/math/pull/1263#issuecomment-2880582816  

> Very detailed analysis; thanks!  
>   
> @mborland : Do you know if the CI failure is "known" or spurious somehow?  
  
Known. I told @jzmaddock I would fix it, but then I had to take some unplanned time off. I haven't forgotten.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2025-05-14 22:29:54 UTC  
> Url: https://github.com/boostorg/math/pull/1263#issuecomment-2881737570  

@mborland : Merge it over the failure?

---

## Comment 7

> Username: mborland  
> Created_at: 2025-05-14 22:36:44 UTC  
> Url: https://github.com/boostorg/math/pull/1263#issuecomment-2881747324  

> @mborland : Merge it over the failure?  
  
Yes

---

## Comment 8

> Username: NAThompson  
> Created_at: 2025-05-15 00:54:10 UTC  
> Url: https://github.com/boostorg/math/pull/1263#issuecomment-2881918402  

@Tomato-in : Thanks! This was a great first commit!

---
