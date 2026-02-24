# #494 - Hana runtime performance is 100 times less than native cpp? [Closed]

> Username: jackfong123  
> Created at: 2021-08-25 05:04:15 UTC  
> Updated at: 2022-10-21 03:56:36 UTC  
> Closed at: 2022-10-21 03:56:35 UTC  
> Url: https://github.com/boostorg/hana/issues/494  

```  
class Person {  
public:  
    Person(std::string const& _name): name(_name) {  
        gvids.resize(1000, std::unordered_set<int64_t>());  
        for (size_t i = 0; i < gvids.size(); ++i) {  
            for (size_t j = 0; j < 1000; ++j) {  
                gvids[i].insert(j);  
            }  
        }  
        nc = NestedClass(0, "123456789", {1,2,3,4,5,6,7,8,9});  
    }  
    std::string const& get_name() const {return name;}  
      
    struct NestedClass{  
        int gender;  
        std::string id;  
        std::vector<double> vec;  
        NestedClass() {}  
        NestedClass(int _gender, std::string _id, std::vector<double> _vec): gender(_gender), id(_id), vec(_vec) {}  
    };  
    NestedClass nc;   
    std::vector<std::unordered_set<int64_t>> gvids;  
private:  
    std::string name;  
};  
BOOST_HANA_ADAPT_STRUCT(Person, nc, gvids);  
BOOST_HANA_ADAPT_STRUCT(Person::NestedClass, gender, id, vec);  
  
int main() {  
    Person jack("Jack");  
    { // native cpp  
        auto start = clock();  
        for (size_t i = 0; i < 10000000; ++i) {  
            const auto& gvids = jack.gvids;  
            int gender = jack.nc.gender;  
            std::string& id = jack.nc.id;  
            std::vector<double>& vec = jack.nc.vec;  
        }  
        auto end = clock();  
        std::cout << "native cpp time: " << (double)(end - start) / CLOCKS_PER_SEC << std::endl;  
    }  
    { // use hana for reflection  
        auto start = clock();  
        for (size_t i = 0; i < 10000000; ++i) {  
            const auto& gvids = hana::at_key(jack, BOOST_HANA_STRING("gvids"));  
            Person::NestedClass& nc = hana::at_key(jack, BOOST_HANA_STRING("nc"));  
            int gender = hana::at_key(nc, BOOST_HANA_STRING("gender"));  
            std::string& id = hana::at_key(nc, BOOST_HANA_STRING("id"));  
            std::vector<double>& vec = hana::at_key(nc, BOOST_HANA_STRING("vec"));  
        }  
        auto end = clock();  
        std::cout << "hana reflection time: " << (double)(end - start) / CLOCKS_PER_SEC << std::endl;  
    }  
      
    return 0;  
}  
```  
![image](https://user-images.githubusercontent.com/11769871/130730237-cbb93389-a59b-47b7-a549-8121acf503b8.png)  
  
Hello, I don’t know if my test method is accurate, but the running result seems  not match the official description of hana:   
  
> " Indeed, for type-level computations and computations on IntegralConstants, runtime performance is simply not a concern, because the result of the computation is contained in a type, which is a purely compile-time entity."

---

## Comment 1

> Username: badair  
> Created at: 2021-09-22 05:58:47 UTC  
> Updated at: 2021-09-22 05:59:43 UTC  
> Url: https://github.com/boostorg/hana/issues/494#issuecomment-924610571  

Did you compile with optimizations enabled, e.g. `-O3`? Hana is fast when I compile this code with optimizations enabled.

---

## Comment 2

> Username: ldionne  
> Created at: 2022-10-21 03:56:35 UTC  
> Url: https://github.com/boostorg/hana/issues/494#issuecomment-1286425314  

Indeed, I get the same here.  
  
Without optimizations: https://wandbox.org/permlink/xB602DZNrycNvLw5  
With optimizations: https://wandbox.org/permlink/jEPZHBcnjM1oOMOv  
  
TLDR: enable optimizations or you will get terrible performance out of Hana. Hana totally depends on the compiler's ability to peel off layers of abstractions via inlining.
