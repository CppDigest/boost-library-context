# #464 - error C2039: 'ob_refcnt': is not a member of '_object' [Closed]

> Username: shelllet  
> Created at: 2024-12-18 01:47:07 UTC  
> Updated at: 2025-03-10 19:10:13 UTC  
> Closed at: 2025-03-10 19:10:13 UTC  
> Url: https://github.com/boostorg/python/issues/464  

boost\libs\python\src\converter\from_python.cpp  
  
````  
  void* lvalue_result_from_python(  
      PyObject* source  
      , registration const& converters  
      , char const* ref_type)  
  {  
      handle<> holder(source);  
      if (source->ob_refcnt <= 1)  // error C2039: 'ob_refcnt': is not a member of '_object'  
      {  
          handle<> msg(  
#if PY_VERSION_HEX >= 0x3000000  
              ::PyUnicode_FromFormat  
#else  
              ::PyString_FromFormat  
#endif  
                  (  
                  "Attempt to return dangling %s to object of type: %s"  
                  , ref_type  
                  , converters.target_type.name()));  
            
          PyErr_SetObject(PyExc_ReferenceError, msg.get());  
  
          throw_error_already_set();  
      }  
        
      void* result = get_lvalue_from_python(source, converters);  
      if (!result)  
          (throw_no_lvalue_from_python)(source, converters, ref_type);  
      return result;  
  }  
````  
  
cpython version: 3.13  
  
cpython\Include\object.h  
  
the `_object` memter variable `ob_refcnt` does not exist when build cpython with `Py_GIL_DISABLED` defined,.  
  
````  
#ifndef Py_GIL_DISABLED  
struct _object {  
#if (defined(__GNUC__) || defined(__clang__)) \  
        && !(defined __STDC_VERSION__ && __STDC_VERSION__ >= 201112L)  
    // On C99 and older, anonymous union is a GCC and clang extension  
    __extension__  
#endif  
#ifdef _MSC_VER  
    // Ignore MSC warning C4201: "nonstandard extension used:  
    // nameless struct/union"  
    __pragma(warning(push))  
    __pragma(warning(disable: 4201))  
#endif  
    union {  
       Py_ssize_t ob_refcnt;  
#if SIZEOF_VOID_P > 4  
       PY_UINT32_T ob_refcnt_split[2];  
#endif  
    };  
#ifdef _MSC_VER  
    __pragma(warning(pop))  
#endif  
  
    PyTypeObject *ob_type;  
};  
#else  
// Objects that are not owned by any thread use a thread id (tid) of zero.  
// This includes both immortal objects and objects whose reference count  
// fields have been merged.  
#define _Py_UNOWNED_TID             0  
  
// The shared reference count uses the two least-significant bits to store  
// flags. The remaining bits are used to store the reference count.  
#define _Py_REF_SHARED_SHIFT        2  
#define _Py_REF_SHARED_FLAG_MASK    0x3  
  
// The shared flags are initialized to zero.  
#define _Py_REF_SHARED_INIT         0x0  
#define _Py_REF_MAYBE_WEAKREF       0x1  
#define _Py_REF_QUEUED              0x2  
#define _Py_REF_MERGED              0x3  
  
// Create a shared field from a refcnt and desired flags  
#define _Py_REF_SHARED(refcnt, flags) (((refcnt) << _Py_REF_SHARED_SHIFT) + (flags))  
  
struct _object {  
    // ob_tid stores the thread id (or zero). It is also used by the GC and the  
    // trashcan mechanism as a linked list pointer and by the GC to store the  
    // computed "gc_refs" refcount.  
    uintptr_t ob_tid;  
    uint16_t _padding;  
    PyMutex ob_mutex;           // per-object lock  
    uint8_t ob_gc_bits;         // gc-related state  
    uint32_t ob_ref_local;      // local reference count  
    Py_ssize_t ob_ref_shared;   // shared (atomic) reference count  
    PyTypeObject *ob_type;  
};  
#endif  
````
