# g++ Cheat Sheet
Exploring how to use g++ to manually build internal and external libraries. Also some basic Makefile exploration.

## Reference:
| **key** | **description** |
| --- | --- |
| `<something>` | replace with something |

### General Form:
```zsh
g++ TARGET SRC.cpp BIN.o -I/PATH/HEADER.hpp -L/PATH/LIB.dylib -lLIB_NAME -F/PATH/NAME.framework -framework FRAMEWORK_NAME
```

### File Extensions:
| **extensions** | **description** |
| --- | --- |
| `.cpp` | c++ source |
| `.c` | c source |
| `.hpp` | c++ header |
| `.h` | c header |
| `.i` | pre-processed code |
| `.s` | assembly code |
| `.dot` | a diagram of the syntax tree |
| `.o` | object file |
| `.dylib` | Macs dynamic library format |
| `.dll` | Microsofts dynamic library format |
| `.so` | Linuxs dynamic library format |
| `.exe` | executable |
| `.framework`| framework file for macs |

### Important Flags To Know:
| **flags** | **description** |
| --- | --- |
| `-E` | reveals the `.i` file(s) or pre-processed file(s). |
| `-S <.cpp file(s)>` | reveals the `.s` file(s) or assembly file(s) |
| `-c <.cpp-file(s)>` | converts the `.cpp` file(s) into `.o` file(s). |
| `-t` | changes the details seen from the `objdump` program. |
| `-I/<path>` | specifies a non stardard path to search for includes. |
| `-l<sfml-window>` | implicit library to be found in the stardard path |
| `-L/<path>` | explicit path to library. |
| `-o <app> <.o file(s)>` | links the `.o` file(s) into an executable. |
| `-fdump-tree-all-graph` | reveals the `.dot` file(s) for syntax trees. |
| `-g` | reveals debugging information. |
| `-Wall` | reveals most warnings. |
| `-O2` | turns on optimization. |
| `-E` | reveals what the pre-processor is doing. |
| `-framework <name>`| adds the framework if found in default search paths. |
| `-F/<framework path>`| specifies an additional search path for frameworks. |

### The Tools/Programs:
| **tools** | **description** |
| --- | --- |
| `g++` |  invokes the gcc tools. |
| `xdot <.dot file(s)>` | invokes the syntax tree viewing. |
| `objdump <.o file(s)>` | shows what the object file(s) look like. |
| `ldd <.exe>` | reveals the standard paths being searched for libraries |

### Misc.
| **flags** | **description** |
| --- | --- |
| `-Wall` | show all warnings. It turns on all standard C++ warnings about code that might cause unexpected or undefined behavior. |
| `-g` | provides debugging feature for your program. You will need this when you want to use gdb (debugger) or valgrind (memory detection tools). |
| `--std=c++17` | specifies the version of c++ to the 2017 standards. Replace 17 with the deisired version. |
| `Werror` | make all warnings into errors. Do this before submitting your code to avoid getting points deducted! |
| `-pedantic` | issues all warnings demanded by strict ISO C++ rules if you want to be extra safe. |
| `-Wextra` | enables some extra warnings not turned on by -Wall. These include warnings for bad pointer to integer zero comparisons, base class not initialized in copy constructor of derived class, etc. |
| `-Wshadow` | warns whenever a local variable or type declaration shadows another variable, parameter, class member, etc. |
| `-Wsign-conversion` | warn if making unsafe, implicit conversions between signed and unsigned types. |
| `-O2` | optimize code and check for errors in parallel to find potential bugs. |
| `--help` | gets help on specific options for the compiler. |

## Rough Compilation Process:

![](/diagram.png)
