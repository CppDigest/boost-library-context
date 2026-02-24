# #66 - Move semantics is required for static_pointer_cast and intrusive_ptr [Closed]

> Username: Klayflash  
> Created at: 2019-04-14 16:31:17 UTC  
> Updated at: 2019-04-22 11:42:35 UTC  
> Closed at: 2019-04-21 01:38:32 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/66  

static_pointer_cast does not support move semantics for intrusive_ptr.  
See "FAILS" line bellow in example.  
  
  
```  
#include <boost/smart_ptr/intrusive_ptr.hpp>  
#include <boost/noncopyable.hpp>  
  
int g_AddrefCount=0;  
int g_ReleaseCount=0;  
int g_DeleteCount=0;  
  
class SomeBaseClass  
  : public boost::noncopyable  
  
{  
public:  
  virtual ~SomeBaseClass() {}  
  void addRef()  
  {  
    ++g_AddrefCount;  
    ++m_Count;  
  }  
  void release()  
  {  
    assert(m_Count>0);  
    ++g_ReleaseCount;  
    --m_Count;  
    if (m_Count==0) {  
      ++g_DeleteCount;  
      delete this;  
    }  
  }  
public:  
  int m_Count=1;  
};  
typedef boost::intrusive_ptr<SomeBaseClass> SomeBaseClassIPtr;  
  
inline void intrusive_ptr_add_ref(SomeBaseClass* r)  
{  
  r->addRef();  
}  
inline void intrusive_ptr_release(SomeBaseClass* r)  
{  
  r->release();  
}  
  
  
class SomeDerivedClass  
  : public SomeBaseClass  
{  
};  
typedef boost::intrusive_ptr<SomeDerivedClass> SomeDerivedClassIPtr;  
  
  
int main()  
{  
  // ensure that example code works as expected  
  {  
    {  
      SomeBaseClassIPtr pBase(new SomeDerivedClass(),false);  
      assert(g_AddrefCount==0); assert(g_ReleaseCount==0); assert(g_DeleteCount == 0);  
      SomeBaseClassIPtr pBase1 = pBase;  
      assert(g_AddrefCount == 1); assert(g_ReleaseCount == 0); assert(g_DeleteCount == 0);  
    }  
    assert(g_AddrefCount == 1); assert(g_ReleaseCount == 2); assert(g_DeleteCount == 1);  
    g_AddrefCount = 0; g_ReleaseCount = 0; g_DeleteCount = 0;  
  }  
  // without move semantics  
  {  
    {  
      SomeBaseClassIPtr pBase(new SomeDerivedClass(), false);  
      assert(g_AddrefCount == 0); assert(g_ReleaseCount == 0); assert(g_DeleteCount == 0);  
      SomeDerivedClassIPtr pDerived = boost::static_pointer_cast<SomeDerivedClass>(pBase);  
      assert(g_AddrefCount == 1); assert(g_ReleaseCount == 0); assert(g_DeleteCount == 0);  
    }  
    assert(g_AddrefCount == 1); assert(g_ReleaseCount == 2); assert(g_DeleteCount == 1);  
    g_AddrefCount = 0; g_ReleaseCount = 0; g_DeleteCount = 0;  
  }  
  // handmade move semantics  
  {  
    {  
      SomeBaseClassIPtr pBase(new SomeDerivedClass(), false);  
      assert(g_AddrefCount == 0); assert(g_ReleaseCount == 0); assert(g_DeleteCount == 0);  
      SomeDerivedClassIPtr pDerived(static_cast<SomeDerivedClass*>(pBase.detach()),false);  
      assert(g_AddrefCount == 0); assert(g_ReleaseCount == 0); assert(g_DeleteCount == 0);  
    }  
    assert(g_AddrefCount == 0); assert(g_ReleaseCount == 1); assert(g_DeleteCount == 1);  
    g_AddrefCount = 0; g_ReleaseCount = 0; g_DeleteCount = 0;  
  }  
  // expected automatic move semantics  
  {  
    {  
      SomeBaseClassIPtr pBase(new SomeDerivedClass(), false);  
      assert(g_AddrefCount == 0); assert(g_ReleaseCount == 0); assert(g_DeleteCount == 0);  
      SomeDerivedClassIPtr pDerived(boost::static_pointer_cast<SomeDerivedClass>(std::move(pBase)));  
      assert(g_AddrefCount == 0); assert(g_ReleaseCount == 0); assert(g_DeleteCount == 0); // <--- FAILS  
    }  
    assert(g_AddrefCount == 0); assert(g_ReleaseCount == 1); assert(g_DeleteCount == 1);  
    g_AddrefCount = 0; g_ReleaseCount = 0; g_DeleteCount = 0;  
  }  
  return 0;  
}  
```

---

## Comment 1

> Username: Klayflash  
> Created at: 2019-04-14 20:15:18 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/66#issuecomment-483054397  

Proposed solution:  
```  
namespace boost  
{  
  
template<class T, class U> intrusive_ptr<T> static_pointer_cast(intrusive_ptr<U> && p)  
{  
  return intrusive_ptr<T>(static_cast<T *>(p.detach()),false);  
}  
  
}  
```

---

## Comment 2

> Username: Klayflash  
> Created at: 2019-04-22 11:42:35 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/66#issuecomment-485398790  

Thanks!
