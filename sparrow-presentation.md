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

  .circle-img-big {
    width: 200px !important;
    height: 200px !important;
    border-radius: 50% !important;
    object-fit: cover !important;
    display: block !important;
    margin: 0 auto !important;
  }
  
  .circle-img {
    width: 100px !important;
    height: 100px !important;
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
<br>
<br>

![w:500 align-center](resources/logo_scientific_computing.svg)

---
# About

<table class="invisible-table" style="table-layout: fixed">
    <tr>
        <td>
            <img src="resources/Alexis.png" class="circle-img-big">
            <strong>Alexis Placet</strong></br>
            Software Engineer
        </td>
        <td>
            <img src="resources/Johann.png" class="circle-img-big">
            <strong>Johan Mabille</strong></br>
            Project Director
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
- A modern and idiomatic C++20 implementation of the Apache Arrow memory format featuring value semantics, ranges, and iterators ...
- Support for both typed and untyped arrays
- Mutability for typed arrays
- Convenient constructors and builders for easy usage
- Zero dependencies (except when compiling with libc++: P0355R7 Timezone )
- Passes all Apache Arrow integration tests, compatible with formats 1.0 to 1.5
- Built-in std::formatters for all Sparrow types
- Licensed under Apache License v2

---

![bg opacity:.5 ](resources/cosmo_fly.png)
# Demo time !

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
