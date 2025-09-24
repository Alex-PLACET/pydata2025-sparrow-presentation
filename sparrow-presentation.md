---
marp: true
lang: en
paginate: true

style: |
  section {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
    padding-top: 50px;
  }
  
  section.centered {
    display: flex;
    flex-direction: column;
    justify-content: center;
    text-align: center;
  }
  
  pre {
    font-size: 24px !important;
    line-height: 1.4 !important;
    margin: 10px 0 !important;
    width: 100% !important;
    max-width: 100% !important;
    box-sizing: border-box !important;
    overflow-x: auto !important;
  }
  
  code {
    font-size: 24px !important;
    font-family: Consolas, Monaco, monospace !important;
  }
  
  pre code {
    font-size: 24px !important;
    white-space: pre !important;
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


![bg opacity:.1 ](resources/cosmo_victory.png)

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
<!-- ![bg width:600px opacity:.5 right](resources/cosmo_rocket.png) -->
<table class="invisible-table" style="table-layout: fixed">
    <tr>
        <td>
            <img src="resources/Alexis.png" class="circle-img">
            <div style="text-align: center;"><strong>Alexis Placet</strong></div>
            <div style="text-align: center;">Software Engineer</div>
        </td>
        <td>
            <img src="resources/Johann.png" class="circle-img">
            <div style="text-align: center;"><strong>Johan Mabille</strong></div>
            <div style="text-align: center;">Project Director</div>
        </td>
    </tr>
</table>

---
# Why Sparrow ?
- Apache Arrow's tabular format has become the industry standard
- Apache Arrow is a huge monorepo with many dependencies, while some projects only need the memory format implementation (e.g., ArcticDB)
- Apache Arrow started more than 10 years ago, and C++20 brought new features that we can leverage in a fresh, modern project

---
# What is Sparrow ?
- A modern C++20 implementation of the Apache Arrow memory format
- Idiomatic C++20 API featuring value semantics, ranges, and iterators
- Support for both typed and untyped arrays
- Full mutability for typed arrays
- Convenient constructors and builders for easy usage
- Zero dependencies (except when compiling with libc++: P0355R7 Timezone )
- Passes all Apache Arrow integration tests, compatible with formats 1.0 to 1.5
- Built-in std::formatters for all Sparrow types
- Licensed under Apache License v2

---

# Nullable Values

```cpp
#include <sparrow/sparrow.hpp>
namespace sp = sparrow;
sp::nullable<int> n1;
sp::nullable<int> n2 = 42;
sp::nullable<int> n3 = sp::make_nullable(7, false);
``` 

---

# Typed Array Construction

```cpp
#include <sparrow/sparrow.hpp>
namespace sp = sparrow;

std::vector<int32_t> values = { 1, 3, 5, 6, 9 };
sp::primitive_array<int32_t> ar = values;
std::vector<bool> validity { true, true, false, true, true };
sp::primitive_array<int32_t> ar{values, validity, "my_array", some_metadata};

sp::u8buffer<int32_t> buffer{values};
```

```cpp
static_assert(ar.size() == 5);
static_assert(ar.front() == 1);
static_assert(ar.back() == 9);
ar[3] = 0;
ar[4] = make_nullable(7, false);
ar[4].get() = 10;
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



zero_null_values() To wipe out the values of null elements.

---

# Array Operations

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

---

# Untyped Arrays

```cpp
sp::array ar(std::move(array), std::move(schema));

ar.visit([]<class T>(const T& typed_ar)
{
    if constexpr (sp::is_primitive_array_v<T>)
    {
        std::for_each(typed_ar.cbegin(), typed_ar.cend(), [](const auto& val)
        {
            std::cout << val;
        });
    }
    // else if constexpr ...
});
```

---

# Record Batch

```cpp
sp::primitive_array<std::uint16_t> ar_int(
    std::vector<std::uint16_t>{ 1, 3, 5, 6, 9 },
    validity_bitmap{},
    "my_primitives",
    some_metadata);

sp::string_array ar_str(
    std::vector<std::string>{ "one", "three", "five", "six", "nine" },
    validity_bitmap{},
    "my_strings",
    some_metadata);

std::vector<array> arr_list{std::move(ar_int), std::move(ar_str)};

sp::record_batch rb(std::move(arr_list), "my_record_batch", some_metadata);
```

---

# Architecture

---

# What's Next

- Aligned allocations
- sparrow-ipc
- Mutability for unions, run end encoded, binary view, list and list view arrays
- Sparrow tables
- Computation kernels

---

# Thanks!

- Github: https://github.com/man-group/sparrow 
- Available on CondaForge, VCPKG and Conan
- Presentation: TODO
<table class="invisible-table" style="table-layout: fixed">
  <tr>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Alexis.png" class="circle-img">
      <strong>Alexis Placet</strong>
    </td>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Johann.png" class="circle-img">
      <strong>Johan Mabille</strong>
    </td>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Thorsten.png" class="circle-img">
      <strong>Thorsten Beier</strong>
    </td>
  </tr>
  <tr>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Joël.png" class="circle-img">
      <strong>Joël Lamotte</strong>
    </td>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Julien.png" class="circle-img">
      <strong>Julien Jerphanion</strong>
    </td>
    <td style="text-align: center; width: 200px;">
      <img src="resources/Hind.png" class="circle-img">
      <strong>Hind Montassif</strong>
    </td>
  </tr>
</table>
