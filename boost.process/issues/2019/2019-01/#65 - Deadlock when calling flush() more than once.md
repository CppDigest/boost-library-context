# #65 - Deadlock when calling flush() more than once. [Closed]

> Username: davisking  
> Created at: 2019-01-02 20:42:39 UTC  
> Updated at: 2019-04-20 08:42:13 UTC  
> Closed at: 2019-04-20 08:42:13 UTC  
> Url: https://github.com/boostorg/process/issues/65  

The following program hangs if the `flush()` call is uncommented, but runs to completion when left commented out.   
  
```  
int main() {  
    ipstream in;   
    opstream out;  
    spawn("cat", std_in < out, std_out > in);  
  
    int val = 0;  
  
    out << 1 << endl;  
    in >> val;  
    cout << "val: "<< val << endl;  
  
    // uncomment this and program hangs.  
    //out.flush();  
  
    out << 2 << endl;  
    in >> val;  
    cout << "val: "<< val << endl;  
}  
```

---

## Comment 1

> Username: davisking  
> Created at: 2019-01-02 22:19:29 UTC  
> Url: https://github.com/boostorg/process/issues/65#issuecomment-451003136  

As an aside, I really dig the API in this library.  It's very nicely done :)

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2019-01-07 10:38:12 UTC  
> Url: https://github.com/boostorg/process/issues/65#issuecomment-451893181  

Is this occuring on posix or windows?

---

## Comment 3

> Username: davisking  
> Created at: 2019-01-07 12:00:48 UTC  
> Url: https://github.com/boostorg/process/issues/65#issuecomment-451913279  

In Ubuntu 16.04.
