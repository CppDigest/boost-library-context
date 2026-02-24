# #24 - Condition number of summation [Closed]

> Username: NAThompson  
> Created at: 2019-01-09 18:25:26 UTC  
> Updated at: 2019-01-09 21:53:40 UTC  
> Closed at: 2019-01-09 21:53:27 UTC  
> Url: https://github.com/boostorg/accumulators/issues/24  

Would you be interested in accepting a PR for the condition number of summation?  
  
It's not a complicated algorithm, of course, but still it's a very useful diagnostic. Here's a sketch:  
  
```cpp  
template<class Real>  
class summation_condition_number {  
public:  
    summation_condition_number(Real x = 0)  
    {  
        using std::abs;  
        m_l1 = abs(x);  
        m_sum = x;  
    }  
  
    void operator+=(Real x)  
    {  
        using std::abs;  
        m_l1 += abs(x);  
        m_sum += x;  
    }  
  
    [[nodiscard]] Real operator()() const  
    {  
        using std::abs;  
        if (m_sum == Real(0) && m_l1 != Real(0))  
        {  
            return std::numeric_limits<Real>::infinity();  
        }  
        return m_l1/abs(m_sum);  
    }  
  
    [[nodiscard]] Real sum() const  
    {  
        return m_sum;  
    }  
  
private:  
    Real m_l1;  
    Real m_sum;  
};  
```  
  
and usage:  
  
```cpp  
    std::random_device rd;  
    std::mt19937 gen(rd());  
    std::uniform_real_distribution<Real> dist(-1,1);  
    cond = summation_condition_number<Real>();  
    int i = 0;  
    while(i++ < 1000000)  
    {  
        cond += dist(gen);  
    }  
    std::cout << "Condition number = " << cond() << "\n";  
    std::cout << "Sum = " << cond.sum() << "\n";  
  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-01-09 21:53:27 UTC  
> Updated at: 2019-01-09 21:53:40 UTC  
> Url: https://github.com/boostorg/accumulators/issues/24#issuecomment-452882516  

Sorry for the noise, actually I don't think `accumulators` is the correct place for this; it should be grouped with the other condition numbers (like function evaluation and matrix condition numbers.)
