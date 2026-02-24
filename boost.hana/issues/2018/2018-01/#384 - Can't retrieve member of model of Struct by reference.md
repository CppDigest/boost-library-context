# #384 - Can't retrieve member of model of Struct by reference [Open]

> Username: seertaak  
> Created at: 2018-01-25 15:28:03 UTC  
> Updated at: 2018-01-26 12:28:57 UTC  
> Url: https://github.com/boostorg/hana/issues/384  

For following reflectable Struct  
  
    struct triangles {  
        BOOST_HANA_DEFINE_STRUCT(triangles,  
            (vec<op::triangle_vertex>,  vertices),  
            (vec<op::triangle_index>,   indices)  
        );  
    };  
  
I would like to obtain references to `vertices` and `indices`, but am unable to do using `hana::members` or `hana::accessors`. I am, on the other hand, able to obtain copies of said elements. Here's an example of the code that attempts to extract a const reference to the indices:  
  
    auto maybe_indices_ix = hana::index_if(  
        hana::accessors<cpu_pass_t>(),  
        [] (auto field) {  
            auto t = hana::type_c<  
                typename remove_pointer<  
                    typename decay<  
                        decltype(hana::second(field)(declval<cpu_pass_t>()))  
                    >::type  
                >::type  
            >;  
            return render::traits::is_vector_of_indices(t);  
        }  
    );  
    constexpr auto indices_ix = hana::value(*maybe_indices_ix);  
    constexpr auto accessors = hana::accessors<cpu_pass_t>();  
    constexpr auto indices_accessor = accessors[hana::int_c<indices_ix>];  
    constexpr auto indices_of = hana::second(indices_accessor);  
    const auto& indices = indices_of(triangles);  
  
If I replace the last line with `auto indices = ...` then it works. The same result is obtained if I use `hana::members` as per manual.   
  
Am I doing something wrong? Assuming I'm not, I hope there's a workaround, because copying vectors of vertices and indices would incur an unacceptable performance penalty in such a tight rendering loop.  
  
Thanks for the awesome library by the way!

---

## Comment 1

> Username: seertaak  
> Created at: 2018-01-25 15:34:19 UTC  
> Url: https://github.com/boostorg/hana/issues/384#issuecomment-360502447  

I have another question regarding the code above. If I elide the declaration of `maybe_indices_ix` and substitute the RHS of the declaration into its use in RHS of the declarator of `indices_ix`, I get an error. Do you know why this could be?

---

## Comment 2

> Username: ricejasonf  
> Created at: 2018-01-25 18:40:01 UTC  
> Updated at: 2018-01-25 18:43:40 UTC  
> Url: https://github.com/boostorg/hana/issues/384#issuecomment-360559502  

It would be easier to help if you provide a minimal example that reproduces the error you are getting. It looks like you are using `triangles` as both a type name and the name of a value.  
  
What is the intent that justifies the above complexity?  
  
Why not just do `triangles.indices`?  
  
For your second comment, it sounds like you are not unwrapping the optional or the integral_constant. That's just a guess.

---

## Comment 3

> Username: seertaak  
> Created at: 2018-01-25 21:25:26 UTC  
> Updated at: 2018-01-25 21:55:49 UTC  
> Url: https://github.com/boostorg/hana/issues/384#issuecomment-360605504  

Here's an example:  
  
    #include <cassert>  
    #include <vector>  
    #include <array>  
    #include <boost/hana.hpp>  
  
    using namespace std;  
    namespace hana = boost::hana;  
    using namespace hana::literals;  
    using point = array<float, 2>;  
  
    struct triangles {  
        BOOST_HANA_DEFINE_STRUCT(triangles,  
            (vector<int>, indices),  
            (vector<point>, vertices)  
        );  
    };  
  
    auto example_data = triangles{  
        {0, 1, 2, 1, 2, 3},  
        {  
            {{-0.5f, -0.5f}},  
            {{ 0.5f, -0.5f}},  
            {{ 0.0f,  0.5f}},  
        }  
    };  
  
    void render(const triangles& data) {  
        // in reality, we will find the constant integral  
        // index below by asking: which field is a sequence  
        // of int-like objects (but could be, say, uint8_t,  
        // and maybe it's more appropriately called "elements"  
        // to mirror OpenGL naming).  
        constexpr size_t vertices_ix = 1;  
        const auto& vs = hana::members(data)[hana::int_c<vertices_ix>];  
        assert(vs.size() == 3);  
        const auto& is = hana::members(data)[0_c];  
        assert(is.size() == 6);  
  
        // Render using vs and is. Don't want to copy.  
    }  
  
    int main() {  
        render(example_data);  
        return 0;  
    }  
  
The error message is:  
  
    prog.exe: prog.cc:35: void render(const triangles &): Assertion `vs.size() == 3' failed.  
    Aborted  
    Finish  
  
I ran on wandbox.org using boost 1.66.0 and selected clang 5.0.0 and C++14, which corresponds roughly to my real use. (I'm using Xcode, latest v9.2 [9C40b].) I tried with several combinations of clang and C++ versions, including clang head and C++2a -- all give the same error. On the other hand, compiling with gcc >= 6.3.0 and C++>=14 *succeeded*. Weird.  
  
The online documentation says:  
  
> Reflection can be added to user-defined types very easily. This allows iterating over the members of a user-defined type, querying members with a programmatic interface and much more, without any runtime overhead.  
  
My usage boils down to access of struct fields by index, similar to what e.g. magic-get allows. So I feel like it's not unreasonable to allow const-reference access to data via indirect means.

---

## Comment 4

> Username: ricejasonf  
> Created at: 2018-01-25 22:10:29 UTC  
> Url: https://github.com/boostorg/hana/issues/384#issuecomment-360617473  

The problem is with `hana::members`. The value in the pair is a copy so you are getting a dangling reference exhibiting undefined behaviour.  
  
Use instead `hana::accessors`.  
See this: https://stackoverflow.com/questions/48318957/passing-a-reflection-with-boosthana  
  
As for accessing references by index, I don't see why `Struct` can't refine the concept `Iterable`.  
  
This was also recently discussed as it was apparently a feature in Boost.Fusion.  
Here: https://stackoverflow.com/questions/48394801/what-is-the-equivalent-of-boost-fusion-adapt-assoc-in-boost-hana  
  
@ldionne Would you consider a PR that adds `Iterable` to `Struct`?

---

## Comment 5

> Username: seertaak  
> Created at: 2018-01-26 12:28:57 UTC  
> Url: https://github.com/boostorg/hana/issues/384#issuecomment-360772481  

> Use instead hana::accessors.  
  
I tried that, and if you look at the original code snippet above, you'll see that it uses `hana::accessors` rather than `hana::members`. Unfortunately that didn't work either. I'll see if I can reproduce in wandbox later today.
