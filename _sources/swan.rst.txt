.. highlight:: rst

*******************************************
Analytics Programming Model - SWAN Compiler
*******************************************


Introduction
############


ASAP aims to make it easy for users to describe and run data analytics queries by selecting the most suitable and efficient execution engines. There should be no restriction on the type of execution engine or on the format and medium used for input and output datasets. ASAP provides a workflow tool to facilitate the visual creation and adaptation of data analytics queries enabling analytics experts to create and change queries by dragging operator boxes and dataset flow arrows in a graphical interface into a suitable pictorial representation of a query workflow. Subsequent to query creation in the workflow tool, a multi-engine resource scheduling platform, IReS takes the workflow description of the analytics query and co-operates with a set of runtimes and data stores in order to effect the best implementation and execution of the query. A bridge is needed between the high level abstract description of a workflow query and the actual provisioning and execution of codes that can run the query.  This bridge is satisfied by the ASAP compiler. It maps the user’s high level workflow query,  as created within the workflow tool , to codes for the low-level programming language **Swan** using metadata which describes how Swan operators are materialized.  ASAP’s scheduler can subsequently use these codes to profile and select optimum runtime engines and data stores.




Design
######


The compiler reads and stores:

1.  The metadata for materialized operators
2.  The user’s work-flow description

and generates Swan source code which can execute the user’s analytics query.

There are two phases:

**Phase 1**: The compiler loads descriptions of operators and workflows from the metadata files into instances of descriptor classes which map operators from the user’s workflow to source code and subsequently executable code. Once this is loaded the compiler can infer rules around the use of individual operators.  For example, it will be able to build the function signature for calls to core functions such inputoutput and operator run statements.  It can work out what type definitions are required for different operators, what arguments are permitted to operators such as K-means and what the default values are if these are not supplied by the user.

**Phase 2**: The compiler generates  the  code  by  loading  skeletal  source  code  from  template  files. These contain sections of Swan source code calling into a library with pre-defined functions. The skeletal code moreover contains placeholders requiring substitution with actual values and/or code sections. The compiler reads the templates and performs these substitions using operator rules to produce operator codes.


Algorithm
#########

The compiler algorithm is driven by:

1.  The flow of operators in the user’s workflow description.
2.  The general sequencing and layout of a Swan/C++ program.
Initially the compiler parses metadata from the materialized operators library and creates a hierarchy of descriptor instances which represent operator and language construct rules.  These may be anything from operator objects to rules for declarations, typedefs and arguments associated with operators. 

Code generation proceeds by:

Parsing and looping around the user’s workflow of operators.
(a)  Referencing the descriptor instances.
(b)  Referencing template code files and substituting placeholders.
(c)  Printing the code section.

Typically  declarations  for  input,  output  and  operator  arguments  are  generated  directly  and wholly from the materialized operator rules, replacing variable names with internally generated compiler ones and filenames with those supplied by the user.


Setup
#####


The runtime should be obtained from https://github.com/project-asap/swan_runtime.git and it is an extension of the Intel Cilk Plus Runtime.

The compiler should be obtained from https://github.com/project-asap/swan_clang.git and it is an extension of the Clang/LLVM compiler.

Note that the Clang/LLVM distribution has its own copy of the Intel Cilk Plus Runtime. We have however used the external code base as it is more recent.

The code for the Swan tests should be obtained from: https://github.com/project-asap/swan_tests.git



Install
#######

For demostration reasons a Linux operating system like Ubuntu it is assumed in this step. In Windows or other Linux distributions the equivalents should be done.



Install the compiler
********************

1. Checkout swan_llvm: git clone https://github.com/project-asap/swan_llvm.git
2. Move to clang: cd swan_llvm/tools
3. Checkout swan_clang: git clone https://github.com/project-asap/swan_clang.git
4. Move to compiler-rt directory: cd ../projects/compiler-rt
5. Create and move to build directory: mkdir build
			               cd build
6. Build LLVM: cmake -G "Unix Makefiles" ../llvm
	       make clean
	       make




Install the runtime
******************* 

1. Checkout swan_runtime: git clone https://github.com/project-asap/swan_runtime.git
2. Move to swan_runtime directory: cd swan_runtime
3. Build the Swan runtime: libtoolize
		           aclocal
			   automake --add-missing
                           autoconf
			   ./configure --prefix=<swan_runtime>/swan_runtime/lib CC=../build/bin/clang CXX=../build/bin/clang++
			   make clean
			   make


Tests
#####

Test clang:

1. Move to swan directory: cd swan
2. Execute: clang --help
            clang++ --help
            clang llvm/utils/count/count.c -fsyntax-only
            clang llvm/utils/count/count.c -S -emit-llvm -o -
            clang llvm/utils/count/count.c -S -emit-llvm -o - -O3
	    clang llvm/utils/count/count.c -S -O3 -o -


Test swan:

1. Checkout swan_tests: git clone https://github.com/project-asap/swan_tests.git
2. Move to swan_tests directory: cd swan_tests
3. Execute: make CXX=../build/bin/clang++ SWANRTDIR=../swan_runtime test


