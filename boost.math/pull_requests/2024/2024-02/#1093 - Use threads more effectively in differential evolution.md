# #1093 Use threads more effectively in differential evolution [Merged]

> Username: NAThompson  
> Created at: 2024-02-13 22:54:31 UTC  
> Updated at: 2024-02-14 00:58:07 UTC  
> Merged at: 2024-02-14 00:57:02 UTC  
> Closed at: 2024-02-14 00:57:02 UTC  
> Url: https://github.com/boostorg/math/pull/1093  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2024-02-13 23:29:06 UTC  
> Updated_at: 2024-02-13 23:30:59 UTC  
> Url: https://github.com/boostorg/math/pull/1093#issuecomment-1942848653  

Using this code:  
  
```cpp  
void test_sphere() {  
  std::cout << "Testing differential evolution on the sphere function . . .\n";  
  using ArgType = std::vector<float>;  
  auto de_params = differential_evolution_parameters<ArgType>();  
  de_params.lower_bounds.resize(12, -1);  
  de_params.upper_bounds.resize(12, 1);  
  de_params.NP *= 150;  
  de_params.max_generations *= 10;  
  de_params.crossover_probability = 0.9;  
  double target_value = 1e-8;  
  std::mt19937_64 gen(56789);  
  auto local_minima = differential_evolution(sphere, de_params, gen, target_value);  
  CHECK_LE(sphere(local_minima), target_value);  
}  
```  
  
I obtain:  
  
```  
./a.out  11.57s user 0.30s system 113% cpu 10.430 total  
```  
  
on `develop`, and   
  
```  
./a.out  14.23s user 0.31s system 564% cpu 2.574 total  
```  
  
on top of this PR. So it appears this is an unambiguous win-though in fairness my laptop has 10 threads so there is much performance left to be extracted.  
  
The PR is clean under `-fsanitize=thread` as well as `-fsanitize=address -fsanitize=undefined`.

---
