Java/Avian, OpenJDK and JavaFX/OpenJFX web port.

### Demos

As this is a very early and incomplete port the demos are very much broken, buggy and slow as hell. Chrome is at least 2x faster and stable in runtime, and Firefox is faster in load time. Funny.

[Simple and stupid](http://ltp.name/Jaklin/test/stupid)  
[Brick Breaker](http://ltp.name/Jaklin/test/brickbreaker)  
[Modena](http://ltp.name/Jaklin/test/modena-uimosaic)  
[Ensemble](http://ltp.name/Jaklin/test/ensemble)  

As a bonus, here is one of the first tests made before JFX port was started: [JBox2D tests](http://ltp.name/Jaklin/test/jb2d). Press `space` to switch between test scenes.  
Source code: https://github.com/lostdj/Jaklin-Avian/tree/jaklin-master/mytest/jbox2d  
Yes, the renderer is broken and it's not just you. Here's the same demo running in applet: http://www.jbox2d.org/

There also was a Quake 2 port via Jake2, with no renderer at all: https://github.com/lostdj/Jaklin-Avian/tree/jaklin-master/mytest/q2 It was... Slow.

### Building

#### Dependencies
You'll have to have a Linux box to build it both for the web and desktop, as this is currently an only platform the development is happening on.

Dependencies: 
- Oracle JDK 8. OpenJDK could also be enough;
- [Emscripten](http://kripken.github.io/emscripten-site/);
- GNU Make.

If you are using (doubt it) [Nix](http://nixos.org/) package manager or NixOS, there's a `default.nix` script for you to install all the required dependencies and create a build environment.

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

