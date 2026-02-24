# #369 - boost::histogram::axis::variant, allow users to choose between sorted_array+std::lower_bound and eytzinger_layout+eytzinger_binary_search [Open]

> Username: jhcarl0814  
> Created at: 2022-12-08 04:19:41 UTC  
> Updated at: 2022-12-23 20:39:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/369  

According to the test,  
  
- When input follows normal distribution, sorted_array+std::lower_bound is faster;  
- When input follows uniform distribution, eytzinger_layout+eytzinger_binary_search is faster;  
[Eytzinger Binary Search - Algorithmica](https://algorithmica.org/en/eytzinger)  
[Static B-Trees: up to 15x faster than std::lower_bound : cpp](https://www.reddit.com/r/cpp/comments/sv012b/static_btrees_up_to_15x_faster_than_stdlower_bound/)  
  
(`boost::histogram::axis::variant` is currently using `std::upper_bound`.)  
  
[comparison (x86-64 gcc (trunk), -std=c++23 -O3 -Wall -pedantic -pthread)](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCAA7ACspAAOqAqETgwe3r56KWmOAkEh4SxRMQm2mPYFDEIETMQEWT5%2BXJXVGXUNBEVhkdF6CvWNzTltQ929JWUSAJS2qF7EyOwc5gDMScRMwCxMANQA4m5u%2B6hJjix4AF6YEOZmAPLrD7MmGgCCG8HI3lj7JnWbgihAUAHohuhTJZrAA6BCA7DvL6fNAMIaYVRbfZpW4AfQI%2BwYAPWABF9hAuIC3NSzBo3usrKiBBiscQcTdMAT9hFaKhkABrPG4zAk8kANkk%2BzBHNuqCoEGCBAZFmlMsBEqlMqlGv2omQCC5tGCXJF0tlmHlisEb0%2BYjwwGYCggktm%2ByV%2ByYJniFgY3tJpB53t91ip8VJgKZKI%2BaNZ2JF3IAjl5ogBPPFoLyCDWU6m0%2BmR5Ee4bATAEBTB5NpjOLbPhwufD3ETAKLy0ct4zCpgjXYKl4iVlPEdOZusRxlFwT7Zut9sKPF8gDu0TxEVr6EH1dHBH9DejHq7Pb70WthLwYv2GkDCcJAovXDdJliUf2r/dVApd7zZKJj%2BfyLfQD3QvQ9eyME88EDMx9gAKn2AUVQAoDXwiYMBX9C8vR9PBrFw%2BsJ0%2BZDX3PXVQOPYhFSg2D4IBSx9gfPdAKfCNCLfZsCCWYkcIIr5YhY/cpyUBoDVPfZVD/F83xvGjdSpHjAMXBA6FFCAvyBXUGAkpDkLxVcvDoRwGDxLZMCoMsRIiWjVTvODeX5IURUQ1jkLU8loLgr86IgVCfXQ8MSVOcTGLfZjtJk9YkQi3VdIifT22CPEqCoZ0AD8EOC192M4%2BC91Cz5J0JPZgggW1eMk19IRAEBtgYdBUBYPEsAANzwFZpw3RlXzBMEAHU6FoHlRS8JR0H2Igzgiepgk9HFMEwUb%2BHZAhDWnQw6pYIl%2BnZRhgBNMLKpAFgCC4ABOE71lifZSwYCBiHQEqVTVLpaoaUa2GIJQGBCAlFzwDFiE7IwTVm%2Bb5v2X7lvakr9oIdAqqzPBFoapVGr%2BghiDwWKam/bB9nwIYMYiCADoYHxolahc8CuctqSVREqquG75hxWGqtJ96KeNamKyBOmIoZphVAejL9kWilpJIn8NEjYCwDADSZdwyw8FKpj/2coCsIsHD8NJA7iHXCB8fRzHoEYWYnI%2BNX%2BMAg6FBIAhidZkAol2m6mHmA7XAgD3LbCsjwIov2NbF4nOW5SXyWlzrzzl3Uq2HGssx3Tqle11WQvVq2iJLMsK2w3cyX1w3jcJs3NMt63kTCp9ys1rxxomRoLwOg0DYYVAqoxJh0BHPlBTZ1BF2h%2BSiNDiWL2j1VY/ln8E5HWsU9VNOVbCtW66I6cWzbDsA/7YMdfHPXMGEhAIFzmmC/DSuiLy7PkKYBvUH2VwW%2BdtuBE7kBu97jN%2B4FQew8b7IVbggduX90BLCYNjIEdUvC8kwIiF%2BBgkgjWFJgNE6AFAgVqgAWibkvNeUl36LEJN%2BU4DwqhMFQWDS47BaLQXIcg6haClCYIULCbcJUArUgYfnNwfozBmBFnfdeRDXyP0bsMMhP5QHgK7gQE%2Bv9fj2UASPDeb5x7hzPJPGWM945DgXsnRWMJlYZ2QrXcRQEZw73nEuFca4swbivkfA69iAaONqk7OGLtMBux9p7Z23sPakAvvnbW/pZj4Odq7YqHsRZVw1oBSRz9X66jkZ/BRSi%2B6qJAB3IBCTiE%2BI/h3KqkDtgwLcHAhBSCqE0PQOg9hOD0DRO6IUiqJCG48KBAwupI0xpU3oQ8bppw%2BnzUaQILBnDF7cKYQ8fhgjhGj1fKIzRJBxbaOArqKestZ7knnknbMqdTHpxrlnTeTAFBKEaD7YMNi5wLiHg49cB9dy6w1D5Cw9zd7djAvvFxkSZYrI1nfO%2BHB5i0E4PEXgfgOBaFIJ3DgNITk4kWMsUUGweChM0OC%2BYAD4hXkhRwSQvAWAgElLCAAHBoSl6xxRmDpedC6J1KWkFhfCxFvAFAgCvAQHF8w4CwCQGgFgSRlJkAoBAEVYr6AxGAAoZgqCECoAIKQZqrVMAADU8CYEXI8JIjBOBYpoO2aI3LvI4tICCZgw4jW8BFWwQQjwGC0FTJarAewjDiDhbwfAzYHB4Cai2S1mIMEN1WFipUVRLXGgiNsYcHgsCWpNmS7g4K%2BAGHldq3V%2BrDVppkIIEQYh2BSALfIJQahLW6DaAYIwKA076ExtyyA8xzg1G5RwaJHVSTQisJYBl%2BxcHPEHT1MQA1cEGvQIYRwyBB0XDAUorlVQME1BcLVUYrRSCBBNNMAYbQ8jpAEBu3IqRD0MCmFtQYy6A0CC6CMTwLQr0dFvdIi9pQ922Gkce8Yr6d2XofAsJYKw5j6ChTCy1iKxKUvFLgyUV1kCzvPsQLMCEKS4EIOszFsxeB8p9QKpAobkBPwlZQBo8rlCGCqEIZVi5YVYulXQaBGQKMhFoNRoe7L7X1RlQMeViqFDKtVQx2VjwG7sdoyG1QGCPjEHlZwXghG6j4FhbwfghbRDiFLWp8tKh1A%2BtINW/QhhjANtjc2kqCKLgZA7V2jUvbrADqHesEdY653zWna1Ody1mw9y5Wi4DP7lMsaozRujOHmwRt4IubYSQ7UQrA2yiDnBsBSaI0QdkqhoOwalMABDFJ0YobdBAZFfabD7HQ%2Bl2i6wHw4f5fMQ0PcBgWaJSS0gZL1jxFhKdLg4oLq9Y0OKelGgzCJf05y2wehcNaAtqQABXANCEs4OscDY35PYrw/MINH0MggEkEAA):  
```C++  
#pragma GCC optimize("O3")  
#include <bits/stdc++.h>  
  
constexpr size_t n = (1<<20);  
constexpr size_t block_size = 64 / sizeof(int); // = 64 / 4 = cache_line_size / sizeof(int)  
alignas(64) int a[n], b[n+1];  
  
constexpr size_t query_count=(1<<20);  
int targets[query_count];  
int results_eytzinger[query_count];  
int results_lower_bound[query_count];  
  
int eytzinger(int i = 0, size_t k = 1) {  
    if (k <= n) {  
        i = eytzinger(i, 2 * k);  
        b[k] = a[i++];  
        i = eytzinger(i, 2 * k + 1);  
    }  
    return i;  
}  
  
int search(int x) {  
    size_t k = 1;  
    while (k <= n) {  
        __builtin_prefetch(b + k * block_size);  
        k = 2 * k + (b[k] < x);  
    }  
    k >>= __builtin_ffs(~k);  
    return k;  
}  
  
int main()  
{  
    std::random_device rd;  //Will be used to obtain a seed for the random number engine  
    std::mt19937 gen(rd()); //Standard mersenne_twister_engine seeded with rd()  
    std::uniform_int_distribution<> distrib(std::numeric_limits<int>::min(), std::numeric_limits<int>::max());  
    for (size_t i = 0; i != n; ++i)  
    {  
        a[i]=std::round(distrib(gen));  
    }  
    std::sort(std::begin(a),std::end(a));  
  
    eytzinger();  
  
    for (size_t i = 0; i != query_count; ++i)  
    {  
        targets[i]=std::round(distrib(gen));  
    }  
  
    {  
        auto start = std::chrono::steady_clock::now();  
        for (size_t i = 0; i != query_count; ++i)  
        {  
            results_eytzinger[i]=search(targets[i]);  
        }  
        auto end = std::chrono::steady_clock::now();  
        std::chrono::duration<double> elapsed_seconds = end-start;  
        std::cout << "elapsed time: " << elapsed_seconds.count() << "s\n";  
    }  
    {  
        auto start = std::chrono::steady_clock::now();  
        for (size_t i = 0; i != query_count; ++i)  
        {  
            results_lower_bound[i]=std::lower_bound(std::begin(a),std::end(a),targets[i])-std::begin(a);  
        }  
        auto end = std::chrono::steady_clock::now();  
        std::chrono::duration<double> elapsed_seconds = end-start;  
        std::cout << "elapsed time: " << elapsed_seconds.count() << "s\n";  
    }  
    for (size_t i = 0; i != query_count; ++i)  
    {  
        assert(a[results_lower_bound[i]]==b[results_eytzinger[i]]);     
    }  
}  
```  
![image](https://user-images.githubusercontent.com/5171262/206354747-ab94802a-edc9-4002-bdb3-2b5805ade8b8.png)

---

## Comment 1

> Username: HDembinski  
> Created at: 2022-12-08 10:15:20 UTC  
> Updated at: 2022-12-08 10:16:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1342452239  

Hi, thanks for bringing this up, this sounds pretty cool. Are you interested in contributing a patch? We could make a compile-time option for the builtin variant axis so that a user can switch between eytzinger and standard binary search.  
  
Before we are doing this, some more research is necessary, however. We need some benchmarks that show the performance on "typical" distributions that one encounters in the wild. Uniform is a rather artificial distribution that is not usually encounted. Gaussian occurs often (as the prototype for "peaked" distributions). Then there are exponential and power-law distributions.  
  
A platform-independent implementation in Boost cannot use GNU extensions. Can you write an implementation and benchmark it without the use of `__builtin_prefetch` and `__builtin_ffs`? An optimising compiler should replace naive code into specialized instructions automatically if they are available on the target platform.

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-12-08 10:19:50 UTC  
> Updated at: 2022-12-08 11:00:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1342463764  

Please also keep in mind that an axis in practice is rarely larger than about 10000 entries. A typical axis has 100 bins. So we are talking about a situation where the sorted array typically fits into the L1 cache. Your "axis" array has ~1M entries, which is not realistic. I tried the benchmark with smaller numbers and still found that eytzinger was 1.5-4 faster.

---

## Comment 3

> Username: HDembinski  
> Created at: 2022-12-08 10:59:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1342534143  

Your implementation returns the value in the sorted array for eytzinger, but we need the position of the value instead of the value itself.

---

## Comment 4

> Username: HDembinski  
> Created at: 2022-12-08 11:26:27 UTC  
> Updated at: 2022-12-08 14:07:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1342570430  

Here is an implementation that is closer to reality. [Link to Godbolt](https://godbolt.org/z/TsfcqK1na)  
  
```c++  
#include <bits/stdc++.h>  
#include <array>  
#include <vector>  
#include <boost/core/lightweight_test.hpp>  
#include <iostream>  
  
class eytzinger {  
public:  
    eytzinger(const std::vector<double>& a) :  
        a_(a), b_(a.size() + 1), idx_(a.size() + 1) {  
        init();  
        idx_[(*this)(a.back()+1)] = a.size();  
    }  
  
    int operator()(int x) const {  
        size_t k = 1;  
        while (k <= a_.size()) {  
            k = 2 * k + (b_[k] < x);  
        }  
        k >>= __builtin_ffs(~k);  
        return idx_[k];  
    }  
  
private:  
    int init(size_t i = 0, size_t k = 1) {  
        if (k <= a_.size()) {  
            i = init(i, 2 * k);  
            idx_[k] = i;  
            b_[k] = a_[i++];  
            i = init(i, 2 * k + 1);  
        }  
        return i;  
    }  
  
    const std::vector<double>& a_;  
    std::vector<double> b_;  
    std::vector<int> idx_;  
};  
  
class stopwatch {  
public:  
    stopwatch(const char* message, double* duration = nullptr) :   
        message_{message},  
        duration_{duration},  
        start_{std::chrono::high_resolution_clock::now()} {  
    }  
      
    ~stopwatch() {  
        std::chrono::duration<double> elapsed = std::chrono::high_resolution_clock::now() - start_;  
        if (duration_) *duration_ = elapsed.count();  
        if (message_[0])  
            std::cout << message_ << elapsed.count() << std::endl;  
    }  
  
private:  
    const char* message_;  
    double* duration_;  
    std::chrono::time_point<std::chrono::high_resolution_clock> start_;  
};  
  
constexpr size_t query_count=(1<<20);  
int targets[query_count];  
int results_eytzinger[query_count];  
int results_lower_bound[query_count];  
  
template <class Dist>  
void run_tests(const char* title, Dist& dist) {  
    std::cout << title << std::endl;  
  
    std::mt19937 gen(1);  
  
    size_t n_list[] = {2, 10, 100, 1000, 10000};  
  
    for (const auto& n : n_list) {  
        std::cout << "  n " << n << std::endl;  
        std::vector<double> a(n);  
  
        for (size_t i = 0; i != n; ++i)  
            a[i] = dist(gen);  
  
        std::sort(std::begin(a), std::end(a));  
  
        for (size_t i = 0; i != query_count; ++i)  
            targets[i] = dist(gen);  
  
        double t1, t2;  
        {  
            eytzinger search(a);  
            stopwatch _{"    eytzinger   elapsed time: ", &t1};  
            for (size_t i = 0; i != query_count; ++i)  
                results_eytzinger[i]=search(targets[i]);  
        }  
        {  
            stopwatch _{"    lower_bound elapsed time: ", &t2};  
            for (size_t i = 0; i != query_count; ++i)  
                results_lower_bound[i]=std::lower_bound(std::begin(a), std::end(a), targets[i]) - std::begin(a);  
        }  
  
        std::cout << "    ratio                   : " << t2 / t1 << std::endl;  
  
        BOOST_TEST_ALL_EQ(  
            std::begin(results_lower_bound),   
            std::end(results_lower_bound),  
            std::begin(results_eytzinger),  
            std::end(results_eytzinger)  
        );     
    }  
}  
  
int main()  
{  
    std::uniform_real_distribution<> uni;  
    run_tests("uniform", uni);  
  
    std::normal_distribution<> norm;  
    run_tests("normal", norm);  
  
    std::exponential_distribution<> expo;  
    run_tests("exponential", expo);  
  
    std::chi_squared_distribution<> chi2(5.0);  
    run_tests("chi2", chi2);  
  
    return boost::report_errors();  
}  
```  
  
- It encapsulates the eytzinger logic and the creating of the temporary array b in a class. The size of arrays a and b are not known at compile time, so we need to use vectors.  
- Bin edges are doubles in general, not integers.  
- eytzinger must return the index and not the bin edge. This costs us another array lookup, since the internal index k must be translated back into the an index in the original array. If there is a faster way to handle this, let me know.  
  
The results seem fairly noisy on godbolt, so this should be checked for stability. Still, depending on the distribution, eytzinger seems a bit faster, from 20 % up to a factor 2.

---

## Comment 5

> Username: jhcarl0814  
> Created at: 2022-12-09 09:24:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1344059820  

@HDembinski Thank you for the implementation. I'm trying to solve some of the problems while there are some open issues.  
  
# find first set  
Here are some implementations of find_first_set from [BitScan - Chessprogramming wiki](https://www.chessprogramming.org/BitScan) and [Bit Twiddling Hacks](https://graphics.stanford.edu/~seander/bithacks.html). I'm using the [second one](https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightParallel) in other tests.  
[ffs (x86-64 gcc (trunk), -std=c++23 -O2 -Wall -pedantic -pthread)](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCAAHFykAA6oCoRODB7evnrJqY4CQSHhLFEx8baY9vkMQgRMxASZPn4JdpgO6bX1BIVhkdFxrXUNTdlD3b3FpYMAlLaoXsTI7BzmAMzByN5YANQma24RhAoA9AoE6KaW1gB0CPvYJhoAguub25h7BxGoKQQnaGImBOtDwwAQBAA7pgwRCAPoETDnO6JRIPJ6vMwbBhbLy7fZuJznIGsdEvDHBAg7KhUBRcCBeSkANkkCJ2ADcZicTkJMIYdthiAIds8GFhiAoBKQdgApPAsHaZTDSmVMSUMRVeBgMYLg1g7CEERIKEDc4DEJiJBB4ZAKG7nQz8YjoG6YdBeE4APyUhnFJyOBAQTGQAGs7RCWLR1gAtaIpADyDAASrCesE%2BcQMSYAOxWF47As7Sk7UxrPPPQtFqg7CCcjGVnPlyuV9lfAAiNdbJgArNhOzsALQ7LgzGZfR5rPtcfYWAvcna8qnssAcBQ7AgWui6nYaNdEYdr307ABecZ2QPO9ebBadNeQ7eHM45T6uVksMyv18bn%2Bvha7k/RNYO2nMsfwbbM2x/HNIPzQtKiUKDczAwt732DsNBnRCYIrQsgQIRYNVLctoKzF5ixpBQzAZZlWSXLkeT5DVBWFUVxXVaULDoWgdgsRZ8FDA0CCNE0zQtK0bTtB0GCdF03Q9b1GL9AMg1DcMCEjGM4wURMU3BAhlHqMR6CjckkNggs8CoWs0LQjQxzwgid0w8yi0EEsHxZJ953vSEuJ2KJ1wQT4GH6YgdlQatTyFfzjnCjVA0%2BYhUx/LszCZNDBxohFaw/UCXPnSzOzHZABwHZycIs6taz2NKd1UDQGsahqADFWra1risHDK1jMcrK0K6rzCZOqmva0a2s6sqgOHdK8oqqt%2ByGkaNFalrmrWtbJoy2I%2BsLAbUuGjR6vWk6VrO07mq26bJF2yrFtqo61ie56XtetYro7Xq5v6qqDrq7sAcBoHge7D7H2%2B3DMHw4hCPKkjyTItyKLWajBBZNlOW5Xl%2BWYjVWOididgAWSYISdgAdWtAhaFQUNpVYoFIQXINEjwC1BOE00TnNS1rVte06mkkhZPdL0fTFaJ/UIFSwzudSoyxWMhW05NU04moM2Qe5TKbParPZGygLs88occjCIYstyiLnE53N82huIChKdhCkponCyKzwDNdhWdpK9NIZDr3nSUFtbPA1y4LgNGjhqawiNVPjMOZAsYO2/ICtYf3nUJ4wAFWwEAFo0dsMvZaUEsIh81i4G4f32mrDtUEc9hc78XMrEPEnaPAxBLRPqRIcL0HQDkazVBQfDdddUANS1u41INaGrCLU/XeVMA/DuUIfEDdYLeH5vgzBEP35tUOmr6z5%2BmtBoe1QaUfx%2Bx0N9CX%2B3g/cwPj/m3/CcJw7D1J8rd5q/nctYbqV8g57AgtAgad8m5PxfkBDKxtoGNm/qA38f9ALAVmrOEBYDKyoWuNNLg%2BD0GwJ/vAv6R0aTIMgtNNBP8MGEKIQWHBAFpo7TLJg9hBYSFWG4bdZsh8wE0MbnVKgDDUHvywZ/Ah0CWzjlwTsG6FsiGCIyuo6%2BhYxG/gkUtR6MimFyLAawpRf4VFcM%2BiIsBWjL52L0VQ%2BR7kpodloc3ER%2BiCwORhiWOGLiKRI1pJIVGBB0ZUgiBEeiJMGjBB2AEEwbgs7PEkF4REYUIBcAAJw5OzCnNwQZiD0DXNgG4iSYRKAlFKHYAAJQytBR7KCFCGVAiRpQAGkYSBnqRUpMvojIc2NFzSEYybhayRAoRIQoeYsBYLqG4JBgAnE4sIUQDB1jk2lnCCOnhSZujhAEIQXBiJmXmpZCA0TX7Gz8Rqc2Z80AMHOJgVQMzXJUmCFgVQnluwWF%2BR2DK7dXEaGlMOaUkhYhgrMNKbs2YIU5OlGYKFBY1iBx/kyBIOxuwou7KCtRMKdhrBRWYRFw54UFmkNApkhKAawrJWsJksK0XYqxZIFlZgYXQMkN2aUawGUsrWPisw0hhwoq4IS7F6LXFMhZZICl3YmU7DMBSpk%2BLJBYrWBS8h0qwHdlFWsXl2LCVmDZQak1LKo66t/JIJVaUIX4q4EatYoqzD4prtKaO1rrxmCNVIT1ZKdhkq4CynYKKdgUp2OlNusCNFPJeW8sKjI0a0R2FgCIxBGTaAYCyB8R0NBrCoNmHJ6BsxcAiJIZAEQNBMFiDkkRdzXLfN%2BRYKA0TJEDmiWOAAVGmzAGas05skMg/%2B2KdrdkgpYPeWYgmIypBRbs4TIn%2BRidyTp8oKZiAjlrGsbqJVjkNCM7kYzIQTKCgoaZsyLTzMWcs1ZhARCGC2TspQiRDKInQEck5Zyz6XOuSgo29lTb%2BIeT%2BeNiJE0fKba8ltAKHzArAeqyNWLFUQpJZGwlarvXNjhQiiFmqKXIs9Uqol0pMXYcrPq2FKLDWwtpeqk1ZqKOFmJXyk1ZKsSeu1VitRZGuU/1tbCo19rsVkskO6pVTrmXMdRaK7sWK3UQsFQpwlXq1G8ugb6vlwaDWWoZfioNSKqU/xDZ68N4rJWRpI0a2V2GSJxoEAm95yaImpvTZmvA2bc2oPqoW4tpby2VurbW%2BtGjG1fJgzdP5baIh7F7PHCIA4Rw9r7QOzzQ6R0PDHT2SdFhp2mWwhiLYE8djnHaZCUmO7gWJC8BEUEyAQA/jK4kCrBAtYQHAyWYpva2AXqYMAZUaaFh1cwL290FpqgPhCg7RIG4xxF2gb1hQ/XMBwkbEtlb0EZNpsWKTdIa3czjb2wILb0CHQNAOxYc46AQAoAQEKBgqBbvWnBHCC8ngMn7a2LTEMt3HuQggB%2BCCMCz4%2BLYQWb0RAWuVYQIDkHZ2Li3a1g9p7IAjvVAJOgYb9AsuVEtEoUeGVrtI/uwIVHL2EBvaRB96ocJvuhj%2B6gAHY4hznYIGtjRN8IDo/2z2nnAg4QPjx8aN0EyFiCEB04u6EANsDbW38jCE6t6uMrMTlACwqQEgJDsWXq2vhuG18LgnYutQEDh1rg4pXEcgFcFGDRh8MQzLwOyA5jWXKda1vUHrUyVsc7Plj2r9Axu7dpyItXyOye3ccGwOEyRKQEnD6Tx7z3YRU8lN4Wn9OQxZbZ37mdjDiIvHA6895qRTxsgAI5eGiAATzp%2BLggaFskW7cFiXK5YXPLuGANggCgewWCr7X%2BvpucvlQKm5C8XhaC97hBRacfzB/EDr2gEfE6x%2B22LJP6fChZ%2B0l6gv6vS/h%2BCFH3NcfVIt8z%2BRv3xfy%2BG%2Bn/LOfk2k9t%2B74UFFgfh%2B7%2Br4LxiJ/l%2Bd8F0b8v9j9G8185pN8kQp92c59pRL839CV4DkY4CoDp839RUkDaRuw4YXh2RUA8BR5M0GAEQkRe9zdzlVdrctRLISAWBdlBA4R8BiQ8AIhPsTsDhO9aIssmCzc1dXZogbQ4RQQFle8CROCEQHhbsFkGBAdpQ%2BCp4kpkAhD5RjgxCspG9JwpCmBVBAd28mtrcWACBckcktUdgBsZCRwRFbwIAy9VtPk80nw8AdgwAwAMpb9QCnwIFLA8BlcwFu8oY%2B8/k8AcsHweDoBGA9CEZ5oA8Rt1wsUCBCUCAWUCBRUCBsD7cKDfxmtWsd1LtzAzA58dgjdp5o92AapCUhojD7NdEbwh4bC8By97DUFHDnDXDpp3CV8T9eEvDOJfD%2BF5wAC3959OJT82w58IB/DRCgictIiVdIYX8YDaRpxSExiliJj6ge9AiRildvEXFwIairdysYcdg8jOUKIzAiiDARdR5SiFtOVpRKjepY0DjrDbC2QnDmjeEnCXC3CQDOiNDZweifDLEbZn9oDAC99%2B9gjwDVjKJ1jiBNioSZipdmxMDKIIEgJzj4TETpidiMjsJ9iEcji2sEATjvwzjaQ1hLj8cSiN47iKi0okjqjoFXiGi7CiwHCvjWjfih9/jPDrhvC%2Bj2EBjUCr9KSkSYTkZsSAiJS2xZj%2BEwS0Dr8VipTJitjoS5TdiCTnEDjsjjjTiCjQlqTrj142B6SHjGSbpniWS6i3imimEWifj2i/j79uiBTeiQSdgRSFiIT39ZS0IKIwk1TZT5T%2BE0SboVTQlpSpjtjNT8TT4iTocSSySkIKSFBuxjSCdTSyj8iLSmQ0jmSf5WTGiOTPjZxvi2iOwOjXTAT3TgSf5O5bZBigDcTGFYTF1gzWzQz2E0TsDIz0zoz1TkT4yojGyeJ4x4whA844QC4py4RngAgAg4RsAABFCAbOW2Sgm7EAKIYAYICAZspYlODc5sNXVwA80U30kcbDecLc27Xc/cw8yiOYE8u8m3MUC8n0hAoUr022bs388cyc6c2c6chcpc1c9c/KTcwsNXB8mQp8688HACt888hCl8qC0863OCz88Et/d6G86CgsM8j8p896E8/8%2BcCwCcuckC%2Bcxc5ctc18mCrCzAPc%2BCy8oY48jClCkijiufdC%2BaW85i7c7Cp84dAizC7c1Cvi0JH8%2BcCi22KioCmc7AOcsChiyCwSwiq3ES1ix8mSukLirSyS27aSr8/iiSt80Sgy0GSy4S0y3i8yrAuSv8kRJSmi1S6ciCtEhINE5ONy6i4CzyjSny0gNEsijRdyoKuc7y0UoYsKuKwM/8qKlSmKtc0K3s2Yh3OdHXJgfc5XBDXCLUEg84BQSXOaH8RtH4P4W7IEZIC7aIIUCUcqs5bCDgOYWgTgbsXgPwDgLQUgJ7DgA3d00rBYJYT4dYHgUgAgTQdquYX7FkG4DQN1WIWIbMbMZFHlHJbsNFTqjgSQXgFgCQBqUgXq/qwa3gE0UFGavq9q0gOAWAJAV5doDJEgcgSgDYhQAyEIWgIQBAJnXqqatAFgVmAwaob6yoP6gG2a0gYG0GgYYABQZgY0f6ggWG1AEGugaIeMDJKGyEQG3gZ65AZ4BEk0DgQm1QdoWofAXq3gfgQQR9cQf1em%2BQJQNQGG3QBIAwIwFAHo/QFgk0WAZgM06q84UgdkAYbgWILQUcAa2bdIMmgca7NCV8awLEQceMC4gccmIZAcbudAQwRwe8PWwMEkdAS6sa5YPQa7YICG36/6/GzgKajcTAFYKayEMSJ2u6va7q06mGwa7ASm5AV6sKVQWIJkAcXNYAZAe8CYogkMMcCAYat8GwAUfAIgMKSamYXgG6mWuYIKJgcUSgeakAQ1G4fJHJKOTlMtfVDQeVfQTgA6v226gazgS6kAa62a2WvaswHq/2tu6aruuYCWiUdIEASQIAA%3D%3D%3D):  
```C++  
#include <bits/stdc++.h>  
#include <boost/core/lightweight_test.hpp>  
#include <iostream>  
  
int ffs1(uint64_t v)//Sean Eron Anderson, Jim Cole, Jason Cunningham https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightLinear  
{  
    int c;  
    if (v)  
    {  
        v = (v ^ (v - 1)) >> 1;  // Set v's trailing 0s to 1s and zero rest  
        for (c = 1; v; c++)  
        {  
            v >>= 1;  
        }  
    }  
    else  
    {  
        c = 0;  
    }  
    return c;  
}  
  
int ffs2(uint64_t v)//Sean Eron Anderson, Bill Burdick https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightParallel  
{  
    if(v==0) return 0;  
    int c = 64; // c will be the number of zero bits on the right  
    v &= -int64_t(v);  
    // if (v) c--;  
    if (v & 0x00000000FFFFFFFF) c -= 32;  
    if (v & 0x0000FFFF0000FFFF) c -= 16;  
    if (v & 0x00FF00FF00FF00FF) c -= 8;  
    if (v & 0x0F0F0F0F0F0F0F0F) c -= 4;  
    if (v & 0x3333333333333333) c -= 2;  
    if (v & 0x5555555555555555) c -= 1;  
    return c;  
}  
  
int ffs3(uint64_t v)//Sean Eron Anderson, Matt Whitlock, Andrew Shapira https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightBinSearch  
{  
    if(v==0) return 0;  
    int c;  // c will be the number of zero bits on the right,  
            // so if v is 1101000 (base 2), then c will be 3  
    // NOTE: if 0 == v, then c = 31.  
    if (v & 0x1)   
    {  
        // special case for odd v (assumed to happen half of the time)  
        c = 1;  
    }  
    else  
    {  
        c = 2;  
        if ((v & 0xffffffff) == 0)   
        {    
            v >>= 32;    
            c += 32;  
        }  
        if ((v & 0xffff) == 0)   
        {    
            v >>= 16;    
            c += 16;  
        }  
        if ((v & 0xff) == 0)   
        {    
            v >>= 8;    
            c += 8;  
        }  
        if ((v & 0xf) == 0)   
        {    
            v >>= 4;  
            c += 4;  
        }  
        if ((v & 0x3) == 0)   
        {    
            v >>= 2;  
            c += 2;  
        }  
        c -= v & 0x1;  
    }  
    return c;  
}  
  
int ffs4(uint64_t bb)//Martin Läuter (1997), Charles E. Leiserson, Harald Prokop, Keith H. Randall https://www.chessprogramming.org/BitScan#With_isolated_LS1B  
{  
    if(bb==0) return 0;  
    constexpr int index64[64] = {  
        0,  1, 48,  2, 57, 49, 28,  3,  
        61, 58, 50, 42, 38, 29, 17,  4,  
        62, 55, 59, 36, 53, 51, 43, 22,  
        45, 39, 33, 30, 24, 18, 12,  5,  
        63, 47, 56, 27, 60, 41, 37, 16,  
        54, 35, 52, 21, 44, 32, 23, 11,  
        46, 26, 40, 15, 34, 20, 31, 10,  
        25, 14, 19,  9, 13,  8,  7,  6  
    };  
    constexpr uint64_t debruijn64 = 0x03f79d71b4cb0a89;  
    return index64[((bb & -bb) * debruijn64) >> 58]+1;  
}  
  
int ffs5(uint64_t bb)//Kim Walisch (2012) https://www.chessprogramming.org/BitScan#With_separated_LS1B  
{  
    if(bb==0) return 0;  
    constexpr int index64[64] = {  
        0, 47,  1, 56, 48, 27,  2, 60,  
        57, 49, 41, 37, 28, 16,  3, 61,  
        54, 58, 35, 52, 50, 42, 21, 44,  
        38, 32, 29, 23, 17, 11,  4, 62,  
        46, 55, 26, 59, 40, 36, 15, 53,  
        34, 51, 20, 43, 31, 22, 10, 45,  
        25, 39, 14, 33, 19, 30,  9, 24,  
        13, 18,  8, 12,  7,  6,  5, 63,  
    };  
    constexpr uint64_t debruijn64 = 0x03f79d71b4cb0a89;  
    return index64[((bb ^ (bb-1)) * debruijn64) >> 58]+1;  
}  
  
class stopwatch {  
public:  
    stopwatch(const char* message, double* duration = nullptr) :   
        message_{message},  
        duration_{duration},  
        start_{std::chrono::high_resolution_clock::now()} {  
    }  
      
    ~stopwatch() {  
        std::chrono::duration<double> elapsed = std::chrono::high_resolution_clock::now() - start_;  
        if (duration_) *duration_ = elapsed.count();  
        if (message_[0])  
            std::cout << message_ << elapsed.count() << std::endl;  
    }  
  
private:  
    const char* message_;  
    double* duration_;  
    std::chrono::time_point<std::chrono::high_resolution_clock> start_;  
};  
  
constexpr size_t query_count=(1<<23);  
uint64_t targets[query_count];  
// int results_ffs1[query_count];  
// int results_ffs2[query_count];  
// int results_ffs3[query_count];  
// int results_ffs4[query_count];  
// int results_ffs5[query_count];  
int result_ffs1, result_ffs2, result_ffs3, result_ffs4, result_ffs5;  
  
void run_tests() {  
    std::uniform_int_distribution<uint64_t> dist(std::numeric_limits<uint64_t>::min(), std::numeric_limits<uint64_t>::max());  
    std::mt19937 gen(1);  
    for (size_t i = 0; i != query_count; ++i)  
        targets[i] = dist(gen);  
  
    double t1, t2, t3, t4, t5;  
    {  
        stopwatch _{"ffs1 elapsed time: ", &t1};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs1[i]=ffs1(targets[i]);  
            result_ffs1+=ffs1(targets[i]);  
    }  
    {  
        stopwatch _{"ffs2 elapsed time: ", &t2};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs2[i]=ffs2(targets[i]);  
            result_ffs2+=ffs2(targets[i]);  
    }  
    {  
        stopwatch _{"ffs3 elapsed time: ", &t3};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs3[i]=ffs3(targets[i]);  
            result_ffs3+=ffs3(targets[i]);  
    }  
    {  
        stopwatch _{"ffs4 elapsed time: ", &t4};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs4[i]=ffs4(targets[i]);  
            result_ffs4+=ffs4(targets[i]);  
    }  
    {  
        stopwatch _{"ffs5 elapsed time: ", &t5};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs5[i]=ffs5(targets[i]);  
            result_ffs5+=ffs5(targets[i]);  
    }  
  
    // BOOST_TEST_ALL_EQ(  
    //     std::begin(results_ffs1),   
    //     std::end(results_ffs1),  
    //     std::begin(results_ffs2),  
    //     std::end(results_ffs2)  
    // );  
    // BOOST_TEST_ALL_EQ(  
    //     std::begin(results_ffs1),   
    //     std::end(results_ffs1),  
    //     std::begin(results_ffs3),  
    //     std::end(results_ffs3)  
    // );  
    // BOOST_TEST_ALL_EQ(  
    //     std::begin(results_ffs1),   
    //     std::end(results_ffs1),  
    //     std::begin(results_ffs4),  
    //     std::end(results_ffs4)  
    // );  
    // BOOST_TEST_ALL_EQ(  
    //     std::begin(results_ffs1),   
    //     std::end(results_ffs1),  
    //     std::begin(results_ffs5),  
    //     std::end(results_ffs5)  
    // );  
    BOOST_TEST_EQ(result_ffs1,result_ffs2);  
    BOOST_TEST_EQ(result_ffs1,result_ffs3);  
    BOOST_TEST_EQ(result_ffs1,result_ffs4);  
    BOOST_TEST_EQ(result_ffs1,result_ffs5);  
}  
  
int main()  
{  
    run_tests();  
  
    return boost::report_errors();  
}  
```  
  
# prefetch  
Did not find a cross-platform solution.  
  
# alignment  
replacing  
```C++  
std::vector<T> b_;  
```  
with  
```C++  
#include<boost/align/aligned_allocator.hpp>  
#include<new>  
std::vector<T, boost::alignment::aligned_allocator<T, std::hardware_constructive_interference_size>> b_;  
```  
should improve the result. But the test environment is too unstable to observe the difference.  
  
# convert from breadth-index to inorder-index  
Currently the lookup table method is the fastest.  
I implemented a converting function hoping it can save the space of the index lookup table. But as there are too many variables and branches, the code is only half as fast as `std::lower_bound`. (`std::lower_bound` becomes much much slower when adding one more `0` at the end of `n`, but as you said we should also take care of the situations when `n` is not large.) I will come back later to see if this is optimizable.  
[using `eytzinger_one_based_to_inorder_zero_based` in place of lookup table (x86-64 gcc (trunk), -std=c++23 -O2 -Wall -pedantic -pthread)](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCBmAMwAbKQADqgKhE4MHt6%2BeqnpjgJBIeEsUTHxSXaYDplCBEzEBNk%2Bfly2mPaFDPWNBMVhkdF6Cg1NLbnto30DpeUSAJS2qF7EyOwc5nHByN5YJnFuTqPEmKwH2CYaAIJbO3uYB25iwCSECCwXV7fx9177hwAbjUiMQvjc7gxdv9HodiIZ0KhPnFLhDflCHk8QgB3cE/bYYmFPZAIYgCVB4yHQgFuCKoNIEAD0YjwwAYzNorJC6AA%2BmJaKhRKCAHQIZLJSno6mw2n00aMtCnRmc4AIAjYzCstU8giYUai8WSgnS4mcliEBSUm7BAgAaioVAUEC8NoSkh1tsBC0ZjKEZwYtuwZID1wYWGICgEpFtFjotBjq3wyAA1ra1QRkgoQD7gPDkgg8MgFMLpgx%2BMR0MLMOgvIyAH5KBHRRkRd5MFPFtUsWhbABa0TSAHkGAAlLUEZSNfmdb4mADsVhutuXtrwVAggIOABFtxoFrbTgRVgGNAdF9cV6vBLbkLbt7a3WfbT6b7bsXHbVFbQQEJhbQwhmIW1UCoW0AC8B0/C1gIDH8/2IcdvkvQE7zMBJ7wAWldd0CA3BYzyQlcXzXW08JvDCMIIpcVxIjdUISW0NFUDQWNYliADFOK4zj91vSi4i3W04jMKiLxo0C6PMBimLY7jZK43jbX4wSuHQuJz0vWiUKkxjmI0TiOPYwzDMU5TbQADlEzSJO0tDdP0hyjKcxz9NM%2B9JCs8TSNs6TVDifyAsCoK4jcgTbRE9TCOXLT6N0gBWBLEqS5K4tClTPOXQ9jxvUT5x3CFrWvGgwx5AxRh5JRcPSCCPS9WcF1nDQAE4suIAM3AACWuEceQsABJAAVAAqarMBAiBRp1BYMJ5HkIhdWhHAYHldjA2g8Nyud8uuYiiuCXkyoICrMFwl1BDdWrvV9f1A2DW1Q3DSMA3TTNs0ZXMmHzQti1LctK2rWsG39cMWzbDtRQIbstj6wRMGAaIAlQYBBwiQEnC8S0bhfedz2x5q1w3bdd33VqT1EvGmptA87zCrhycZO9muxAt6G8mnLhRe8uHwrGGauJrLzBSxrHpxmWpO7KwUi3m7y275JrtTAAE8CDA4J4eIHkBEwOamCUXkiB5YISHDHkILJXX9YmvAartAhTh1zpMDYQQVpWQRowV78HZ5MlsTdrwPdtL3ldV9Xoi1kJLerT2bZ1xWVbVowI%2B16PeXwYBCADwQeduBrqOi9d7cwHW/ezggiYEvcosvYOCHQEAQED052wQJgInoCB8OlsTly904FC8Rbdwyt8WcwCBi9L1B/bQQOCDAMBt25mucZry8vbpAgiBYX2Z55J2XaOufBG3KeD/oI/y4wqAuDArwnieSefbLk%2BCAw7nppXnva6IhmvfoFQI6g8Ijn0PowY%2B7sK4CQgHfB%2Bhwn7n1flAjCZgFhf1QhYOuDcQDmgYBALeO897%2B3Aa7N%2BpA4GP0OM/EuxDr5oO7hpX%2Bwc44ekAcArwoCfakMgfPbcsD75ULcDQ6es8UEMI/pg0iBDUDbyRHQnh18V5oQEfAtwiCX77zfqg9BBx2KUIQdQpBWjxELBAIQ%2BRZdFFv0YevFc/dxwVU4WAy%2BEDy5n24a4shKCuAYXYU4rhtDrFQNHtZCAock4a0jjrCIetqyVy3CvAutc17JOYQePUQ8K7CwEv4kBLjnZuLfqE5hEQW7JhKcuPKdjlydCUKudc4TE7h01qnWJ%2BtYqqKEU0sOydWlR3adWHkGcs7aIYd3HcVdc7pNSb3dJ2DG6OAngUq%2B5DjFiPnqQCJLTolpy2c0vpuzBnp1ZKMqBEzRg4PLNiRovI9Y6i8MkTuNT5l5OcZ4wp3jNkvPSespRpAfnMJ6ZElOAy4noCknWLphjhHbMOW08FwzTm8MEB/dB%2BE5zCIMeo6hcKokIv1kizOKL34MIBWk%2BZtSDn4rBYSkZJK0WVNlttdJdS/wE2BTsgl1ZOnYqfni0FMTEX0voeixexMXmzMpZlTJw8clbjeYEx2XiSXWDpj/aVL5LmLLwMsj5qyoGkD%2BeQgV/ShX632b0ml5qhkipsdubVIBrm3L5AoB5TyJ6AuXC%2BQWjj8n6qKYar1z4GaXgsbvKxKr3Eoj5UYzRGzUVoL0canxIAU3z1QeSuZ6SfUrlNUc8FkLoU4thdSwVaciVnIzZ/LN0qQ213zdyk5xKlG2Ipb/R1SyREX0%2BSSo18by6WpBWavZjbaW2uReXC59dG7OorK691zz23zIQqqDhSqe0Gu%2Bcu9JUBw0KKjdo5RCRi0aNocgjN4zk0DqPWmm94ja11rzWWkdxyi2xtLVa8txzK0MprcGy8Y6bXNqrai7%2BTDf7VOXWy4Njrm5nBJO3Tuba5lQbmaTDJg9h49xrsRIu96%2BECQlck3NfcZ1NwYC3RDHcJ4ocvC%2BNlDTu0XtPkRgSSS5mkYyUeNqjFR4MdoPUgm6bWMcy3EmkjfN86cdDfY1hdp92Rt7dGrcKzA0ZtvoImFzGTHVvQYyjV3rZN93k7aRVamvmsa3KeuN57dOJv01wKRjq8EyLkRG/ewTNkfp0wm0lujDP1o3qZ1d2p/VBMPSEgSFm/1%2BMwEAgJMXo0QaC%2BJTl8Lx0QrY1uaukn5lwcowhtuNGu78eM7aRjBMgNpwSRx%2Bj5XBYS14%2BZgNlnoEpa45Vxp1Xjm8q0yW9L1qK12tMQFyZOXpl/xSdJ%2BrK6msBh64iw2xsKwR3NqgNOEBcMNeYS1iLynyHbafSJ9%2B7Qjt1sG9%2BwtaEoU%2BcW3SydR70Xzixf1/lL6C0PZbWM9Bj6jNPvuxO77Pi6NTcvGh2bgHBPsu6x9ptfW1Hva/a%2B4Vj3RsLHFVM87K4pVg%2BYRhvbyqDshMsHTSwgODYbZW6bdbm3seUtC%2BupLh28t1sU55yLhHsA%2BZOzo69dm/MfzvQL%2Bhf3UuUop%2B%2Bt7uK4eZd/a2pY9P5kU/l09sr02WV49qdDpXG9yPwdbkh2j6uIdTZg6z%2Bx%2BvCuG5KyhtD3xdQsCeUwXUTwCBK2SIwVgf4Bp4hOF4BwFWX2yxS6aiAaAGCjAWSAYEDgSBPF95zOyTB9wgBeUwHkEAU/RgiJnpgJY45dykXV5hL5ox4HQKoPPBeIJF%2BsLaEvVSZvMOCIQUrgXZsV6ryYOKFhTxxUEvefPo128pft8kpgXgiDAU9/CUEXcs9T9QDeAQowdKqF4qvu0uOO2mdTFzJlzM6B/ggPvwxgkM819o/uHf8yz%2BCTMLaIatp9%2BWFIrnnvFgKkD5pm4W0G%2BmVTdf4z8OYxN7RHQIA6xkxQdf4CYKlstcts18d5tbRL9XBR8XlzckDf4MNL8ohM58F8JydZdgMdQqcGATY1sBxNtL8R8lhioDo9ZgETos8eQr8u4lhkw%2BB9pSomDjpcJoCYDa4XxcC2D8DghStLAu8eRP9v9Jkx85YCo5kvZW8qpTM8AaZBINBY5bYX9NCG8b9m9YCJIz8cUL82C6DDCUt0kND7xVCIA8BoxH9n9oCmVO9K8ZDe85D9C8A3CVwP8vCe9B8woM9P9fDhZhYB8/DoofCGA29HDwon89D69wNJVFDsDuNspfDAtx85kI8o9HVY9QQE8vhk8ZDAtCiQR49DgBoc85QCBG4WQ2Qj5GiVRuQ%2BRaABQhRqi3Bajo824KwblTg3ZI97YA9HBgQjZYZiAqBohGB1gKo44vgURPxyiOt/5yMiieiFYLhVwPDNp5DGprhdg9YFA65UBkgbkCASQQ9vhkhOFORkA09klRgLiriSRw8t8bwBjn82AFAFAmB4ZoxEQHjMBn8aw59Mh9CAJOjkh7ZU9GYMi/iAT4YZCFxkTATHgtoxdLwISXdMg0SLA8Tug8ocTLc%2BhCTHUSRgxUBG4CxVRfY9RPAp8CTdhBRkxG4KDsRSstpbjkkgDETLwGwiBLiXcPirCXkqTSRyRG5iTMgngQSaNdjOhPoOl7wpSaS6StRGTIxvBugVouiOSQAuSi8MI64KSmVaI5SBAeR9whprTlp9CVTMxqxhQ34MDl1aIMTUTP9%2B8txJtKUqSVht8YVbRvSdZf8ngKsDAXTKx3Sb9QzHVXAewcj0i7iEJAQXd2Aa58i7RENiBfi9QUSdZR5FT6BwTVh8SbTR4NSZS008A2AeRUgbQnhayKCtSGSB5mT9S2SUxdjpgmg1j6pDilDHdnddRf8Tj/jbQtw8A19OYbhARUAK8DxA4dQ9QCAnRczvjGhn9HACB6BoxZy187J8BRgdD45bQABHLwaIJWKdPk5Q8jOeEMktb8QgVmIRaPZM3KF48jFgAgLgJqJqOIOcW0eGfBVIpQy8csUiL2AMEAEPMwaMLgbQhvFiFC1iTC1iPKCU5dIMqfSMw4VCR/f8Eioiv/AML8pMsMFM6wy3HBLYqWNwMs2EbAW0dAF3JgCABgIQvXRiqo5i1i3YsYeGTciAG8u8qdJlSouPZinYlYgeLJN1MPSS4ge8%2B1DvBixuJi1s%2BTXYpSxaN1AUDUTWOkQOdACS289S6SnDZdWC62XQ2wsKU8dSVcW0THQSBgJ8EWKQgM%2BZTihoMIoI/Qs83CCCu3fC8jSMJoCacjcQ/BQK7Pb8sMCAJKgLc8F5BylQ/Q1yrBDQzy686yjSqLLBXy2Mfy35RoMSy0XvXwn/e8MK6ARgSKjI1i78dob8CKeipvHqhtYPRsNYBANKrivi5hV40U64hAW0Qk8wUiqlZHPNGMjpRwNgRCua6MKSQC6pLS3%2BbK9Q3Kp8AqpeMKNSkqwjMqiIiqgDFcQyzcg%2BF9YKqIgSQaj40Sk6Wq2MII/cM0x1BKkahoMapvTXSDIw/LEU946a2asweaszGeCOcysMaM1UnlVa9gEizatCAgESOWXa2ufapyw6ty46%2B8M65LTBawPASq6VO64y%2BGsy92CFOqoIh1cjEyhGxmuKnBf69Kz2cjdA3m78aqj6p6/0pSaPHm0awAtMqKnBF8ii8i26qsp9X%2BdamGhW7G58DqhWmi9AOio49JCwQcQcIQAaHkAabAU2nka4AIAIHkbAAARS2x3W0vMThgkNptKnprmkZqWEFOlV1ogE9vZoZossVxdrI25vdvwU9tNXDoyP4sbnQNjpfWpuXEYRx35LTJluuCphYCYAkLwqfJwUDjXBIAjTOFoCRRODwHmhJMMXYtLtHmIDXN1FGCdDmtLvLE%2BBhujFLpQqETMGrjsuLs5PLrEGrvtlrpZIECoXYooOIGRBSxbuWjbvErmoXvzp7F7v/HLoHphSHtasTpAEwFUFSBCEEDwAnrCoQjrvlIboqzPopECxXvXPbq2xhtPvPogSvu3uQsftSH3pLUPt/NHpQALAqhvMaAnRrrvtnofpJDwDMAgDimFGHuXtbo3I7phsQZEh3twaAaeBAZHsax4wDDpAZEblOFSEHOiDJAjA9Lzm2g4CWFoE4Dil4D8A4C0FIFpI4HUSuuDhWDWD/C2B4CNU0BYc4JADim0LYY4EkF4BYBkbnGFCkDMHMjijnHUc0e0ekC4Z4b4d4CzG0IaO4ZYdIDgFgCQDQCd2PzIAoE%2BLsfoBiGAAUGYEzAQFkVICwDRnWAADVdVsRBxPcuHxGaBFpogswCFJHSBWxmB1LOBxHbGj5hxaAlZYmsB86jBxBzGfG8BThahgQsw8nT6agp8NhxGbROhYnOQylGglYPAsBYmp7lHuALGqADA3HAnMBgnQmkneB%2BBBARAxB2ApAZBBBFAVB1A8ndB2gDAjAUByr9Ba6sxYBmA1qKHzzgQYhuBzItBfsLjugSmMJLltxTArr4glJBxH8MIAB1fkJST3Tiy%2BviOE0kM4dAYxp2WoZwcJMMCYNoLZMMWYQCPINIDIAQQF8FgoTIUFsoYYKYH57oXocYZkyYDoLoOoMYfoYIQYBFmIKYHF6FolmYPFuYRFpYSMVYdYRYfQdhzh2Jvh//cyBIDCN0cC5AW8Z%2BQOaA0iXAQgEgVCOIbmXgMxg5yxpAMp5AJfBxygaqhQScEIWgIQLx7EMJ3gWx5IOgKshgJVzoVVmeAxzVpEbVlxkANxjxhQLxggUgLV%2BxwcKfQ19V2J6V64YgNxzgXgaV%2BofALhwZ2QEZ8QcZoZ%2BQJQNQWJuZ/QQwYwZZuptZqADZ9gLZ21nZpJ/ZqR3huEzIE5s5gSC5qwSwK5jCG5pSB5zop56sQwRwN5n8FuL55YGlsZ2weuYIfVlVtVjV/tEuAZ0gbEPMAZ1hhl0gY13hzgbAVQcp0EFltljl4ALl0iMYhgPliAARwtmwQMfAadsRhYMVyR37X8JgcMSgIdhRpRkASQMwYUXRnRrRnRpIUdox2wPQcVzNjk1CuRzgOIRlvJp91937HZ9IZwSQIAA):  
```C++  
#include<iostream>  
#include<algorithm>  
#include<vector>  
#include<random>  
#include<new>  
#include<chrono>  
#include<boost/align/aligned_allocator.hpp>  
#include<boost/core/lightweight_test.hpp>  
#include<climits>  
  
int ffs(uint64_t v)//Sean Eron Anderson, Bill Burdick https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightParallel  
{  
    if(v==0) return 0;  
    int c = 64; // c will be the number of zero bits on the right  
    v &= -int64_t(v);  
    // if (v) c--;  
    if (v & 0x00000000FFFFFFFF) c -= 32;  
    if (v & 0x0000FFFF0000FFFF) c -= 16;  
    if (v & 0x00FF00FF00FF00FF) c -= 8;  
    if (v & 0x0F0F0F0F0F0F0F0F) c -= 4;  
    if (v & 0x3333333333333333) c -= 2;  
    if (v & 0x5555555555555555) c -= 1;  
    return c;  
}  
  
int find_last_set(size_t v)  
{  
	return CHAR_BIT*sizeof(size_t)-__builtin_clzl(v);  
}  
// int find_last_set(uint64_t v)//Sean Eron Anderson https://graphics.stanford.edu/~seander/bithacks.html#IntegerLogObvious  
// {  
// 	if(v==0) return 0;  
// 	int r = 1;  
// 	while (v >>= 1)  
// 	    r++;  
// 	return r;  
// }  
size_t eytzinger_one_based_to_inorder_zero_based(size_t tree_element_count, size_t tree_row_count, size_t eytzinger_one_based, size_t eytzinger_one_based_digit_count)  
{  
    if(tree_row_count==0)  
        std::unreachable();  
    size_t result=0;  
    while(tree_row_count!=1)  
    {  
        size_t bottom_row_element_count=tree_element_count-((1zu<<(tree_row_count-1))-1);  
        // size_t left_subtree_element_count=(1zu<<(tree_row_count-2))-1 + std::min(bottom_row_element_count,1zu<<(tree_row_count-2));  
        size_t left_subtree_element_count=(1zu<<(tree_row_count-2))-1 + ((bottom_row_element_count-1)&(1zu<<(tree_row_count-2))?1zu<<(tree_row_count-2):bottom_row_element_count);  
        size_t right_subtree_element_count=tree_element_count-1-left_subtree_element_count;  
        if(eytzinger_one_based==1)  
        {  
            result+=left_subtree_element_count;  
            break;  
        }  
        else if((eytzinger_one_based & (1zu<<(eytzinger_one_based_digit_count-2)))==0)  
        {  
            std::tie(tree_element_count,tree_row_count,eytzinger_one_based,eytzinger_one_based_digit_count)=std::forward_as_tuple(  
                left_subtree_element_count,  
                tree_row_count-1,  
                (eytzinger_one_based&~(1zu<<(eytzinger_one_based_digit_count-1)))|(1zu<<(eytzinger_one_based_digit_count-2)),  
                eytzinger_one_based_digit_count-1);  
        }  
        else if((eytzinger_one_based & (1zu<<(eytzinger_one_based_digit_count-2)))!=0)  
        {  
            result+=left_subtree_element_count+1;  
            // std::tie(tree_element_count,tree_row_count,eytzinger_one_based,eytzinger_one_based_digit_count)=std::forward_as_tuple(  
            //     right_subtree_element_count,  
            //     bottom_row_element_count>1zu<<(tree_row_count-2)?tree_row_count-1:tree_row_count-2,  
            //     eytzinger_one_based&~(1zu<<(eytzinger_one_based_digit_count-1)),  
            //     eytzinger_one_based_digit_count-1);  
            std::tie(tree_element_count,tree_row_count,eytzinger_one_based,eytzinger_one_based_digit_count)=std::forward_as_tuple(  
                right_subtree_element_count,  
                ((bottom_row_element_count-1)&(1zu<<(tree_row_count-2)))?tree_row_count-1:tree_row_count-2,  
                eytzinger_one_based&~(1zu<<(eytzinger_one_based_digit_count-1)),  
                eytzinger_one_based_digit_count-1);  
        }  
        else  
            std::unreachable();  
    }  
    return result;  
  
    // if(tree_row_count==0)  
    //     std::unreachable();  
    // else if(tree_row_count==1)  
    //     return 0;  
    // else if(tree_row_count>=2)  
    // {  
    //     size_t bottom_row_element_count=tree_element_count-((1zu<<(tree_row_count-1))-1);  
    //     size_t left_subtree_element_count=(1zu<<(tree_row_count-2))-1 + std::min(bottom_row_element_count,1zu<<(tree_row_count-2));  
    //     size_t right_subtree_element_count=tree_element_count-1-left_subtree_element_count;  
    //     if(eytzinger_one_based==0)  
    //         std::unreachable();  
    //     else if(eytzinger_one_based==1)  
    //         return left_subtree_element_count;  
    //     else if((eytzinger_one_based & (1zu<<(eytzinger_one_based_digit_count-2)))==0)  
    //     {  
    //         return eytzinger_one_based_to_inorder_zero_based(  
    //             left_subtree_element_count,  
    //             tree_row_count-1,  
    //             (eytzinger_one_based&~(1zu<<(eytzinger_one_based_digit_count-1)))|(1zu<<(eytzinger_one_based_digit_count-2)),  
    //             eytzinger_one_based_digit_count-1);  
    //     }  
    //     else if((eytzinger_one_based & (1zu<<(eytzinger_one_based_digit_count-2)))!=0)  
    //     {  
    //         return left_subtree_element_count+1+eytzinger_one_based_to_inorder_zero_based(  
    //             right_subtree_element_count,  
    //             bottom_row_element_count>1zu<<(tree_row_count-2)?tree_row_count-1:tree_row_count-2,  
    //             eytzinger_one_based&~(1zu<<(eytzinger_one_based_digit_count-1)),  
    //             eytzinger_one_based_digit_count-1);  
    //     }  
    //     else  
    //         std::unreachable();  
    // }  
    // else  
    //     std::unreachable();  
}  
  
template<typename T>  
struct eytzinger {  
    eytzinger(const std::vector<T>& a) :  
        a_(a), b_(a.size() + 1)  
        // , idx_(a.size() + 1)  
    {  
        init();  
        // idx_[0] = a.size();  
    }  
  
    auto operator()(auto const& x) const {  
        size_t k = 1;  
        while (k <= a_.size()) {  
            k = 2 * k + (b_[k] < x);  
        }  
        k >>= ffs(~k);  
        if(k==0)  
            return a_.end();  
        else  
            return a_.begin()+eytzinger_one_based_to_inorder_zero_based(a_.size(),find_last_set(a_.size()),k,find_last_set(k));  
        // return a_.begin()+idx_[k];  
    }  
  
    size_t init(size_t i = 0, size_t k = 1) {  
        if (k <= a_.size()) {  
            i = init(i, 2 * k);  
            // idx_[k] = i;  
            b_[k] = a_[i++];  
            i = init(i, 2 * k + 1);  
        }  
        return i;  
    }  
  
    const std::vector<T>& a_;  
    std::vector<T, boost::alignment::aligned_allocator<T, std::hardware_constructive_interference_size>> b_;  
    // std::vector<size_t> idx_;  
};  
  
class stopwatch {  
public:  
    stopwatch(const char* message, double* duration = nullptr) :   
        message_{message},  
        duration_{duration},  
        start_{std::chrono::high_resolution_clock::now()} {  
    }  
      
    ~stopwatch() {  
        std::chrono::duration<double> elapsed = std::chrono::high_resolution_clock::now() - start_;  
        if (duration_) *duration_ = elapsed.count();  
        if (message_[0])  
            std::cout << message_ << elapsed.count() << std::endl;  
    }  
  
private:  
    const char* message_;  
    double* duration_;  
    std::chrono::time_point<std::chrono::high_resolution_clock> start_;  
};  
  
template <class Dist>  
void run_tests(const char* title, Dist& dist, size_t query_count) {  
    std::cout << title << std::endl;  
  
    std::mt19937 gen(1);  
  
    for (size_t n : {2, 10, 100, 1000, 10000}) {  
        std::cout << "  n " << n << std::endl;  
        std::vector<double> data(n);  
        std::vector<double> targets(query_count);  
        std::vector<size_t> results_eytzinger(query_count);  
        std::vector<size_t> results_lower_bound(query_count);  
  
        for (size_t i = 0; i != n; ++i)  
            data[i] = dist(gen);  
  
        std::sort(std::begin(data), std::end(data));  
  
        for (size_t i = 0; i != query_count; ++i)  
            targets[i] = dist(gen);  
  
        double t1, t2;  
        {  
            eytzinger search(data);  
            stopwatch _{"    eytzinger   elapsed time: ", &t1};  
            for (size_t i = 0; i != query_count; ++i)  
                results_eytzinger[i]=search(targets[i]) - std::begin(data);  
        }  
        {  
            stopwatch _{"    lower_bound elapsed time: ", &t2};  
            for (size_t i = 0; i != query_count; ++i)  
                results_lower_bound[i]=std::lower_bound(std::begin(data), std::end(data), targets[i]) - std::begin(data);  
        }  
  
        std::cout << "    ratio                   : " << t2 / t1 << std::endl;  
  
        BOOST_TEST_ALL_EQ(  
            std::begin(results_lower_bound),   
            std::end(results_lower_bound),  
            std::begin(results_eytzinger),  
            std::end(results_eytzinger)  
        );     
    }  
}  
  
int main() {  
    std::uniform_real_distribution<> uni;  
    run_tests("uniform", uni, 1<<20);  
  
    std::normal_distribution<> norm;  
    run_tests("normal", norm, 1<<20);  
  
    std::exponential_distribution<> expo;  
    run_tests("exponential", expo, 1<<20);  
  
    std::chi_squared_distribution<> chi2(5.0);  
    run_tests("chi2", chi2, 1<<20);  
  
    return boost::report_errors();  
}  
```

---

## Comment 6

> Username: HDembinski  
> Created at: 2022-12-12 08:42:58 UTC  
> Updated at: 2022-12-12 08:43:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1346098066  

Thanks for the additional research on this. Please try use C++14 when you write the benchmarks, because we need to support C++14 in Boost.Histogram. I think we can do without prefetch in general and try to figure out a way to enable it when it is available on the target platform. It shouldn't make a big difference, because the sorted array is likely going to fit into the L1 cache in this applications, so there is no prefetching happening. I think the alignas trick goes into a similar direction and may not be needed here, although using Boost.Align is fine, in principle, too.  
  
The main issue seems to be ffs. If we do not use `__builtin_ffs`, the performance drops too much to generally replace lower_bound for all n. Am I reading this correctly?

---

## Comment 7

> Username: jhcarl0814  
> Created at: 2022-12-12 10:59:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1346272253  

@HDembinski Thank you for the advice and addressing the prefetch and alignment issues.  
  
# find first set (problem: compiler does not replace software based implementation with instruction(s) the intrinsic could get)  
Sorry I was so stupid that I did not find a fast ffs. I just found out that ffs is already inside boost (`boost::core::countr_zero` and `boost::multiprecision::lsb`) and std (c++20 `std::countr_zero`). They have almost the same speed as `__builtin_ffs`, so we don't need to worry about compiler not identifying and replacing the code.  
[`boost::core::countr_zero(v)+1` and `boost::multiprecision::lsb(v)+1` are as fast as `__builtin_ffs(v)` (x86-64 gcc (trunk), -std=c++14 -O2 -Wall -pedantic -pthread)](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGISdKuADJ4DJgAcj4ARpjEIGaSAOykAA6oCoRODB7evv7SaRmOAiFhkSwxcQnJdpgOWUIETMQEOT5%2BAbaY9sUMjc0EpRHRsfFJtk0tbXmdCpODocMVo9UAlLaoXsTI7BzmAMyhyN5YANQm%2B25RhAoA9HPoppbWAHQIF9gmGgCCB0cnmHOlyiqHSBFuaGImFutDwwAQBAA7pg4QiAPoETBzN4pFIfL6/MyHBjHLxnC5uJxzKGsfE/P4kgEUkFgiEkaHXAg4vH7T70on/MmYZmgua3FheWiOFJQ5B4DICW6hTHAWLcum/H7K05UKgKDQQLzKgBskgxpwAbqsCSZElYfqdHacoQQtgxTmi0VEjVLQmjdQoIFaLvbfokACIE7UBriGk1mgiW1a3W5CTCGU7YYgCU7fBhYYgKASkU4AKTwLFOOUwJdLTCL7rcXgYDFC8NYpwRBBSChAKeAxCYKQQeGQChec0M/GI6BemHQXluAD8lIYC7dOQgmMgANbjhEsWgHABasXSAHkGAAlVELMLNG12glO07a0z7UMvvBUU5B60Op1bU/F8nQtIFw1/MCTAAVmwSDTgAWlOLhVlWIFPl5ZCQ0dFNTjTRMLTADgFFOAhBzoNtTg0EiiGQki11OAAvM9nSxAhnxAx1p1/ZBwKwj9LWwp4rEsf9vk4x0gI4iTQPQ/F9ggrgQ2kkDbUjADJIjFTuiUFSpI0kDeIuCCNGUgy1JUl03VOd9Qws%2BktUEHU9TMONBFNc0rRTNMMyzHM8wLBsSwsOhaFOCwtnwXdOwIbte37Qdh1HcdJwYadZ3nRcV3TfNYg3Qgt13fcCEPE8zwUS8b3hAhlGaMR6CPeknwM78g2M4yNDQqziHdUyPxUt8%2BNNbDcN4xFQtOGJSIQQEGBGYhTlQH9mOzSabkW90CBm51bxUqCzGNYzEPjDE/zM8SnVw79ILQ5AEIQ86vx/INzgOqjVA0T6vs%2BgAxP7/r%2B27EKO/YzEep1rpe8xjXe76Abh/6gYehTkMO/qWue/aYY0D6ND%2B36foJgmkaOgAOcHHUhrHYcJ2m8fpumfpJlHJAp19Mde7HVH2Hneb5/n9mZiCwfRi7KY56H3ug6WZdluXoKF/jgMdbr3Vsx91M1b5oz1fY3IIDyCOTVMcszbN3QC2IgtOABZJhYtOAB1EcCFoVBdxLAKoURPCtxSPBBxiuK%2B1uAchxHMcJyaNKSAyhdl1XXLiHyrbtz3N4SqPIlT2zCrr1vEK%2BnTbZ3ia5X2bahSOq6zBXR6qi2bfbDTlG05xtoMKpq22b5sW5aWM5Eic27nbqtIFSZNwot2ctV8SK4LgNEXz7fyietATMdZpsYGy24mqb9hU3DwnPAAVbAQBnjRwKOi0S27tW%2BP2LgXgGiW3pxlDznM5qxZAqeUh1DwGIGy68dQkEWugdAs8ID1gUD4ecpFUCdiHIA90W5aA/iWtvUiFZMBiRkkZFGSlRYvnsn/HSwof7lxfEQ4WbMnq/ihh/VQuo2FsLQu1FGnVv5/zIXaSSBkZJQV5PJCCoNm4TwkkZZ4KMJGkIkuQmSkNmFc3YZwqu3DOFCMAgI3hMkXwiIwhhRSaMLCCL4dI16VhiFmKkZpTWyjnqqPerqDRkYtH6MUXo%2BxIEjFiNOOTASXiDG72sGTBhujHESRUdTHGVB3FHR4b4oCFjQmOn8aIlmkTOIyJsRBVmCjOJKJic4uJ3NEmeJST4nRnFMkmNOCLGhVjwkoyadU6JuTgYo3KSQ8uJSVa12surJqmsoxOQDJIfWhtJpRGNnbFooRThBBMG4Q%2B3xJBeExAtCAXAACcezEhbzcFuYg9ASLYBeMslEShCzFlOAACTqrQaByhsw7lQCkEsABpFEW1HlXKvGueqQcewh0RBCl4yAZoKAUDKVAYcWAsDbC8EgwBbghWEKIBgBxHYFTRPKTw9t5xoiCEILgdlf5PQgFEKIXDww8NVg3IpNkBBzEwKoGUr4nKhCwKoYa0ELACogkdfSljHQaBLMhEskhSZSrMCWaCyRTiSD2SWMwcrHT7HHrU04xouCKs1dBSVKqFWnH2JqswarkLKpVTq8VeqzXS0Vda/YxpFXatONBA1KrPVmAVb4yQ0ESz7FdZ6/YJqEgli4JqrgZqvX2pksaT14wvXusacq40JrJA%2Bv2Mqrg7rfHQWkOa4NXqzVmB9Z0c1FbPUL0TRJSQ6aDoypNVwMt%2BwS1mBNc/aNkrfFmDLVIaN1rTjWq4J6wJUrbWHR/h48uaAGDss5QtI07kEynCwFEYgRptAMFNHxHGGh9hUESHs9AiQuBREkMgKIGgmCkz2WzJlvKOUCosFAWlnNEK0rQgAKk3Zgbdu792SA0cYr15NoKRksH0jWNpHKJgDNBaZG7f0pm%2BRWJ2Yh5TQt/N2uNaEuxgpTBCxEUKYVwuzIi5FRhUXEHRZikQhhcX4qUCkOqmJ0CkvJZS8urVaX0sZUM%2BufUF1ssxCu7liZX38tZoK4VfExUyWzban10F02yvVbas1WaG2cSVTK61OaQ3Ko1dG9N5qSz6v0yBYthqQ1lugk67NFaq3SF8RakNFbrVEmjfmn1drHW2ZfE2xVZaW1euMz29N7aPUhadJ2xVPru0yvDSls1i8ZXBoHR28dJaeYjpDSasd6qPO6ondGzVU7kLxpnVKstybbMWRZYu5dXK10Gw3VundeA90HqSR9E9Z6L1XpvXeh9T6WUvtynJmCH6aVRHOLBVeUQEIoX/YB4DfXQPgY%2BJBmCMGLBwdGQh7WEy9TGlQ55Y2ihKJnAsL1vdeFMAAEcvCMB2DRZBABJWbyFuWnCYFWVALAUhbNiE7WOk0ACeVZTnnMzFcoINyrb3KeYOF5pw3moA%2BVQEsDFfkFQBacIF%2BYQXEfiqHRKEcUrR3SnOeO2U1x5U3GnYqpVs7lUqreG2GAgigh3F4PEZc36Vw8QymudderPpE%2B6FkcwQAoHZErtAzYyJohWqgM6sHzrkPGUhvUiRrtG0fPxqg4vq6sWl8y8uTKFcECVxKX0MogEKj8CAWgCgog6%2BO3rrSDlvjHDgacOYnzET2zw2K8HURYTIBACpMPKQI8EGhRANriZoXNAA2wWFTBVQlnQJsWPmAAMLkHL0Pic0O4pDImhS%2Bvjc8KHz5gNEQEm8t7Uglx05f7ZZDb3aXvvQu%2B%2BMnC0AfFgHiq4QObVASuRzwjRFCIs3hehomOO7HcSuGCoERBAa0EZzhUqiXpAyK4iDJ8jwgffR/mmOinygGfAg58gCH1kCkRevAl/290IcShoFHQP7Qqz7z6ohL5YieBbL94b67jb67435IRj4EBt4sqMIQBv4CBoj/oYEMBoh8S/49jzhQqbCCD745IzwQAd6qht6CqmTQYELpJAGbCJgUgUinBUGt5AhuBsEEH/7EHq436sGXCh4EDoBK6uBHgsr64/Ayh4AWjEoJ4GQZ42SnI55YjN7UFsyf4l5l5bB96YFsxAFP475K6OBsBohpDKgUhGEgEgAL4IDgEr5QGYEwE7j7ZIEoGUrzpnYZ4cpcoZDMTmjvaxAw7r4kHsQKS7JCFuBEjWiiydYzKTCqgEAKDzbBHEChFq6CCHbnRXROTL6SgpH%2Bh6h0EWDpGZHhE5Gix5GJgFFSgKDFEKBKSCrlFhHq5VGhg1GsTwL1GNFgwtEfYZFtHZHQa5G3DSbdGFENEBiHwDEhHDHsSjHVHjHah1FFGTJpGDEVHtFLGdErH5FYhTGNHQSbHzFZGLHeE/BdFrHTGXanFDHnEdEEjXGHG9EBiJD3HbEjGXHna1GvHIEBgmprGNE%2BrAkBhmpgm6wliQkKAlowllownpowkfGiwEgWioB4DQI7q4GYhzCBicLH736iFK7NjfgkAsAEqCBoj4DUh4DejD6XAJEJj7Y0kEAQAP5zRsDECjhoiwjIopEUhMkYgfBO6hD74lgckILcnIC8kVg3CCknQREXwgAsBMCqD75xHlwP4sAED7J7J5qnCqgMC7KakqTcTsl4CBEyaHrYR4CnBgBgBHStGPECThKWB4AMGcRJG1ypGCp4CHZ8SsnQCMCmmB4vjaH0CkQmq6n3xmoECeoEAloEBloGz3won9KEkgRJ4p54YT7mBmCAmnC8GIJmHsCvRmrQwECmRaSoFOjmkBGt7WlJK2n2mOkozOmVGunPDumekGK4Q3GNGlH%2BlLHhiAkQDekCl%2BmHahkOovgwmmSyKjklHjnNDJG%2BkhTTlswDK36j4X45kIAehST%2BoxhFkGCEHQKlkN7%2BoliVlKQ1l37gI7INnmh2nNkCR2kOlOlbELHYRukhS9mTzjEDkxjzbDkS4xgrkMY%2BmgWbm1mcQwlKSLkQUTnrlgUzn8KdI7m6rZlX6HlPjHkuSnl/4ll4JXkVkHQEBgz3m%2BL1mWmNmvg2nvmtlflnGdnmJ/kem%2BIvj9n/G3EKD9EbkjngmQVrkwX0HkFzn/F9GtJLn8UiXQVTniVSEB5/zKYSQ4Wp4Hl5kEUKD7BEXnm4JsBkU3kUWHzUW6q0VWkMVvnmIfltkQQdntFdkiT/lcWXRAW8WNGzGCXgW6zyWTk%2BXoXpIwmHxIV%2BUoViXhhBUOJ6SZkvgaW5lHkFl6iSD6X/6GVln5kmXGiJktYPmWX0WvncItmfntnfkunsXdmuW6rcUeU9HrEpWRXGSTL%2BWoWwUPmSX1WNGsxhWwmtWRXRVH6YVqWcQJVaVJXIZpUkVGXlnZXJl5U0UQIWlWVFUmQlX2WnCOXZHOXWCcU1XuWTFvF6gnGKW%2BUKAoYRWnWDUyTwkyXIb9VXVbkqX8IPljV4UiTJUKAwzFkXmkWzWcwGwLUWVLXPlNnFVMWlUOXlVsXWK7UAUSQ8X1V8WHSnXNWXYPWBUSVOiIl3Xo2XWY3KXDVxVOhvXaWfWJBTW/UzVZUA0fHmUOoFUvmMW2XMVlWsVOWVUuV7WzkHXAVG5NUKTvEY1oVY2DJdXvG40KDG740i2E1na1XhTnjnhCCnxojnwq1ojfBBBBBojYAACKEAR84x8VxJIAMQwAYpfNTRW8RtWZptrgEAVtKEtmuEJtYhZtmAFtxpVtm8LtxtJN9t%2BYjtnl4JvZuEg1uEFgStGt6tqtWtOt%2BthtBkrtAd7t5tltIdeoztISLc/tRJ7tDtTt6wttbtSu6d3tmdulxdydedIhBdQdVtgsttEd4xUdytqtsdmt2tutBtJdqdZdntGdSNIJNtNddt9d6Awdw9MY1df8Kd%2BdA9XtU9Rxkys9CtpdIAhdldYGzdbMkd0dHd2AGt8dPdSdc9tdD%2B5dy9R11tN5Y9G9W909Wda9B1G9V9VtCsft494hDdldCsu9LKbdMdR9qtid85pACFg1QDh9GtYDUlgJED8DLkUDB9atIDp94DIVKD7daDsDBtmDSDsJ2DwDeD19AJJRiD4tx1xDMDoD%2BDhDkqiJNDuDdDZDg5lDhRjRRy/uYyiG7BTAYpYkI1zozYGIbE%2BJeuBk9uoojuIAUIaQ4%2BsQ2YhYZBqJKlHA6wtAnA0EvAfgHAWgpAc%2BHA3BVVoemw2wgIBwPApAju%2BjGj6wW%2BpoLwGg3apMpMiQiQGqQaey0E2qWjHAkgvALAEgn0pAejBjRjvAvYkqtjWg6wcAsASAHKdQWyJA5AlAq5CgtUYQtAQgCAu%2Bej1jaAYOdA%2BhDA2T3QeTBTmgvAxT/s9AcQwACgzAPY%2BTBApAdTdAsQ54WyVTiIhTvAyTyA3wDGvYHAgzqgdQjQ%2BAejvA/AggzG4gw68z8gSgagNTpAugBqBgRgKAf5%2BgdJvYsAzARlDupAFoow3ApMcT6wnyvQYzCEDwxkwk1gUgiE54ZgiEjsIKCEgC6AhgjgvEvzW0NI6AUT5jOwegDwoQFTuT%2BT/TnA1jZEmAuw1jiIiUiLGj%2Bg2jujGzRj2AkzyAqTC0qgpMxoCEB6wAyAvE452JO4aEEAJjLlJYuAhAECVjqwvAsT9jpAM0TABYlADjIA%2Bw0ELwhyeyC8/ql6xaGg4w/jgTYTeLnAUTIAMTNTqE2LHAZguLdjhjyrNj6r6wFzhYWQ/gQAA)  
```C++  
#include <bits/stdc++.h>  
#include <boost/core/lightweight_test.hpp>  
#include <iostream>  
#include<boost/core/bit.hpp>  
#include<boost/multiprecision/integer.hpp>  
  
int ffs0(uint64_t v)  
{  
    return __builtin_ffs(v);  
}  
int ffs1(uint64_t v)//Sean Eron Anderson, Jim Cole, Jason Cunningham https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightLinear  
{  
    int c;  
    if (v)  
    {  
        v = (v ^ (v - 1)) >> 1;  // Set v's trailing 0s to 1s and zero rest  
        for (c = 1; v; c++)  
        {  
            v >>= 1;  
        }  
    }  
    else  
    {  
        c = 0;  
    }  
    return c;  
}  
  
int ffs2(uint64_t v)//Sean Eron Anderson, Bill Burdick https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightParallel  
{  
    if(v==0) return 0;  
    int c = 64; // c will be the number of zero bits on the right  
    v &= -int64_t(v);  
    // if (v) c--;  
    if (v & 0x00000000FFFFFFFF) c -= 32;  
    if (v & 0x0000FFFF0000FFFF) c -= 16;  
    if (v & 0x00FF00FF00FF00FF) c -= 8;  
    if (v & 0x0F0F0F0F0F0F0F0F) c -= 4;  
    if (v & 0x3333333333333333) c -= 2;  
    if (v & 0x5555555555555555) c -= 1;  
    return c;  
}  
  
int ffs3(uint64_t v)//Sean Eron Anderson, Matt Whitlock, Andrew Shapira https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightBinSearch  
{  
    if(v==0) return 0;  
    int c;  // c will be the number of zero bits on the right,  
            // so if v is 1101000 (base 2), then c will be 3  
    // NOTE: if 0 == v, then c = 31.  
    if (v & 0x1)   
    {  
        // special case for odd v (assumed to happen half of the time)  
        c = 1;  
    }  
    else  
    {  
        c = 2;  
        if ((v & 0xffffffff) == 0)   
        {    
            v >>= 32;    
            c += 32;  
        }  
        if ((v & 0xffff) == 0)   
        {    
            v >>= 16;    
            c += 16;  
        }  
        if ((v & 0xff) == 0)   
        {    
            v >>= 8;    
            c += 8;  
        }  
        if ((v & 0xf) == 0)   
        {    
            v >>= 4;  
            c += 4;  
        }  
        if ((v & 0x3) == 0)   
        {    
            v >>= 2;  
            c += 2;  
        }  
        c -= v & 0x1;  
    }  
    return c;  
}  
  
int ffs4(uint64_t bb)//Martin Läuter (1997), Charles E. Leiserson, Harald Prokop, Keith H. Randall https://www.chessprogramming.org/BitScan#With_isolated_LS1B  
{  
    if(bb==0) return 0;  
    constexpr int index64[64] = {  
        0,  1, 48,  2, 57, 49, 28,  3,  
        61, 58, 50, 42, 38, 29, 17,  4,  
        62, 55, 59, 36, 53, 51, 43, 22,  
        45, 39, 33, 30, 24, 18, 12,  5,  
        63, 47, 56, 27, 60, 41, 37, 16,  
        54, 35, 52, 21, 44, 32, 23, 11,  
        46, 26, 40, 15, 34, 20, 31, 10,  
        25, 14, 19,  9, 13,  8,  7,  6  
    };  
    constexpr uint64_t debruijn64 = 0x03f79d71b4cb0a89;  
    return index64[((bb & -bb) * debruijn64) >> 58]+1;  
}  
  
int ffs5(uint64_t bb)//Kim Walisch (2012) https://www.chessprogramming.org/BitScan#With_separated_LS1B  
{  
    if(bb==0) return 0;  
    constexpr int index64[64] = {  
        0, 47,  1, 56, 48, 27,  2, 60,  
        57, 49, 41, 37, 28, 16,  3, 61,  
        54, 58, 35, 52, 50, 42, 21, 44,  
        38, 32, 29, 23, 17, 11,  4, 62,  
        46, 55, 26, 59, 40, 36, 15, 53,  
        34, 51, 20, 43, 31, 22, 10, 45,  
        25, 39, 14, 33, 19, 30,  9, 24,  
        13, 18,  8, 12,  7,  6,  5, 63,  
    };  
    constexpr uint64_t debruijn64 = 0x03f79d71b4cb0a89;  
    return index64[((bb ^ (bb-1)) * debruijn64) >> 58]+1;  
}  
  
int ffs6(uint64_t v)//Using de Bruijn Sequences to Index 1 in a Computer Word by Charles E. Leiserson, Harald Prokof, and Keith H. Randall https://graphics.stanford.edu/~seander/bithacks.html#ZerosOnRightModLookup  
{  
    if(v==0) return 0;  
    return boost::core::countr_zero(v)+1;  
}  
  
int ffs7(uint64_t v)  
{  
    if(v==0) return 0;  
    return boost::multiprecision::lsb(v)+1;  
}  
  
class stopwatch {  
public:  
    stopwatch(const char* message, double* duration = nullptr) :   
        message_{message},  
        duration_{duration},  
        start_{std::chrono::high_resolution_clock::now()} {  
    }  
      
    ~stopwatch() {  
        std::chrono::duration<double> elapsed = std::chrono::high_resolution_clock::now() - start_;  
        if (duration_) *duration_ = elapsed.count();  
        if (message_[0])  
            std::cout << message_ << elapsed.count() << std::endl;  
    }  
  
private:  
    const char* message_;  
    double* duration_;  
    std::chrono::time_point<std::chrono::high_resolution_clock> start_;  
};  
  
constexpr size_t query_count=(1<<23);  
uint64_t targets[query_count];  
// int results_ffs0[query_count];  
// int results_ffs1[query_count];  
// int results_ffs2[query_count];  
// int results_ffs3[query_count];  
// int results_ffs4[query_count];  
// int results_ffs5[query_count];  
// int results_ffs6[query_count];  
// int results_ffs7[query_count];  
int result_ffs0, result_ffs1, result_ffs2, result_ffs3, result_ffs4, result_ffs5, result_ffs6, result_ffs7;  
  
void run_tests() {  
    std::uniform_int_distribution<uint64_t> dist(std::numeric_limits<uint64_t>::min(), std::numeric_limits<uint64_t>::max());  
    std::mt19937 gen(1);  
    for (size_t i = 0; i != query_count; ++i)  
        targets[i] = dist(gen);  
  
    double t0, t1, t2, t3, t4, t5, t6, t7;  
    {  
        stopwatch _{"ffs0 elapsed time: ", &t0};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs0[i]=ffs0(targets[i]);  
            result_ffs0+=ffs0(targets[i]);  
    }  
    {  
        stopwatch _{"ffs1 elapsed time: ", &t1};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs1[i]=ffs1(targets[i]);  
            result_ffs1+=ffs1(targets[i]);  
    }  
    {  
        stopwatch _{"ffs2 elapsed time: ", &t2};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs2[i]=ffs2(targets[i]);  
            result_ffs2+=ffs2(targets[i]);  
    }  
    {  
        stopwatch _{"ffs3 elapsed time: ", &t3};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs3[i]=ffs3(targets[i]);  
            result_ffs3+=ffs3(targets[i]);  
    }  
    {  
        stopwatch _{"ffs4 elapsed time: ", &t4};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs4[i]=ffs4(targets[i]);  
            result_ffs4+=ffs4(targets[i]);  
    }  
    {  
        stopwatch _{"ffs5 elapsed time: ", &t5};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs5[i]=ffs5(targets[i]);  
            result_ffs5+=ffs5(targets[i]);  
    }  
    {  
        stopwatch _{"ffs6 elapsed time: ", &t6};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs6[i]=ffs6(targets[i]);  
            result_ffs6+=ffs6(targets[i]);  
    }  
    {  
        stopwatch _{"ffs7 elapsed time: ", &t7};  
        for (size_t i = 0; i != query_count; ++i)  
            // results_ffs7[i]=ffs7(targets[i]);  
            result_ffs7+=ffs7(targets[i]);  
    }  
  
    // BOOST_TEST_ALL_EQ(  
    //     std::begin(results_ffs1),   
    //     std::end(results_ffs1),  
    //     std::begin(results_ffs2),  
    //     std::end(results_ffs2)  
    // );  
    // BOOST_TEST_ALL_EQ(  
    //     std::begin(results_ffs1),   
    //     std::end(results_ffs1),  
    //     std::begin(results_ffs3),  
    //     std::end(results_ffs3)  
    // );  
    // BOOST_TEST_ALL_EQ(  
    //     std::begin(results_ffs1),   
    //     std::end(results_ffs1),  
    //     std::begin(results_ffs4),  
    //     std::end(results_ffs4)  
    // );  
    // BOOST_TEST_ALL_EQ(  
    //     std::begin(results_ffs1),   
    //     std::end(results_ffs1),  
    //     std::begin(results_ffs5),  
    //     std::end(results_ffs5)  
    // );  
    BOOST_TEST_EQ(result_ffs0,result_ffs1);  
    BOOST_TEST_EQ(result_ffs0,result_ffs2);  
    BOOST_TEST_EQ(result_ffs0,result_ffs3);  
    BOOST_TEST_EQ(result_ffs0,result_ffs4);  
    BOOST_TEST_EQ(result_ffs0,result_ffs5);  
    BOOST_TEST_EQ(result_ffs0,result_ffs6);  
    BOOST_TEST_EQ(result_ffs0,result_ffs7);  
}  
  
int main()  
{  
    run_tests();  
  
    return boost::report_errors();  
}  
```  
  
# convert from breadth-index to inorder-index (in a complete binary tree) (problem: Is it possible to remove the lookup table/compute the index on the fly, while still be faster than `std::lower_bound`?)  
The lookup table method is already faster than `std::lower_bound` `for (size_t n : {2, 10, 100, 1000, 10000})`: [using a lookup table (x86-64 gcc (trunk), -std=c++14 -O2 -Wall -pedantic -pthread)](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCBmAMwAHKQADqgKhE4MHt6%2BeqnpjgJBIeEsUTHxSXaYDplCBEzEBNk%2Bfly2mPaFDPWNBMVhkdGxibYNTS257Qrj/cGDZcOVAJS2qF7EyOwc5nHByN5YJnFuTjPEmKzH2CYaAIK7%2B4eYx25iwCSECCzXtw/xTy8RxOADcakRiL97o8GAcgS8TsRDOhUD84jdoQDYc9XiEAO5Q/57bHw17IBDEASoQkwuHAtwRVBpAgAejEeGADDZtA5IXQAH0xLRUKIIQA6BDJZI0rF0hEMpkzFloC4snnABAEPGYDma/kETAzCVSmXEuVknksQgKU2A%2BkELzJeg0zFmnEnRnM5UkTAsiKEY3S9F/WnuhVelheWiOZIXZB4dICFnBA3AaKBl13NAMGaYVSxgDUM3QIBA6QAXph9QXUMlokwIQXzGZmwX%2BeWvAWIAWvDneZh0AXhUYhwJgAWGAXliYAOwWAsXB3EScMY7z2cAERD9xZLILKYLVCoCggXhTADZJNWQdOd3vZ1Y7wXn8%2B8FQICDjlu4huNMsF5gS6Thoa5/LuL7PouGyTp6Myliq7AoOsgjEO20SoB%2B06WFwoFPpufwHkeJ5noIl7XreDxzn8EFvh%2BX5fn%2BAFAQWIFxI%2BdwQVBy4FrBBClpG0Z4LGNQJpkpa0AoESYdYOFsSGM5btC9yEcEAoGDM/JKAQEAVlWBAFje8nsbcACcXGTm4AASdwAEr8hYACSAAqABUumoO%2Bun6ssAC0/L8hEZ6CQw/IHOWtDSXJ0IKWBe4qQwalMBpWmnheV76Teu5CJck7YJSk53Al0QKAIBaagQyQKCAu7AEiyQIHgyAKGKMyGPwxDoGKA5eCyAB%2BSjItEfpfEwyAANbNZqLC0LsDmCJgabEAEqDAAA8hEIJOF4Np4VReEaCZtGfj%2BDH/uZLG4Xc4GmQexBNj%2BBayex10HXiDX0F2IL3TcwYPVwFEvSZnHWCDUVXfeB3nZCYPXTF9xefpmAAJ4EOWwSLfyAhVhESUDvqqD8sEJBYKhlaUgFuPoDpeCVtWBAXFWnSYGwgihchBCkEWNN6QW9OYFWlJ4mzvYc1ztOIyjaNGNEmMhBTSjoJzCMFsjqPozLWPy3j%2BDAIQwuCBRD7US%2BtF8wLqBC2gIv0T%2Bf7GxBz5JUoTTUGISjTmDEHKxcChRgQDGXRBb10JgEBm/ygv6wQYBgF%2B/3202e0cQ7z7K4yBBECwEcW/yTMswQUdfuHeeMAXVuCD5UBcO2XivK8YcM9nlvsz5/2%2BfHnspwW4HK/QVAF77ETF/Q%2BeFz%2BEDVx2dcnA3/NN1HPlmMs7dNpYRYECWIBWgwEDp5n88l6z5cc5PtcnPX4eR8fi/L4HXe95g/eaV4Q%2BN4fZfs1%2BE819Pbiz%2BbzcRY3xXtYLsu9UAZ1RAfEepcF7xzMOeb%2BU9z4z0vjna%2BS8PYADFT6/3/vPDBywQB7ygZHd%2BUcPbsS7mLHmxBdQDxfsPZmsDj5FzfjAo%2BLcuA%2BT7gw1%2Bc9yGsM7inWiqspYY01jjBWNsNzx3uNQo28jqEvh9n7UGG5eHP34YzDhH9rbCOUREC4TAxp3xTvhJRKdOhKH3O%2BCAYj1aoUkZTVe54uy4JQX/Bx0snFyykdrDketCG3xOrbCiCik7KJfMWUsjhQ5MNHsfUgaDAGCFIN4iRfjKbpMlo42W2NKb8h1kE9mHsNwxJAO1PEjQBRJX1I6egEAE5ROfJoweCSWHs1IM0lpKS4HdMsS0589jck%2BPyVrdA5hzy9SQWfNw9cMkayyQrIpgS9EVzbtOGcf8PHzJnos3xBSVnFPWQQYBAzk5DOfAc8Z/iBQnLgZQnpz4LGXIdtYzAtioA3OcQrVx7if6eJGWrMZvyAm61OcA5YMdTrPMTlQlpqjozqLaYw9hzDOHW2wmY5RPcN6xLwPE9FiSul9KST85ZA4ckgsyUc8FJSRZlIqVUmpgoFD1KdKHOFEFwKcXoVojpmK0ncpfLyl8JCs5kN0WPbAuyL6Nyvi3JexwsFkq4SANVQCzAXKGWK65ozaUTKmTMuV%2ByDVLLpfctZjzVgiufHqlW5rDkTNWRCx5OLqEVLifgwRpKFXoK6RSy11LxEWpdQ84%2BTL8WVJINUjqbKOWNLtQ7OhGo%2BGCtOTqq5wzwGQMlTnX1QD4GINNX/TVFdMHYPLWcrgGr/WpLOdq5NEEg1GoQSawFeyvFOtuYUiNXDl5Zuza2u5rqGUbKeYMiCrzqEfOTU7aI2kqBu0wJOt5M6VGAWggBX2yKwYJ3AqbetY9vy/nCaKvcEEF0u2XRJVdOLwIfK%2BdWmRci3kOvOqxBF9q9xPqPXPRV%2BifobmVYM2G37u6XuidzasEroEYtOWwgR0rr5V07fKgDAai3L1bmunlUHU4wf0qi7RucUOf3HqW/BgGK04a4KvecFTt65v3lKhDUdSBUercAh9BGaHVlTXqdpxLOn6I3BmuBPDH7ppE0K/2BiHWiJ7WCyZoSz0Hr41ehQzsl0rrwxe95d6vkjspq%2B89P7lHnRIxJoREGHV/rsSZv5UyAXIK7cC0NzrR39q1cvMpsKwN8cUe%2BzTm7mJObxkQQmDBiYyzJgTO5TTAtXOs7JzNGmrncfaBloZHm8kqeNbMvBEWrVuuvps2cOz0NmppWG7z1rglDsg1ckrY7IUdzs0FuGIXDM2Nonl0FlLBwuaK0C1rPnaPLxhWEnLL5gv4cs1u7iqXkPsdYdiywrWotEw6nF9CEyks9aGYJmTq2SUiyaw66hcG2PncENcLjx7CEqqy3WzDDbF6XdC1Y5TQ3CtUfGw1gdtrktDMB2VgdvGHYbosy2u9s2U7Xt03e/TFmYfNbnaDl8SPXYo8uq8v4BoWBOgbPKAgSM6zMDYAWJyhJzheAcI62rd15svgORAbMMx16bzBA4EgrxafBgQQWJg/4QBwqYPyCAovOYRCl0wFq3MID/lAW%2B6hnM8DoFUPLxXlZlcMceuZ%2BFcLgiEGVx6k2Wv%2BQmAAKwWBAjbjc90ncK90ubgxBPBlMC8EQGsdYkQQmVxAJyBZOf%2B2F6of8YfjdTug%2BLAsY1nePQt8%2BYOH0ICJ%2Bni7/kuvQ7Lxj286hmeHpmALC5BPBvd7W7t6Yx3903AFkjynxOilC8vmLz9YDh5jwQF6mNVHDtD3vlMWpu2sfYdhe3ZLrqCV3cQYW5jtvE/IJLcnNPqIusd5YQsFtgmO2SZoXJol6fbvVg0ASvydSA9ALS5z6f1YY0%2BCqUv0la/2l%2B8D84qvkXOeN/BHN5YJrtrrbhYLXt%2BBBp7m8srKbtpNAUnhoErERhXl%2BIbgXtQm%2BF2MXnstnrnsrirpEi0ngEnjARAHgJzKXuXv3s3jRFbiAWAcQdQeKtXqAbbk7igZLiAXgCDJYKwYwa%2BMQQwGbmQQWBQcgWvB1nCujl/sxFwR7t1gnNHhUrzhCALr8MLhwQYkoeCPzicE5LLoqHxCAOyJyPnKWMYXyIKLQMKKKDoW4HoQWJeL8OiDxNbpodGsobYQjNcPuLQfujFPuvcAcE7OvLWNUgQOSAXskC/DyMgOLoMjMKEQ2OSBzgIFzuSI0OXmwNpkwGmJzCiNEZgOXugBsA2JkEngwFGLQMkPTGLk2OPgWFkQoDkVWA%2BI0c0ZuE1hBMUQHpkNbnON0aUQIB0XCq1E0H0RYBUuSPlKgKWA1BqBHIaJ4D7r0QcCKGNKWDFniObgpGgXNt1tOoMv1EQMkGEckfgfPtEtGlMVSKWAMd0K8PkREM6M4Z0EwJVAOEnpMRSDcSAHMQgAsSVN4N0KFNYesSAJsfrj5OvH0K4Rca%2BFQF2Hcb0f%2BC5EiQIPyEnq8e8Z1MfHPibgiRAG0WmMwQ7huEbp6lcesPpL/A0YaE0cSfXq8CrAYNiWKLiSrp4tzqWK4DNHIa3n8LGHgCCKTnEW8tHukcQJkXSc0bCQnI8fQEUSUcCTil8dMQSmwPyKkCmK8KqT8X8QCUscCaseNN4aMQXPjv4cZPcETiTgaPXkEdpgWBuAmPJhiHcCCKgJrguL2PqIaAQCeOKQgBkbzIQPQJzM6TMC5vgDMIgfHgAI5eDRBIwUK7GpyUk%2B6MknAhkEAfQ0kVI8n47xHRosAEBcAmQmRxAzgFhpg7wSFKRvLtRdjKyTggDwraqPQIEdmdlcAaC9mcw9m9kgQKTnEjHpnUmcmtgTiryl40mTh5nRoFkGJezuHaHQxuDykIjYAFjoANhMAQAMCf6XE86rkPHrBPGbm8yNBpj%2BkQAJlJkULN5aF85rleHOFIr%2Bm5xOq3mJnEDJmRqPkrnPk6lEbeHvnsrCjaioSMi9hUx3m/kPkBFL6HgkBNlIFEEoFfr7gFjTZO6rhsQMbWB4Dkldw7kNCcGsFJ7RnaQ1lrqjmbwlQuwVJ/47ykUy5ckgCuAQCsUhJWlIWNnUzx7oUPSYVEE4UFhwV/kUbrg8GAHEUpzjDXk2h25cF14oFUXQCMC0X1Ebm8ztC8wthLl7FwnvJOqnkFGmmXCbAIBcW7mHkpwJEnFJEIBthGxmCl5s5Ops4sl/KOBsCtnNicxTKln4SGUpz8VwEYVrhYViUSVjzSVWCyXNqbq7ofkHLkWO5fgDRWVhxXmARKUWAqVkkFhQlMULT/7cXN5SFGXJoOWnHOXjGTmtIWwyzQUJTMlvE%2BV4B%2BXTmBUIIEAtiWnJrhVoXwFRWiWxwPSxW2YEWJX1HUJgWX7NVQXISTLKW8E/gVIQUtUrU6TRrMU2UNCrDsWcXcWcwKV5XpVFUlV7VlUsW2WVXyH1GTFUmZkN6NULiDHZoOz%2BVuWvX6Xdy6V/X5kJS8m8XUIWCrSrRCBOT8hOTYDQ38h3ABABD8jYAACKh2QypVm%2BEAC1W1y1MFR1NVC5s%2BeNS1AUK1IOSFy5m8%2B1C1ByVNWNJNVM9NTqclU4UVLygyBOj1dwB4LATA/%2BI5RZm8vYb4JAkqlwtAqy5weAgU9xKCW5YtOKxAPpBoMwJ4zYYt7UPwblnMYt/Zv8ZgdsiFNNGxEtYgMt9MctyxQxitE4EtKtatfpmtblMWxAAtM0etDtHthtnixtWlUBC5%2BYWMggeAltVFdC8tmQ08W5eYqQTtIU6tN5zY8dodjgYgAVKsIdftXaAdhZQdm85IeAmkCZjQASst0ddteyW5xdZgEANuYoJtEGqtSdLtTSblddWdddudrw%2Bdptk%2B3EvEpYFwqQYx0QlIxAJ4tF3WHAqwtAnANuvAfgHAWgpAMxHA8yMljG6wmwnyuwPAySmgc9D%2BIANuCBC9HAkgvALAIA54cQYoXACQ8Qkg54PZJkXAM4NuowK9a9G9vAVUCBfEq9c9pAcAsASAaAxOIcZAFAKR0D9AMQwACgzAlUCAECpAWAm0WwAAaoSniKtJTpwIfTQNGMVJQBEMfaQP6MwL%2BcQ7wFA/nKtAwLQEjFQ1gALUYOICA5g3gHGI4GCFVDw3mDUD7tsIfSmJ0FQzyEYo0EjB4FgFQ9bbfdwKA1QAYMg3g5gAQ0Q6ozIIICIGIOwFIPo/IEoGoFQ7oO0AYEYCgNwTYDI1VLAFTuwLxKQGCDENwAkFoIOrWN0EIz5MWF%2BKYNvVIMVatKXj5AAOpCjFV1g7lh3IBxMEAUiXDoAANMy1DOD2IJSTBtDpIJQDClDlB5BpAZACB5OlMFCZBFNDAxDTCZPdC9ATBLFTAdBdB1CzC1OLD1NjB9CVPTBdPzDFPDD/RrAbBbASDz2L3L1UMb2N4JDng%2BSXjVnIBJMNy9j95di4CEAoUH3LC8DAM%2BNgNIAiPIA%2B4kDkCUBXkKDKCGCdBCDoN4gr2H1QPJB0CDEMB3MhC0CPMWy/0MOojvOIMgDIOoMKDoMcxvMwOrQ%2B5/PPNUNnN3DEDIOcC8BnP1D4Ar28D8AGOiDiAmO4tmMqDqA8NWP6CGDGD2P6By1ONQAuPEIGHuPDBeM%2BOrB%2BOZABNBM/ghMJUWBhM%2BQRPFUxNWFxMDiGCOBJM%2BTVGpNMDpPjN716DFjBDfMPNPMvOHMMz0OkB4h1T0PTMcBL2kAAvr2cDYCqCiONiqCLPLOSCrPrP0ybP/gQBb18ucw7ONj7OHPH2DoIBpPDDK76CcDX2kC31xDnhigJCf3njf0JAmQaDP2v3aomv/22B6BHMn2kDrEDlBscBxCzM8OpsZuDoeOJh%2BCSBAA%3D).  
  
Until now I haven't find a fast way to convert from eytzinger index to original index ([using a divide and conquer loop (x86-64 gcc (trunk), -std=c++14 -O2 -Wall -pedantic -pthread)](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCBmcaQADqgKhE4MHt6%2BekkpjgJBIeEsUTFxZraY9nkMQgRMxAQZPn5cFVVptfUEBWGR0bHxCnUNTVmtQ109RSUDZgCUtqhexMjsHOYAzMHI3lgmG25OQ8SYrPvYJhoAgpvbu5j7bmLAJIQILOeXN2ZbDDteewOADdMA4SJ9rrc/vdHsRDOhUB8NhdIT87gCHgcQgB3CHfX7/QFuZAIYgCVB4qGEzFuCKoZIEAD0YjwwAYzNorJC6AA%2BmJaKhRERiAA6BAJBKUtHQjGPOkMxloE6MznABAEbGYVnqnkETBDMUSqUEmEHHZ4FiEBTG9FEgheBL0Smok2yg7yoaKkiYRkRQiGyXIr5U020%2BmelheWiOBInZB4FICRnBPXAaIB51XNAMIaYVSxgDUQ3QIBAKQAXphdQXUAlokxhQXzGZmwWeeWvAWIAWvDmuZh0AWBUYhwJgAWGAW5iYAOwWAsne3EScMfbz2cAEWD10ZjILKYLVCoCggXhTADZJNWgdOd3vZ1Y7wXn8%2B8FQIED9luNhuNHMF5gS6Thoa5fLuL7PouyyTh6BClkq7AoEsgjEO20SoB%2B06WFwoFPpuXwHkeJ5noIl7XreNxzl8EFvh%2BX5fn%2BAFAQWIEbI%2BVwQVBy4FrBpaRtGeCxqCCZpKWtAKBEmHWDhbHBjOW6QtchHBLyBhDDySgEBAFZVgQBY3nJ7GXAAnFxk5uAAElcABKPIWAAkgAKgAVDpqDvjpupzAAtDyPIRGeAkMDyOzlrQUmyZC8lgXuykMKpTDqZpp4Xleek3ruQinJO2BkpOVzxdECgCAW6oEAkCggLuwBwgkCB4MgCgihMDD8MQ6AigOXiMgAfko8LRL6bxMMgADWTXqiwtCbPZgiYGmxABKgwAAPIRECTheNaeFUXhGjGbRn4/gx/5mSxuFXOBJkHsQTY/gWMnsVd%2B3YvV9BdkCd0XEG91cBRz3GZx1jA5Fl33vtZ3EBdV3Rdcnl6ZgACeBDlsEC08gIVYRIlA66qgPLBCQWCoZWZL%2BTj6DaXglbVgQJxVpUmBsIIIXIQQpBFtTukFnTmBVmS2Ks727OczTCPI6jRjRBjITk0o6Ac/DBZIyjaPS5jcu4/gwCEELggUQ%2B1EvrRvP86ggtoML9E/n%2BRsQc%2BiVKA01BiEo06gxBSsnAoUYEAxF0Qa9dCYBAps8gLesEGAYBfn9dtNrtHH28%2BSt0gQRAsOH5s8ozzMEJHX5h7njD55bgjeVAXDtl4jyPKH9NZxbbPeX9Plxx7ycFuBSv0FQ%2Bc%2BxERf0HnBc/hAVcdrXBz13zjeR958xt02lhFgQJYgJaDAQGnGdz8XLNl%2BzE81wcddhxHh8L3M7vsZ3ovc73/deIPDf76XbNfuP1dT24M9m03wsr5L2sF2beqB06Ij3sPEu8845mHPF/Sep9p7n2zpfRe%2BwABix8f5/znuguYIAd6QIjm/SON946ey5tWYg2on4v1nmQw%2BhdX7QIPs3Lg3lH4aWfkPJmMDmEd2TrRFWkt0Ya2xvLa2G447XDvobORd8Xze19iDDc3CB58JHoI2%2BSjnwRBOEwUaAdO74UUcnSoSh9zvggKItWqEJEU2XueLsODkG/zsVLBxstJFa1ZLrAh19pG23MfbBRSc9HFlLI4EOWiBFs1IKggBghSCePET4imqSJb2JlljCmPJtYBLZu7DcUSQBtWxPUXkiVdQOnoBASheihyYD7jwhhDM2Hv2FqQRpeikmwJ6aEppXY0nqwyVI%2BBPVEEnzcHXUZ3i8nywKf4rp5dW7ThnL/Nxszp7zNyZrXkhTVkECAYMiJwzlbZK8fs3xhyVmwIoUMl8ZjzkQUsZgaxUA9mOPls41x393G2KuekxZfidbHKAXMaOJ1ekJ10XolR0Y1EaN4aw/h7CrbYRMXo7ua9ol4FiWi7RCT%2BmHyyara5PyBzkrEWM0FdzwXkK/GUipVS%2BQKFqY6EOsKILgU4nQtpcSMUpJ5S%2BPlL5iGZ1IZ00e2BtlnwbhfZu8wsGko4SANVgDyiiufOK583zxkDnMOeKZ8rdnArpQc5ZjLL6tzOcMvVlyKUgqtUch52KlFlJiXgphJLFVoISQa%2BlNKclUoZUU4WJSWUkEqe1dlnL6k6vtrQtU9ChXHPtRc58UBJVQPRRCuBCCzW/01eXRe7tMGlpOVwDV/rkknO1U8vRQaDnGtNQCnZHiLULNdfc2119M1Zpbbc61Ea1mPNec82Gk79XiQeE2%2B2jtohaSoK7TAE6IIvM4oBaCAEfZItBvHcCJs62j2/L%2BCivK9wQSXc7Vdc6N1ir3O8z5VbpGyPOY6s6rF4Vd2fXO19p7BHfQ3Cq0JMNf2OtTuA3e0r81nvTfPSuHaFWzyVYA1uLdH26uvS%2BHuLS01EviVbMexa8HobLdfFuy95xlM3mAiBUrs6%2Bu6WRqtQCPVQeoXpFNOpNFEeFX7H8iHbVcII4KgTxzOO4dfO%2BYdFNgmXqfUo29K613Yb/fbF9Iju03IU8dH8H6r1KLOii9pOcZU6KPTJ5WAHaJAudZa25fzpm4Pk0st1gSSkwvAzZ8Jxm75nXc7jIgBMGBE2lqTfGtyGm%2BYuWZkTCTrMXPY60ZLwyHO0p7bcttrnAXBfDQW6%2BGytkofNY57L%2BTPPKoHelppBXR1Fek2E6dAWXzaZsQVlzZGGvVa1cV6FNslM4Za5Bmz27mIJckwXLFlgGuhcJu1SL6EDmxc/eNzuvHCOMMs0luLwzc1weJSRuVZWS1AZq6qi7GHa1oYDVqwdmnhkFdyz13TYbGsPIWHV5t73DWFfdUIp7U6FLra03On7i6FBOzUw%2B5rW7lPvMh6pl2cPD3Tq%2BHqFgjoGw0gIIjOszA2AFkcniY4XgHBOqy3C%2BO8yIDZiGKvdeIIwRQwOKToM8CCxMH/CAWFTAeQQB5xzCIgumDNS5hAf8ICjOd3AhzPA6BVBi4l5WKXNGHrDZpwu4IhApceuM4r5XJgACsFgQIm43HdK34udL66By8%2BOTAvBEBrHWOEwopcQEcgWBnfsueqH/H77XM777VlGtbh6BuXxB3ehACPU8bc8lVyHa%2BIfhkJ/umYAszkCwJ5XtvHkpuLDGMt3dNwBZA/R%2BfAj5OmfvogcPMeCAPVRoaZou%2BYxBmL1JrOgLzq8V7e/reRDhdE3d396iDrLeWELDzfxot4maEyYxf73bhYNB4o8jUv3QCQvk/r4WKNPgKlt%2BJV31pNv7flN9%2BT1P4I%2BvLBG6L2b0v35f2O9CUrXXWlv%2BR40IrNxnnpHn9Onp3G%2BF2JnjskninlLtLonMMngJHj/hAHgBzNnrnm3tXvbMekri/iXqblbl%2BPuNgRBKLsXm/pHgLsXngMDJYIQaQcbMgQwHrmgQWBgcATLtfjXq1ptjutxLQQ7hjqEsHmUizsKI8BzhcFztQUDmIaCBIeziLuGHBCACyGyHnKWOodyHyLQAKEKOCEoQWJeJ8MiDxEXkDriszgoYYW4PDOcPuHgRdPhIetcDsI7KvLWJUgQCSCHgkM/JyMgHzl/kQAkN4SSPTgIIziSPULnmwNDkwGmBzAiAEZgLnugMsA2GkJHgwFGLQAkHTLzk2AuvEQoIkVWA%2BKUeUZuI9hBBkR7mkEXnOPUVkQIDUbChMA0E0RYGUiSHlKgKWPVGqOHPqJ4C7o0TsIKKNKWOFtiPrvJGATwaDpuqEn1KEeEQgOrv5p3L0aSOSKWC0dUI8CkREE6GYZUEwBVAOJHrsf0YMdqCMcVN4NUCFPodMSALMert5KvF0BYcPsbFQF2IcY0f%2BM5MCQIDyJHhcVcR1IfEPrChARAFUWmPgRbhuFrnfL0UsHpD/AWMiVWOXo8LZpcfLCKHCdLu4kzqWK4NNEIaDl8LGHgECLjsEecsHjEcQHEfqGUSiR6icfQOkZkS8R6rcfsRqhaFWEkCmI8KKeFvccMd7GMS8ZMWNA4Z0fnM4dFK4VcFjjjnqOXu4dDgWBuAmEJiiFcECKgIrguL2LqPqAQCeOyQgLETzIQPQBzCaUMMagWPgEMIAWLAWAAI5eDRCIzkKLFUlIQu6EkHCukEDvS4llI0nOEhHrwsAEBcDGTGQbAzgFhphbztxGShJtRdhKyTggBwrlAPQAHVk1lcAaANkcz1kNkgTyTwH/Epx4pRk4mUmtgTjLzZ64mTiJldnJlA6exdniG2H8mYjYA%2BkNhMAQAMDcGRlTls5uAzkOHDBpgOkQDBmhlMrjl4aTk2Hrn2FmGIoOk5zdp7khnEBhmHwrnyGs4yncYOGXkcoCiaioR0i9iUz7n3mHlFmh4llUwBlIHEE/r7gFiDZW6rhsQ0bWB4AYmdzoALk0GEGR6%2BlaT5kTodFdnFTOxlL35bxoV1ALCRmuAQBkU854ULqgV/6QVrjQWwVBl3kPkfwIV0EWDIVJrbmATWhm60Fl7EHYXQCMB0Wh4zk8ytA8wthHk8EdlabdrHFLCnGzlFinArCbE0UrkTleENi%2BHdF9n6rdrtYGAwmulsAVnNgczGoZkuFKUQQMVAEQX3RQVIGsUAUcUkbrj0FP4oUIr6i%2BwcrzIYWW7MpaURH8UOnhXokFjfHEXzQP66XYG16jZJpDAGU%2BEIBtiGxxAQRfnSy/nxTEmWWODWUDl2XwIEAthalOUvguXgX/7MWeUxz3TeWjx%2BVWABVJrbr7pXlFU/nIToBxXMpdlDX%2BQjXaRdkkXUULkUVJmD66UcwxWCU8WEH/iJWzXJWkULVpXCGh5YnRm4kmULitFZr2w2UFW4m1VdwyUxkV5LXoC0nAV3wWArQrRCCOQ8iOTYDfU8hXABABA8jYAACKa2wySV0%2BEAH52%2B5sxVI1FFmVo5g%2BcNk1JV6A32Y%2Bx568c1cN8y2NoeE568VFBN3agVU4zFNeoSjuh1B4LATAD%2B7Z8cZSvYb4JAUqpwtAyyxweAAURxyCc57NHqxAtpeoQwJ4zY7NbUHw8QPYLBTZP8Zgts2pJNMxnNYgvNdM/N4xbRQtE4nNot4t9pUtcQ4WxAjN008tFtLASt7iKtkl6tIAeYSQIQggeAWt2FtCAtaQU8c5rtFIQOYtwUEtu5zYgd7tjgYgtlys%2BYqA9tnajtKZ5yuxeAGkwZ9QfifNvt%2BtOyc5JIeAZgEAJuIoqtv6IddpktDScQhdLY8tddidjwydatyi/BMEKhpYJwSQXR0QZIxAJ4eF06HACwtAnAJuvAfgHAWgpAAxHAsy/ltGSwKwHymwPAiSmgI9R%2BIAJuABY9HAkgvALAIA54GgIoxkM4Jup9F9V9%2B0l954pAU9M9c9vAlUABcE09I9pAcAsASAaA2OwcZAFAkRAD9AMQwACgzAFUCA4CpAWAG0qwAAagStiCtITpwOvTQNGEVJQBEJvaQH6MwPeRg7wP/XnCtAwLQIjPg1gIzUYOIJ/XA3gHGI4CCJVIw3mKCC7msOvSmJUPg5yAYvUIjB4FgPgzrcfdwF/VQAYBA8g5gKg%2Bg1IzIIICIGIOwFICo/IEoGoPg7oK0AYEYCgNxfoPzZVLAETuwLBKQCCDENwAABxaADq1jVDsPeTFhfimCL1SAJUrTZ7eQADq/ICVdYaFHtyAITBApIpw6Ar9jMDgokti8UowLQqS8UUwfQMQrQOQqQAgKT2QyQuTDAGTxQ/Q4w8T1QnQIwYxYwbQChHQww3QwQvQpTWTtgjT%2BT4wjTJTMwf0iwywqwEgo949k9%2BDc9le9j543kl4eZyAET9cvYbeXYuAhAJAy8Gwf0vAH9Tj39SAnDyALuJA5AlA9QEDyghglQQgMD2IU969/9CQdArRDA5zIQtAVz5sT9pDiIDzYDIAEDUDCgMD7M9zgDK0Lu7zNz%2BD%2BzVwxAEDnAvA%2BztQ%2BAU9vA/Aqjog4gmjaL2jKg6gjD%2Bj%2BghgxgJjgj5jUAljRCKhNj/QDjTjCwLjaQbjHjP4XjPVFgPj3kfjCVQTehITA4hgjgET3kBR0TTAsT/TK9egxYwQLzlz1ztzWz9MJDpA2ItUJDwzHAE9j9YznA2AqgXDjYqgkz0zkgsz8zdMiz/4EAC9bLHMKzjYa9cwWzm9A6CAMT/QUu%2BgnAh9pAx9GwxkZdZgkg/rGgQbIbQb0gnzs98Ltgeg2zW9pA0xzZXrHAGwozjDL9G9n9A6tjiYfgkgQAA%3D)).  
I'm wondering if it is possible to tamper with the problem, e.g. change from an array-based complete binary tree to an array-based tree where `left subtree node count - right subtree node count == 0 or 1`, because the tree will still be compact but there will be less arithmetic operations when converting the index:  
  
- if node_count == 1, eytzinger_one_based_index should be 1, inorder_zero_based_index results in 0;  
- if node_count == 2k+0 (left_subtree_node_count is k, right_subtree_node_count is k-1) or node_count == 2k+1 (left_subtree_node_count is k, right_subtree_node_count is k): (computing left_subtree_node_count and right_subtree_node_count is much simpler: no `std::min`, only one decrement operation)  
  -   if eytzinger_one_based_index is 1, inorder_zero_based_index results in left_subtree_node_count;  
  -   if eytzinger_one_based_index begins with "10", inorder_zero_based_index results in ... (recursion);  
  -   if eytzinger_one_based_index begins with "11", inorder_zero_based_index results in ... (recursion);  
  
The only work left is to figure out how to build the tampered version of eytzinger layout and search in the tampered version of eytzinger layout.

---

## Comment 8

> Username: HDembinski  
> Created at: 2022-12-12 11:33:51 UTC  
> Updated at: 2022-12-12 11:34:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1346322350  

This is nice and impressive work. I am surprised to see that ff6 and ff7 are even seem to be faster than ff0 on average. If we can use boost::core::countr_zero(v) + 1 that is ideal, because boost::core is already a dependency. I was not aware that this function exists in boost::core.

---

## Comment 9

> Username: HDembinski  
> Created at: 2022-12-22 09:11:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1362591551  

@jhcarl0814 So this is cool, how do we proceed?  
  
I think the results are promising enough that we should do the next step, which is to replace the standard search in `axis::variable` with the eytzinger search in a PR. Once this is done, we should run some more benchmarks with the new code under realistic conditions. This should give us the data to decide whether the new search should be the default or whether we need to add an option so that a user can select the trade-off they prefer.  
  
 Are you interested in submitting a PR? You put so much research into this, so you should be the main author of the PR to give you the credit.

---

## Comment 10

> Username: HDembinski  
> Created at: 2022-12-22 09:18:52 UTC  
> Updated at: 2022-12-22 09:19:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1362599777  

The place to construct the data structures is about here:  
https://github.com/boostorg/histogram/blob/develop/include/boost/histogram/axis/variable.hpp#L101  
We expect that the user provides a sorted list (and throw if that's not the case).  
  
This function computes the index for a real argument:  
https://github.com/boostorg/histogram/blob/develop/include/boost/histogram/axis/variable.hpp#L163  
There are some complications, because an axis can be circular, and there is a special treatment if the value ends up on the edge of the last bin in some cases. Also note that the code uses upper_bound and then subtracts 1, because we want the intervals to be inclusive on the lower edge and exclusive on the upper edge (except for the last bin in some situations).  
  
Finally, there is also a function to update the axis, because an axis can grow. This should update the eytzinger structure. We don't have to make the update super efficient, because this is not called very often.  
https://github.com/boostorg/histogram/blob/develop/include/boost/histogram/axis/variable.hpp#L175  
  
You can read on the concept of an axis type here:  
https://www.boost.org/doc/libs/master/libs/histogram/doc/html/histogram/concepts.html#histogram.concepts.Axis  
which explains in words what the interface is supposed to do.

---

## Comment 11

> Username: jhcarl0814  
> Created at: 2022-12-22 10:02:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1362641155  

@HDembinski Sorry for being late. (The company I work for was testing the new workflow in the past week (make a cms website in 1 week by 1 person given an html5 template) so I didn't make time.)  
I'm very interested in submitting a PR! Thank you very much for the links and hints! I'm starting to work on it.

---

## Comment 12

> Username: HDembinski  
> Created at: 2022-12-22 10:11:08 UTC  
> Updated at: 2022-12-22 11:37:46 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1362651509  

Don't worry, I also have a job and cannot always react immediately :). I am looking forward to it. For reference, I removed the experimental parts in your code and removed the vector a from the eytzinger struct. We only need to hold a reference to a during construction of the tree. I replaced the call to std::lower_bound with the real thing, a call to the index function of axis::variable. I need to subtract 1 from the index in eytzinger to get the same behavior.  
https://godbolt.org/z/M74e7WeWv

---

## Comment 13

> Username: jhcarl0814  
> Created at: 2022-12-23 20:35:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1364321294  

@HDembinski The `k = 2 * k + !(x < b_[k]); // double negation to handle nan correctly` you used in 8ab311f000c2c0e363ebecf5db18495707b25853 not only covered nan but also covered bin edges.  
  
[using `targets = data` exposes the problem (i.e. `lower_bound - 1` can not replace `upper_bound - 1`, in other words, `lower_bound` can not replace `upper_bound`)](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIMwDMpK4AMngMmAByPgBGmMQSGgCspAAOqAqETgwe3r7%2BQemZjgJhEdEscQlcybaY9iUMQgRMxAS5Pn6BdQ3Zza0EZVGx8YkpCi1tHfndEwNDFVVjAJS2qF7EyOwc5gHhyN5YJgFuThPEmKzH2CYaAIK7%2B4eYx25iwCSECCzXtw%2BBTy8RxOADdMA4SL97o8GAcgS8TsRDOhUD8AjdoQDYc9XsgEMQBKgof89tj4a8YqgMgQAPRiPDABh02gMiLoAD6YloqFERGIADoEKlUsSYXDgW5KdSaWgLjSWcAEAQAO6YBlK9kETATQXC0VY8UIyVUiYykiYGkxQi6kXov5inEnKWmlheWiOVIXZB4TICGnhLXAeI2/Wkw0Uk20hA%2BojAJEsOmqH00kGtPBMGL0EN26H3AMAaioVAUEC8AYAbJJNfmQct7QB2Kz3fMt/N4KgQEHHAAiPY0y3zFwIGwY%2BY0xybJPb%2Ba4f1b%2BZpNPzqvznswVHi%2BedBBAKHN%2BcysMwbYIbYU%2BaY%2BawqVcjGQAE8L7QLkx0Pe562hyOt5Hd7L2HuXiCMQ7IAF7xKgnZ1pYs4BJOuz1EoH4tl%2BxCjtuu6uu6eBrt6vp%2BCAtAKDEUHWLB8GBK47YNr2uZ3FqLCpAYWqvAQ943swbD5gAKsS5xeA4%2BaYPeBCgeEQbEPmJiNshQkiWJRjxBAaAMBMB4EOgu5ghCxCvLxdpmOWF4DiAsnzjE7IQEw/KZOBEADtYM6rG26CqJZ1m2Zg9lSZYTmydJk7zvO4SEFZdZwWZrZ4K57ImEkFjjkk3ZSQEyUeXgdkDgAtDOE7%2BfWtEPM2UWCPmqA3kifL2RA3H5ipEzmEZqgDvVp4BZFLaedWADWKXJeRHXLtG9D5hAvWvFu7I2RlXnLHWxVBUF42pfmZj5gAVPm42%2BRAFlxRY3VxclE3NXlC1LSlNx2itRYlgAft14WBYtg6YMOaEuW5%2B2HUlZ13PO0mFbJXWniFBDKQIakTJpIDaXyem/IZF6kAeM3VngfVjijINbZjXAOTJ53BVQo3LW4PYXtNmUE89L1RZjYNWSjeAo2tm2PX9dPBTF31HQz%2BY5QNRMvXt8U/cdK1MPteDWLLv0RcLi0YxTjNMMzrMbbjjn45zL2A4NqGjjLCv/a2%2Bt0fO0NaeC8MnNxKMYSA9KMmwgi7s7bKcrQ3K8pCdso5WvzopNuudRp1s6a8QGZIymDoA7v5OwqDCuzuSesnHXs%2B0wttuNHGfx/mgfXdgn2xSb%2BsV/cBxMAo54TOVyo53iUmE3cqReJmeDIKZ50N6kTcEHiEOqaeeKtJtbB10wQYoyinf0Jt6AbDn2SYwwbq0KkBDECZUmK/mU8KDPmCxY2R8n4DpCDcvlXZGfFi36vAhX4NcxtA/VsoPihK7tGirsguAoTwXhGjsgODybqu4GCoGVPZQGrdaat0KgDc6d1%2B6D2HjTN%2B4dv4EhgbuJ%2BjRXjz0zAiUu9QmCpCUOgTGX88T4NQH/dUgDtQgLARA5AUCQAwLgdldSAxy5IOJqNIh98BzrTEQIdkmNKHULjvyNAQFwZPUGtOCAF8gyxXiolbs81TZc3UjDJRbUTgTU0afFK5MThCQMPI9Aij1iCG8q8CaX9XC0FDsg%2B09xPR4FTFqXuBi6qQzHggCeh9tTHy0V40hi8rwrzAV4%2BhP8CEgEcGwdk6QAyvBSYw5hACgHsPvpww6wd34ECETRP6fwGJMRzseXEBg675m7DGYkIJUDRUHEBTU2oCAllanVcJxBNqOAIPQFGbSGpI3wBMbGaNTwAEcvDxHvOApxBBsF91wSYqxE1xkjVcTY9xDB0CeKrsEr%2BLACBcAAJx3ICPWfMQYGAQB1pc%2Bc/BJIQBxqOEAiCzAoxqMCjQGhQVgohWCwG2zgmW12esUx1i3A%2BTWvmUc5g1rHJRRisxJzcEeK8fCmGcM/ZuDieQq8OcmAQAYKog%2BX9SW6ROBS64%2BZJhBgGRAFZayNnKPpXC1sjKbZkpBmyoBboBnsmEqJcSSkeXEHWUowQAq6bCsjicMVwcJXugUOyLwwp4jskpEBdA3LVmKr5SqmpB9vmjRxsrFa444JtnzGAMAFMGATh8lYSweB9GGJbOgal0s%2BYUzmeDV5AqcEw2AW0X5uC4jAHCBAYNLRnKnLNWmpgc0bWCpbHa35izXUU2dRYV17qKYKqVZs71ss/UBsDRyt6ChQ1JUxhG6AjBo10R8UVftfa%2B102rVaggPYvUmzps2gZPZs1Es/NqSVeqZUKQkvyIBM1zW8uVVs%2BdKFF26v1YakCJqznru1JukdO6e0Dt7Xe29%2BarzrDIeyrgKMCBmD3Ygwa84V1yuZeS599A2VKFaMPbNqrA0YObggfMD9MW/vkv%2B1sciaHsrwGwAFmKUaNVuZXYRL1C0OsxmWitHqVpXtrS6%2BtFh/U/pejqqVf7FK6XijLeW3ZQObAQBAadra2NHUg4tc2j72oH3MonaMDc4ysHdkmBQWk0wZmA3i0uTB5OpupUJrm0Gh6wfg2YNFLZuSqhPU42hqG47ocw6inDhkP34fowWkg9ri2OuSqRjGlaKMWprcoutlhrB0fEwxg9UqDUVWNeZttvZUrqZ9PycIWBVC8daJy/jtHBNfpEzGv8iL9k2IQ5%2BZ%2Bga6ZYcMwVlFH6Fyvsq0Y3chLPkvQsAAeRa0Ibi7JuLYA6%2ByO4IQQjsmwAARQgE5%2BrIAk0psY3qiLRrT3oGcuNzNEAZtHsiwt1Yy3E2YGTW8tbzGJJbZC2HGGrhVtheXUhljjbFpPTNudHLBU%2B0FhYEwFNsLiW7iAu2EgLBWFiHZBG4geAYigOyMc0uP2vHEF6VqCYJZMU/e%2BT8QzKMfvAuxWYfseavs8L%2B4D4HoPwcv1U%2Biv7MO4f9MR4ZmBxA3ueLR%2BT%2BnmO8VuGxzevHmBVDpAiIIdMtAgcxhB2D4hZPufpEpwwPpCOxuGYlwIRgjgxDYaEjz1ArPkUc9x0K3Z0Z2QKBWa0TORPRcQ7J3iPAZgIBJH5Djydn4qey8xZbz9TPXea9eNrpr%2B73roUThcdIH94gEmICWaNz37gcFWLQTgSReB%2BA4FoUgTCODk0C75YBGwtg%2BQCDwUgO4k/R9WFApI4LY8cEkLwFgIAki26SFwcsAAOSQGgm9N8MncyQSRpCJ%2BT6n3gCnwWF60KsOAsAkBoEYnQeI5BKBT9SDPhIwAFDMGoQgVABBSBYBBN3TAAA1PAmBlQtY4pwfPNB3TxAU7tTQvArTMEVef3gU/U4tYYLQe8d/t%2BYDe0YcQRe2%2BeAXojgYICmgB3O4IoC2w%2BeAY9Q3%2BLIMQSIiqHgWA3%2BO8GGz%2BqwVABgK%2Bh%2Bx%2Bp%2BjAz%2BMgggIgYg7AUgJB8gSgag3%2Bugb6BgRgKANG%2BgoOCmsAnE7A24pAYICQ3ATeo%2Bqw5UjQ4BWU0MPYpgGeFgUgAsLWa0WUAA6lyALDeMGvzsgCoQQPiJcOgIPvUDbNkC4GctMH4G%2BqEOEMMJUKMG%2BkUFkAICYXoLYY0AsCMNUD0AYQIP0FMCAvkG%2BnYB4U0JMIMBYYsNYbYEEQ4X4UES4VYdUKsFnpsBQTHnHgnt/qnvmKoE3uWFlJWC8sgBobxrDgwI9KNLgIQC5rsPjLwCPtHqQBAEgJAcgKAiQHPlZMQCvsoIYPUEIBvsqInvngvnQM/AwJ0RELQD0bAn3i/qiIvvQMvqvlQgoBvlvoMXMS1qAhMX0d/o0XcO0eAbwI0c0PgInrwPwKQaIOIJQWcdQSoOoIAfQfoIYMYCwYgewVAJwZNpGDwaMPwYISntvNkKIeIalJIb6tIZILIfIUod7CoXHIYI4BoVlNvNoa%2BIPusIkXoNDOEKMd0b0f0dURcDAbwMqEiKkFgfoCkaQFMSnpwNgKoFAXyBkVkTkRCcAPkaNDvEBCURAOnmCSjGUYyZUcsNUXfnNKQAgDoaMPZBSZXtXiAJIJIPyJIE3nnkkHclwFwPWAEMkJIOWFSWkZwIPnoDUWKVAjUOXpwAEKkYAQPgXqKasLwfhPKUAA%3D), previously `targets = <random>` doesn't expose the problem because `double` has so many bits that it's almost impossible for `<random>` to generate exact bin edges;  
  
[using `targets = data` and `k = 2 * k + !(x < b_[k]);` fixes the problem](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxBIArBqkAA6oCoRODB7evnrJqY4CQSHhLFExXPG2mPb5DEIETMQEmT5%2BXJXV6XUNBIVhkdFxCQr1jc3ZbcPdvcWlgwCUtqhexMjsHOYAzMHI3lgmG25Ow8SYrPvYJhoAgpvbu5j7bmLAJIQILOeXN2ZbDDteewOADdMA4SJ9rrc/vdHsRDOhUB8NhdIT87gCHgdkAhiAJUBDvr9/oC3BFUCkCAB6MR4YAMam0WkhdAAfTEtFQoiIxAAdAhEokCVDiZjSeThpS0CdKYzgAgCAB3TC0%2BUsgiYYZ8gVCtHQjGPMkUyUkTCUiKELWC5FfYUwg6GiUsLy0RyJE7IPCpASU4Lq4DRS06ol2sVGhCeojAOEsamqT2UoENPBMCL0QPWyHXX0AaioVAUEC8voAbJI1dmgXMbQB2KzXbMN7N4KgQIH7AAiHY0c2zJwIywY2Y0%2BzrhOb2a4X0b2cplOzSuzbswVGi2YdBBAKBN2dSf0wTYITYU2aY2awiVcjGQAE8T7QTkx0Nep42%2BwO1%2BKN1uTpu0F5BMQLIAF7RKgrZVpYk4bKOmxVEoL4Nm%2BxCDuum5Oi6eBLh6Xp%2BCAtAKBE4HWFBME/K4zY1p2mZXOqLCJAY6qPAQ14XswbDZgAKgSxxeA42aYNeBBAcE/rENmJi1gh/GCcJRjRBAaAMMMO4EOgm4gmCxCPFx1pmMWJ49iAUnThELIQEwPKpCBEA9tYE4LE26CqGZFlWZgNniZY9lSRJo7TtOwSEOZVbQcZjazo5zkmLEFjDrE7biRsCWuXg1k9gAtBOI5hQ2eBOSy0WxdFCUdielmpe5IV%2BY2ElUTc9aNjmqAXnC3I2RAHHZopwzmPpqg9t1h6%2BTlO4VeWADWiUJSRI0KuG9DZhAk2PGuLLlWlVYNf5/kRctSXZmY2YAFTZstXkQKZhXjcViVuNm/XZVt22NntCWHSdZ0WNmYCQKot2rVdxVVSNe0XNa%2B15gWAB%2B43A0905IYOeVRTF13xY9VzTrVNpPW55aBQQCkCMpwxqSAGnctpnx6SepCjSB%2BNTUOdN44er32eJknw41VCLXtbilSlG2c9Vz2NUzBPmXTeB0%2B9p1w5jYvhXOyMFajN2lXg2aZTN3PbargPxRLGNK42l3q0bguFXg1i2%2BjoV6/5WuawwQVMNLsvHadnlfVwCti9jjuI02Js1dWdVSaT6mgpTBwcXTqEgDSdJsIIm7J8ybK0ByXLgnHdOlp8yIAw7isNlH5Mx3nbj/qkdKYOgCefunsoMKnX4Zw3Wc50wsc10pTINwXkhF9gkUFaX2OT9cOxMAox7DM1Cq99iItfIkXipngyBGbjRCJMvBDYkTSmHtiDQnWw89MP6dMIpv9Anegyy9%2BkTMMM6tCJAQxCGeJjtXwUDfTABVayAOAbVUgI1n6tXSKAiwMDX4CEgSNSYjR4EV2xLiBgqBNzhjlCyE4ChPBeBqCyHYnJxqbhwQqGytU15PUDmXf%2BzCoaLwPivBAHlhqO0wTiPEm5EE1EePfVMmIx5VCYIkJQ6AmZ8OwbgkA%2BCECEI1CQshFDkBUJADQjymU0EEAnqLJ2vMIBCLgT2I65iBAsiZpI6RDceR/kEHQ0uYtxwQHAf6NWRV4qbWYWLTBSwhoHBWl4kBt0Vr2JkU4pYLjbKhIOCpMmrhaChwbEw9exA8CJnVLvZhg0uoIAvtmcJRipKiMfmeF%2BZD0nJN/PwnBm5HBsBZMkX0jx5ECKUSqVRxDvAaJztdYuBjymQnDhjL4tF6K933I8We89sztgjASIEqA8q9n/GqDUBACyFPPsQE6jgCD0DpssnqNN8DDBZmNQ8ABHLw0RrzkLiQQWyXNmFBNIZEpJxyFqPBWhXVJky95kxYAQLgABOSFGxqzZn9AwCAfsQXMP4GJCArNsyDhACLMwdMuAJAnBoQlBLiX4uJcS2q7zjHTi%2BSEgWSTzCHSxZ5Q6AKkmDnZXdIFDB0BpLcc9CuFNq6VPEWeXuTAIAMH9oK1S0dNIiKWGI842YRj%2Bl2RAB5TyXn/jeXU2lcrK4KoOKzFVRDnS7JZAJISIl5JauIM85xeqBXbSFVXLSJrblmo1BahQLIvACmiCyMk/50CaseQ6nVggFYjTRYtTFzt9rDmgk2b6YBSoMBHD7aweB/GmwbOgCV1sNb7SuYTBFMbeGGuIY0DFhqojAGCGYiVDkeVhsLfUOYlaAmNjjRi25qbSrJq%2BlrH6pV7WOteVm22lhc0jWnGqzAuzi2W1LRGaAjBu043qju7de7HYTqjQQDsmaXX%2BUXcupKHamD6tfD6l0frrWyVEjyIhFVw3aqdTK7a5qH3%2BsDYBENvLX0anfYer9KL927uotusWorVVtFVWYW9GSPn5ukjauSHq3CipVUoBox9r3fqVuww%2Bq94FMunE%2B21YkGzRIbqqvAbAcVMrpr1CFU8aXPT7Qmpmw7U1jv2uBqdKaZ0WDnY7MWEVf2Wuo1hldnYkr4ZWFwi9CgFPEfQzJx9MkaMKY7Mp4%2BamNPpV1j21DdUA5ofzYncMi8oysHTnGBQ6kkwpnoOyseTBnPNs7Sh/ypHOHZgo2YZlDYORKkA3E2R9HZEtPYKytjekCDIYmWesWPGB2JoSvx0d6ahMRsnbq6dlgc15vQ3ehQvr/0tWDdF/TSVvOeh5MELAqgIDGZijbPx/nMlVrJn%2BelK1KOviQRV7aLHQs/LuilmcCHpv1JAMC6e5nswWAAPLraEBxFkHFsDbZZFcAIAQWTYAAIoQHnY2CuDam3aZq0GoD6AHJXfLoa1wEB7sBtq09hYr3Fu3cRfduTok/uSeu%2B93ln372yd01h8r20qo1UYRM8ZEcsyCFKUwJt1LI6Gv/M2EgLBVFiBZGW7JERSHpE89mAndTiBbPVMMAsTKCdoo%2BKFumBP8VcrMN2FFBqyY4OICwUn5O8CU%2BEYksewukTGIZwwbZzPLuhdl2IVjWKic88SW4Pn3bBebkwKoZIIRBDJloGTiMFOqfIOl/xY3%2BIz0K6VxqplRuTeMEcOrzn9vkja4Zbr/nK2DcoHDCyBQDyGhd3F5L6ndvsR4DMBAWIPIg/y8ZzslnoWE/IZ9zn/3jw9cC7vf2ZCH4KSbhOMkdB0RcTEALDG1HVwOALFoJwWIvA/AcC0KQXBHABala8sQ5YqxPIbB4KQDc3eW8LCoRUNvHBJC8BYCAYs1YeRmEkNWDYAAONfsRIWxB31wHfkLSBd57333gLmEhT60AsOAsAkBoDonQaI5BKAv8SG/mIwAFDMGkQQFQAIFICwCBG3kwAADU8BMAFR1tWJOAJ8aAXRogXMLpNBeBzRmAHVEDeAX9251sGBaBrwMDQDMBRcjBxBp9QC8B3RHAQQXNqCjdQRSE1gJ9fQqhSDGQIg4QHUPAsBSCf4mNcCFgqADA/9oDYD4DGBcCZBBARAxB2ApA5D5AlA1BSDdA2gDAjAUAxN9AJcXNYA2J2B1xSAQQYhuAd978FhmoahGD0pSYOxTBB8LApBtZ1tDp0oAB1dkbWC8QtM3ZAPwggHEU4dAa/KoGOdIFwXlMYVoUgQIYIPoEoAYNoXINIAQOInIFIDIhgaYfoModoKIgQLoUYEhcYIohwToEYHoJImYVI2wGorIiYGo/IlIsoBYYfFYJQ1vdvTvUgvve6PfdKUseFZAIIjrBXWGRaXAQgEgMfP2XgO/FvUgCAJAZg5AUhEgD/cyYgP/ZQQwKoIQIAhULvCfL/OgJBBgA4kIWgY41AU40gi4%2BgX/f/KRBQIAkA546IdbUhe4x4pg1QUEK4PYxg3gDYuofALvXgfgeQ0QcQZQ2E1QlQdQagzQ/QQwYwPQ7gwwqAYwkAUw8wxAqwmfXvb%2BdIewxwpKZwqwSCSQdwzwnw7OPwhuQwRwII9Kb%2BUIx8a/JYbovQUmYIG4o4k4s4pYk4Ng3gBUOERIEQ/QPo8/AYzgbAIEzY7kIY4sEY%2Bk4AcYxaH%2Bf8aYiAAfWkmwbMWY9UzYRYyfDArtUgBAMIgYGyeUxfZfEADYMwHkasWIPSLgd0yQDQSQE/LgZQi/XgK/WwPQZY20qhUlZ0jYfo6g8MqMhYcwnCEASQIAA%3D%3D%3D);

---

## Comment 14

> Username: HDembinski  
> Created at: 2022-12-23 20:39:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/369#issuecomment-1364322447  

I know that it works also for bin edges, I had that tested in an intermediate version of the local benchmark. Let's continue the discussion in the PR.
