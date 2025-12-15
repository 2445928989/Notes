我使用了如下的代码进行测试：

```cpp
#include <cstdint>
#include <cassert>
#include <iostream>
#include <algorithm>
#include <vector>
#include <list>
#include <queue>
#include <cstdint>
#include <iostream>
#include <algorithm>
#include <vector>
#include <unordered_set>
#include <unordered_map>
#include <set>
#include <list>
#include <queue>
#include <map>
#include <vector>

uint64_t memory_usage;

// use this when calling STL object if you want
// to keep track of memory usage
template <class T>
class MemoryCountingAllocator
{
public:
    // type definitions
    typedef T value_type;
    typedef T *pointer;
    typedef const T *const_pointer;
    typedef T &reference;
    typedef const T &const_reference;
    typedef std::size_t size_type;
    typedef std::ptrdiff_t difference_type;

    // rebind allocator to type U
    template <class U>
    struct rebind
    {
        typedef MemoryCountingAllocator<U> other;
    };

    pointer address(reference value) const
    {
        return &value;
    }
    const_pointer address(const_reference value) const
    {
        return &value;
    }

    MemoryCountingAllocator() : base() {}
    MemoryCountingAllocator(const MemoryCountingAllocator &) : base() {}
    template <typename U>
    MemoryCountingAllocator(const MemoryCountingAllocator<U> &) : base() {}
    ~MemoryCountingAllocator() {}

    // return maximum number of elements that can be allocated
    size_type max_size() const throw()
    {
        return base.max_size();
    }

    pointer allocate(size_type num, const void *p = 0)
    {
        memory_usage += num * sizeof(T);
        return base.allocate(num, p);
    }

    void construct(pointer p, const T &value)
    {
        return base.construct(p, value);
    }

    // destroy elements of initialized storage p
    void destroy(pointer p)
    {
        base.destroy(p);
    }

    // deallocate storage p of deleted elements
    void deallocate(pointer p, size_type num)
    {
        memory_usage -= num * sizeof(T);
        base.deallocate(p, num);
    }
    std::allocator<T> base;
};

// for our purposes, we don't want to distinguish between allocators.
template <class T1, class T2>
bool operator==(const MemoryCountingAllocator<T1> &, const T2 &) throw()
{
    return true;
}

template <class T1, class T2>
bool operator!=(const MemoryCountingAllocator<T1> &, const T2 &) throw()
{
    return false;
}

void initializeMemUsageCounter()
{
    memory_usage = 0;
}

uint64_t getMemUsageInBytes()
{
    return memory_usage;
}

typedef std::set<uint32_t, std::less<uint32_t>, MemoryCountingAllocator<uint32_t>> treeset;
typedef std::unordered_set<uint32_t, std::hash<uint32_t>, std::equal_to<uint32_t>, MemoryCountingAllocator<uint32_t>> hashset;
typedef std::vector<uint32_t, MemoryCountingAllocator<uint32_t>> vector;
typedef std::list<uint32_t, MemoryCountingAllocator<uint32_t>> list;
typedef std::deque<uint32_t, MemoryCountingAllocator<uint32_t>> deque;
typedef std::unordered_map<uint32_t, uint32_t, std::hash<uint32_t>, std::equal_to<uint32_t>, MemoryCountingAllocator<std::pair<const uint32_t, uint32_t>>> hashmap;
typedef std::map<uint32_t, uint32_t, std::less<uint32_t>, MemoryCountingAllocator<std::pair<const uint32_t, uint32_t>>> mymap;

int main()
{
    size_t N = 100000;
    std::cout << N << " 个元素" << std::endl;
    initializeMemUsageCounter();
    assert(getMemUsageInBytes() == 0);

    vector v;
    for (uint32_t k = 0; k < N; k++)
        v.push_back(k);
    std::cout << "vector\t\t每个元素的内存使用量 : " << getMemUsageInBytes() * 1.0 / N << std::endl;

    initializeMemUsageCounter();
    assert(getMemUsageInBytes() == 0);

    list l;
    for (uint32_t k = 0; k < N; k++)
        l.push_back(k);
    std::cout << "list\t\t每个元素的内存使用量 : " << getMemUsageInBytes() * 1.0 / N << std::endl;

    initializeMemUsageCounter();
    assert(getMemUsageInBytes() == 0);

    deque dq;
    for (uint32_t k = 0; k < N; k++)
        dq.push_back(k);
    std::cout << "deque\t\t每个元素的内存使用量 : " << getMemUsageInBytes() * 1.0 / N << std::endl;

    initializeMemUsageCounter();
    assert(getMemUsageInBytes() == 0);
    hashset h;
    for (uint32_t k = 0; k < N; k++)
        h.insert(k);
    std::cout << "unordered_set\t每个元素的内存使用量 : " << getMemUsageInBytes() * 1.0 / N << std::endl;

    initializeMemUsageCounter();
    assert(getMemUsageInBytes() == 0);
    treeset t;
    for (uint32_t k = 0; k < N; k++)
        t.insert(k);
    std::cout << "set\t\t每个元素的内存使用量 : " << getMemUsageInBytes() * 1.0 / N << std::endl;

    initializeMemUsageCounter();
    assert(getMemUsageInBytes() == 0);
    hashmap m;
    for (uint32_t k = 0; k < N; k++)
        m[k] = k;
    std::cout << "unordered_map\t每个元素的内存使用量 : " << getMemUsageInBytes() * 1.0 / N << std::endl;

    initializeMemUsageCounter();
    assert(getMemUsageInBytes() == 0);
    mymap mm;
    for (uint32_t k = 0; k < N; k++)
        mm[k] = k;
    std::cout << "map\t\t每个元素的内存使用量 : " << getMemUsageInBytes() * 1.0 / N << std::endl;
}
```

