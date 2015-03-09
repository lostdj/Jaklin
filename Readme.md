Java/Avian, OpenJDK and JavaFX/OpenJFX web port.

### Demos

As this is a very early and incomplete port the demos are very much broken, buggy and slow as hell. Chrome is at least 2x faster and stable in runtime, and Firefox is faster in load time. Funny.

[Simple and stupid](http://ltp.name/Jaklin/test/stupid)  
[Brick Breaker](http://ltp.name/Jaklin/test/brickbreaker)  
[Modena](http://ltp.name/Jaklin/test/modena-uimosaic)  
[Ensemble](http://ltp.name/Jaklin/test/ensemble)  

As a bonus, here is one of the first tests made before JFX port was started: [JBox2D tests](http://ltp.name/Jaklin/test/jb2d). Press `space` to switch between test scenes. This build was compiled against Avian CP, that's why it is so small.  
Source code: https://github.com/lostdj/Jaklin-Avian/tree/jaklin-master/mytest/jbox2d  
Yes, the renderer is broken and it's not just you. Here's the same demo running in an applet: http://www.jbox2d.org/

There also was a Quake 2 port via Jake2, with no renderer at all: https://github.com/lostdj/Jaklin-Avian/tree/jaklin-master/mytest/q2 It was... Slow.

### Building

#### Dependencies
You'll have to have a Linux box to build it both for the web and desktop, as this is currently an only platform the development is happening on.

Dependencies: 
- Oracle JDK 8. OpenJDK could also be enough;
- [Emscripten](http://kripken.github.io/emscripten-site/);
- GNU Make.

If you are using (doubt it) [Nix](http://nixos.org/) package manager or NixOS, there's a `default.nix` script found in `myavn/` for you to install all the required dependencies and create a build environment.

#### Cloning

`git clone --recursive https://github.com/lostdj/Jaklin.git`

#### Compiling

You want to build it for the web, right?

```
cd Jaklin \
&& (cd myjdktop/myjdk/ && make mode=fast arch=ems all) \
&& (cd myavn/ && make mode=fast arch=ems all) \
&& (cd myfx/ && make mytests=t mode=fast arch=ems all || make mytests=t mode=fast arch=ems all)
```

The same SDL port can run natively, too. Remove `arch=ems` from the above make commands and add `glass.platform=sdl` to the last two.

### Issues and todo

- Code and repo cleanup and refactoring;
- Replace makefiles with something else. That something else won't be CMake;
- Loading time improvements;
- Stage/WebGL canvas creation works incorrectly;
- Mouse handling impl is incomplete;
- Keyboard handling impl is nonexistent;
- Cliboard handling, media module, ...;
- PNaCl port.

And **the** performance issues.

Probably the most of them come from using a very simple interpreter to run raw and unoptimized Java bytecode.
- Java bytecode does not help by itself and it is unoptimized;
- The interpreter introduces a lot of overhead by tagging the stack with variable types: Int (this one includes all the other types) and Object;
- Computed goto / Labels as values trick would help in native binaries and on PNaCl, but not on the web. See https://github.com/kripken/emscripten/issues/1742 and https://github.com/dherman/asm.js/issues/80

What are the possible steps to fix this?

Methods of code translation:
- JIT;
- AOT.

Targets:
- Optimized interpreter and Java bytecode;
- Register-based bytecode;
- JavaScript;
- C++.

Tools to help with that:
- Avian itself. It can perform both JIT and AOT compilation. In case of JIT, the JS backend will be required; and for AOT, LLVM bitcode generator. Unfortunately Avian doesn't perform any (?) optimizations and produces a very simple code. It may well be similar to one of the backends of Apache Harmony's, which generates machine code a very similar one to Java's bytecode. All the stack shenanigans, except, well, it's a native machine code;
- One of the Apache Harmony generators and optimizers. See above;
- [Soot](http://sable.github.io/soot/) optimization framework. It's not only a framework to play with the code and test optimization strategies, but it can optimize your code from command line. It also has various IRs which can be used as a base for a new register-based bytecode and interpreter. Too bad it's under GPL;
- That Android .class -> .dex translator for Dalvik VM;
- Adapt J2ObjC: http://j2objc.org/;
- There is at least one solution to translate Java bytecode to C++, but I'm sure it will require a lot of work to handle everything we have here;
- One of the tools from this list, plus LLVM itself.

One major point to consider when deciding which way to choose is a dynamic code generation. As in, the user wants to generate some classes and methods in runtime and expects to run it. This won't work in AOT solutions like RoboVM present (though they may fallback to an interpreter, that I don't know).

And the other one, I'd like to find an acceptable solution to "rule them all" and run the same code on all platforms.

