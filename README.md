# MSVC, Building a library

### Using libraries and why it matters.

**Wikipedia** : "The value of a library lies in the reuse of standardized program elements. When a program invokes a library, it gains the behavior implemented inside that library without having to implement that behavior itself. Libraries encourage the sharing of code in a modular fashion and ease the distribution of the code."

#### Project
Example details are grouped into subject related points and showcase investigation result on the subject of libraries. Further details will be provided on more advance features that are covered in the making of our examples.

#### What this README.md is not
Be aware that some of the tooling used in the making of this `Demo` project won't be covered here. External documentations will be provided for your own benefit, which in most case, are also where most of the information mentioned here will be coming from.

### What's a library
* Static
* Dynamic
* Advantages of one over the other
* Implication at the executable level
* How to build one
* the Implications with the Linker
* HOw your OS query dynamic libraries

#### A Simple HelloWorld.cc
```
// some example code
```

##### SYMBOLS
**__cdecl** 

##### DUMPBIN

* https://learn.microsoft.com/en-us/cpp/cpp/extern-cpp?view=msvc-170
* https://learn.microsoft.com/en-us/cpp/cpp/cdecl?view=msvc-170
* https://learn.microsoft.com/en-us/windows/win32/dlls/dynamic-link-libraries
* https://learn.microsoft.com/en-us/cpp/build/dlls-in-visual-cpp?view=msvc-170
* dll with entry point vs no entry point
