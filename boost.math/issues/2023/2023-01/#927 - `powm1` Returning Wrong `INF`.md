# #927 - `powm1` Returning Wrong `INF` [Closed]

> Username: mborland  
> Created at: 2023-01-25 17:38:33 UTC  
> Updated at: 2023-01-25 18:47:05 UTC  
> Closed at: 2023-01-25 18:47:04 UTC  
> Url: https://github.com/boostorg/math/issues/927  

See https://github.com/scipy/scipy/actions/runs/3880172950/jobs/6618011730#step:6:585  
From: https://github.com/scipy/scipy/pull/17432  
  
We should be returning `-INF` instead of `INF`. This is a change between Scipy's last boost release (1.79/1.80) and develop.

---

## Comment 1

> Username: mborland  
> Created at: 2023-01-25 18:47:04 UTC  
> Url: https://github.com/boostorg/math/issues/927#issuecomment-1404070452  

Scipy had their own handling which should be fixed by: https://github.com/scipy/scipy/pull/17855