测试的基准默认都是`uint32_t`类型，测试1个、1e3个、1e6个元素，结果分别如下

```
1 个元素
vector		每个元素的内存使用量 : 4
list		每个元素的内存使用量 : 24
deque		每个元素的内存使用量 : 576
unordered_set	每个元素的内存使用量 : 120
set		每个元素的内存使用量 : 40
unordered_map	每个元素的内存使用量 : 120
map		每个元素的内存使用量 : 40

1000 个元素
vector		每个元素的内存使用量 : 4.096
list		每个元素的内存使用量 : 24
deque		每个元素的内存使用量 : 4.24
unordered_set	每个元素的内存使用量 : 24.872
set		每个元素的内存使用量 : 40
unordered_map	每个元素的内存使用量 : 24.872
map		每个元素的内存使用量 : 40

1000000 个元素
vector		每个元素的内存使用量 : 4.1943
list		每个元素的内存使用量 : 24
deque		每个元素的内存使用量 : 4.16408
unordered_set	每个元素的内存使用量 : 27.5772
set		每个元素的内存使用量 : 40
unordered_map	每个元素的内存使用量 : 27.5772
map		每个元素的内存使用量 : 40
```

可以观察到，实际上`vector`与`deque`在均摊下来后每个元素的占用空间是和数据本身差不多的，但`deque`因为要管理碎片化内存有一个非常大的常数(600B左右)，而`set`和`map`这样基于树形结构的STL需要额外的空间来存储元素，每个元素有一个大概40B的常数，`unordered_map`和`unordered_set`这样基于哈希表的数据结构，会有一个内置的哈希表，所以也存在一定的常数复杂度。

需要注意的省流就是：
1. `vector`的复杂度是不需要担心的，与数组是差不多的，`priority_queue`的底层也是`vector`，他们的空间复杂度表现应该是一致的，同时还需要注意3维及以上的`vector`寻址速度会下降，尽量不要开高维`vector`
2. `stack`、`queue`是基于`deque`的，这三种STL在初始化的时候会有一个大概600B的常数，因此要**注意不要多次初始化**，如果一定要开太多次，可以用`stack<int,vector<int>>`来初始化，这样底层可以被替换成`vector`，但可能会因为`vector`的复制扩容特性导致有额外的常数时间复杂度
3. `map`、`set`这样基于树形结构的STL在存储每个元素的时候都会有一个40B左右的常数
4. `unordered_map`、`unordered_set`有内置哈希表，初始化的时候会有一个120B左右的常数，同第2条，**注意不要多次初始化**
