# #4 - Consider adding default_qvm_traits to namespace boost::qvm [Closed]

> Username: federkamm  
> Created at: 2016-11-03 12:40:28 UTC  
> Updated at: 2016-12-24 23:27:50 UTC  
> Closed at: 2016-12-24 23:27:50 UTC  
> Url: https://github.com/boostorg/qvm/issues/4  

I found the following code to be useful in my projects  
  
    template <typename Q, typename S> struct default_quat_traits {  
        using quaternion_type = Q;  
        using scalar_type = S;  
  
        template <int I> static scalar_type read_element(quaternion_type const & q) {  
          return quat_traits<quaternion_type>::template write_element<I>(const_cast<quaternion_type &>(q));  
        }  
    };  
  
    template <typename V, typename S, int d> struct default_vec_traits {  
        using vector_type = V;  
        using scalar_type = S;  
        static constexpr int const dim = d;  
  
        static inline scalar_type read_element_idx (int I, vector_type const & v) {  
          return vec_traits<vector_type>::write_element_idx(I, const_cast<vector_type &>(v));  
        }  
        template <int I> static scalar_type & write_element(vector_type & v) {  
          static_assert(0 <= I && I < dim, "out of range");  
          return vec_traits<vector_type>::write_element_idx(I, v);  
        }  
        template <int I> static scalar_type read_element (vector_type const & v) {  
          return write_element<I>(const_cast<vector_type &>(v));  
        }  
    };  
  
    template <typename M, typename S, int r, int c> struct default_mat_traits {  
        using matrix_type = M;  
        using scalar_type = S;  
        static constexpr int const rows = r;  
        static constexpr int const cols = c;  
  
        static inline scalar_type read_element_idx (int R, int C, matrix_type const & m) {  
          return mat_traits<matrix_type>::write_element_idx(R, C, const_cast<matrix_type &>(m));  
        }  
        template <int R, int C> static scalar_type & write_element(matrix_type & m) {  
          static_assert(0 <= R && R < rows && 0 <= C && C < cols, "out of range");  
          return mat_traits<matrix_type>::write_element_idx(R, C, m);  
        }  
        template <int R, int C> static scalar_type read_element (matrix_type const & m) {  
          return write_element<R, C>(const_cast<matrix_type &>(m));  
        }  
    };  
  
It eases the instantiation of new qvm types:  
  
    using my_quat = std::tuple<double, double, double, double>;  
    template <> struct quat_traits<my_quat> : default_quat_traits<my_quat, double> {  
        template <int I> static scalar_type & write_element(quaternion_type & q) { // must not modify q  
          return std::get<I>(q);  
        }  
    };  
  
    struct my_vec { double x, y, z; };  
    template <> struct vec_traits<my_vec> : default_vec_traits<my_vec, double, 3> {  
        static inline scalar_type & write_element_idx(int i, vector_type & v) { // must not modify v  
          switch (i) {  
            case 0: return v.x;  
            case 1: return v.y;  
            case 2: return v.z;  
          }  
          throw std::out_of_range("");  
        }  
    };  
  
    struct my_mat { double a, b, c, d; };  
    template <> struct mat_traits<my_mat> : default_mat_traits<my_mat, double, 2, 2>  
    {  
        static inline scalar_type & write_element_idx(int r, int c, matrix_type & m) { // must not modify m  
          if (0 <= r && r < rows) {  
            switch (r + c * rows) {  
              case 0: return m.a;  
              case 1: return m.b;  
              case 2: return m.c;  
              case 3: return m.d;  
            }  
          }  
          throw std::out_of_range("");  
        }  
    };  
  
I think most compiler should be able to generate optimal code from this.  
  
Please think about if it is possible to add default_qvm_traits to boost::qvm.

---

## Comment 1

> Username: zajo  
> Created at: 2016-12-24 23:27:50 UTC  
> Url: https://github.com/boostorg/qvm/issues/4#issuecomment-269104060  

See quat/vec/mat_traits_defaults in the develop branch.
