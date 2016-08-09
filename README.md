#This branch is used to test -finstrument=mock compiler extension

##Diff
https://github.com/martong/llvm/compare/finstrument_mock_0...martong:finstrument_mock_test?expand=1

##Build on OSX

Copy the compiler-rt lib (mock_san) to /usr/local/lib, so
dynamic linker could find it.
Note the following did not really work:
```
export DYLD_LIBRARY_PATH=/Users/mg/WORK/finstrument_mock/rt/finstrument_mock/build.release/compiler-rt/
export DYLD_FALLBACK_LIBRARY_PATH=/Users/mg/WORK/finstrument_mock/rt/finstrument_mock/build.release/compiler-rt/
```

```
mkdir build.mock
cd build.mock
```

Set up the new compiler. In the CMakeLists.txt file the compile flags and linker flags already set up.
```
cmake -G Ninja -DCMAKE_OSX_ARCHITECTURES=x86_64 -DCMAKE_BUILD_TYPE=Release -DCMAKE_EXPORT_COMPILE_COMMANDS=1 -DLLVM_BUILD_TESTS=1 ../git/llvm -DCMAKE_CXX_COMPILER=/Users/mg/Work/finstrument_mock/compiler/build.release/bin/clang++ -DCMAKE_C_COMPILER=/Users/mg/Work/finstrument_mock/compiler/build.release/bin/clang
```
-DCMAKE_OSX_ARCHITECTURES might not be needed if we build the compiler-rt to support
i386 too.
Otherwise the default build would build i386 into the clang libs, which will fail if
compiler-rt does not conatin i386 symbols.

Execute the tests:
```
cd build.mock/unittest
for i in $(find . -perm +111 -type f); do; ./$i 2>&1; done | ag "PASSED|test cases ran"
cd build.mock/tools/clang/unittests
for i in $(find . -perm +111 -type f); do; ./$i 2>&1; done | ag "PASSED|test cases ran"
```

