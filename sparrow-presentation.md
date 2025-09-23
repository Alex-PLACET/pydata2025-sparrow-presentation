---
marp: true
lang: en
paginate: true

style: |
  section.centered {
    display: flex;
    flex-direction: column;
    justify-content: center;
    text-align: center;
  }
  
  .circle-img {
    width: 120px !important;
    height: 120px !important;
    border-radius: 50% !important;
    object-fit: cover !important;
    display: block !important;
    margin: 0 auto !important;
  }
  
  .invisible-table {
    border: none !important;
    border-collapse: collapse !important;
    background: transparent !important;
    margin: 0 auto !important;
    border-spacing: 0 !important;
  }
  
  .invisible-table td {
    border: none !important;
    border-top: none !important;
    border-bottom: none !important;
    border-left: none !important;
    border-right: none !important;
    background: transparent !important;
    padding: 20px !important;
  }
  
  .invisible-table tr {
    border: none !important;
    background: transparent !important;
  }
  
---
<!-- _class: centered -->

<!-- ![bg](resources/cosmo_fly.png) -->

<h1>Sparrow, Pirates of the Apache Arrow</h1>
<h2>PyData Paris 2025</h2>
<h3>2025-09-30</h3>
<h4>Alexis Placet<br>
Johan Mabille</h4>
<!-- <div style="text-align: center;">
  <img src="resources/logo_scientific_computing.svg" width="600">
</div> -->

![w:500 align-bottom-centered](resources/logo_scientific_computing.svg)

---

# About

- QuantStack is a company specializing in open-source software for data science and scientific computing.

- Alexis Placet
- Johan Mabille

---
# Why Sparrow
- Apache Arrow tabular format that became a standard
- Huge monorepo, with a lot dependencies wihl some projects only need the memory format implementation (ArcticDB)
- Apache Arrow started more than 10 years ago, and C++20 brought features that we could leverage on in a new freshly strated project

---
# What is Sparrow
- A C++20 implementation of the Apache Arrow memory format
- C++20 idiomatic API: value semantics, ranges, iterators...
- Typed and untyped arrays
- Mutability of typed arrays
- Convenient constructors and builders
- Zero dependencies (except if you compile with libc++: P0355R7)
- Pass full tests of Apache Arrow integration tests, compatible with formats from 1 to 1.5
- std::formatters for all Sparrow types
- Apache License v2

---

# Exemple of usage

```cpp
#include <sparrow/sparrow.hpp>
namespace sp = sparrow;

sp::primitive_array<int32_t> ar = { 1, 3, 5, 6, 9 };
static_assert(ar.size() == 5);
static_assert(ar.front() == 1);
static_assert(ar.back() == 9);
ar[3] = 0;
ar[4] = make_nullable(7, false);
static_assert(ar[4].has_value() == false);
static_assert(ar[4].get() == 7); // still 7
static_assert(ar[4].value_or(42) == 42);
try {
  ar.at(3); // throws
} catch(...) {
}
ar.push_back(11);
ar.pop_back();
ar.insert(ar.begin() + 2, 4);
ar.resize(20, sp::make_nullable(0, false)); 

for(const auto& v : ar) {
  std::cout << v << " ";
}
```

sparrow::nullable : Similar to std::optional but can hold a reference and does not call the destructor when set to null.

zero_null_values() To wipe out the values of null elements.

---


```cpp
ar.bitmap();
ar.values();
```
```cpp
ar.name();
ar.metadata();
```

```cpp
sp::primitive_array<int32_t> ar_slice = ar.slice(3, 5);
sp::primitive_array<int32_t> ar_slice_view = ar.slice_view(3, 5);
```

```cpp
ArrowArray* array_ptr = get_arrow_array(ar);
ArrowSchema* schema_ptr = get_arrow_schema(ar);
auto [arrow_array, arrow_schema] = sp::get_arrow_structures(ar);

ArrowArray array = extract_arrow_array(arr);
ArrowSchema schema = extract_arrow_schema(arr);
auto [arrow_array, arrow_schema] = extract_arrow_structures(arr);
```

```cpp
sp::array ar(std::move(array), std::move(schema));
ar.visit([]<class T>(const T& typed_ar)
{
    if constexpr (sp::is_primitive_array_v<T>)
    {
        std::for_each(typed_ar.cbegin(), typed_ar.cend(), [](const auto& val)
        {
            if (val.has_value())
            {
                std::cout << val.value();
            }
            else
            {
                std::cout << "null";
            }
            std::cout << ", ";
        });
    }
    // else if constexpr ...
});
```

---
# Record batch

```cpp
sp::primitive_array<std::uint16_t> ar = { 1, 3, 5, 6, 9 };
sp::string_array 
```

---
# Architecture

---
# What's next

- aligned allocations
- sparrow-ipc
- mutability for unions, run end encoded, binary view, list and list view arrays
- sparrow tables
- computation kernels

---

# Thanks !
<table class="invisible-table" style="table-layout: fixed; width: 600px;">
  <tr>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Alexis.png" class="circle-img"><br>
      <strong>Alexis Placet</strong>
    </td>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Johann.png" class="circle-img"><br>
      <strong>Johan Mabille</strong>
    </td>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Thorsten.png" class="circle-img"><br>
      <strong>Thorsten Beier</strong>
    </td>
  </tr>
  <tr>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Joël.png" class="circle-img"><br>
      <strong>Joël Lamotte</strong>
    </td>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Julien.png" class="circle-img"><br>
      <strong>Julien Jerphanion</strong>
    </td>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Hind.png" class="circle-img"><br>
      <strong>Hind Montassif</strong>
    </td>
  </tr>
</table>

- Github: https://github.com/man-group/sparrow
- Presentation: TODO
- Live version: TODO