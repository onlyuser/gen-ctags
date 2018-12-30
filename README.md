gen-ctags
=============

Copyright (C) 2011-2017 <mailto:onlyuser@gmail.com>

About
-----

gen-ctags is a script to generate ctags from elf binary.

Why is this even necessary if you have the source code? Theoretically, languages that aren't supported by ctag but follow the C++ ABI could use it.

See sister project for generating call graph from elf binary: [gen-callgraph](https://github.com/onlyuser/gen-callgraph)

A Motivating Example
--------------------

input: elf binary from below source:
<pre>
class QWE
{
public:
    QWE() {}
    ~QWE() {}
    void f() {}
};

void A();
void C() {A();}
void B() {C(); QWE qwe; qwe.f();}
void A() {B(); C();}

int main(int argc, char** argv)
{
    A();
    return 0;
}
</pre>

output: ctags:
<pre>
~QWE	main.cpp	/^    ~QWE() {}$/;"	f	class:QWE
A	main.cpp	/^void A() {B(); C();}$/;"	f
B	main.cpp	/^void B() {C(); QWE qwe; qwe.f();}$/;"	f
C	main.cpp	/^void C() {A();}$/;"	f
QWE	main.cpp	/^    QWE() {}$/;"	c	file:
QWE	main.cpp	/^    QWE() {}$/;"	f	class:QWE
f	main.cpp	/^    void f() {}$/;"	f	class:QWE
main	main.cpp	/^int main(int argc, char** argv)$/;"	f
</pre>

Requirements
------------

    bash readelf objdump c++filt addr2line

Limitations
-----------

<ul>
    <li>Only supports statically linked functions within one x86_64 binary.</li>
    <li>Only supports function calls invoked by assembly commands callq/jmpq/jmp and conditional jump variants on literal (non-register) destinations.</li>
    <li>Naive algorithm. Only tested on small binaries.</li>
</ul>

Installation (Debian)
---------------------

1. git clone https://github.com/onlyuser/gen-ctags.git

Usage
-----

<pre>
gen-ctags &lt;BINARY&gt; [DEBUG={0*/1}] | sort --version-sort > tags
</pre>

Recommended gcc Options
-----------------------

<ul>
    <li>-g</li>
    <li>-O0</li>
    <li>-fno-function-cse</li>
    <li>-fomit-frame-pointer</li>
</ul>

References
----------

<dl>
    <dt>"Disassemble raw x64 machine code"</dt>
    <dd>http://stackoverflow.com/questions/19071461/disassemble-raw-x64-machine-code</dd>
    <dt>"Trying to understand gcc option -fomit-frame-pointer"</dt>
    <dd>http://stackoverflow.com/questions/14666665/trying-to-understand-gcc-option-fomit-frame-pointer</dd>
    <dt>"3.10 Options That Control Optimization"</dt>
    <dd>https://gcc.gnu.org/onlinedocs/gcc-4.5.2/gcc/Optimize-Options.html</dd>
</dl>

Keywords
--------

    ctags, asm, disassembly, elf, name mangling
