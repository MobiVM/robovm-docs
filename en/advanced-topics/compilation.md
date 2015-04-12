# Under the Hood

## The Bytecode Compiler

At the heart of RoboVM is its [ahead-of-time compiler](http://en.wikipedia.org/wiki/Ahead-of-time_compilation). This is a tool that can be invoked either from the command line, from build tools such as Maven or Gradle, or from an IDE such as Eclipse or IntelliJ. It takes Java bytecode and translates it into machine code for a specific operating system and CPU type. Usually this means iOS and the ARM processor type but RoboVM is also capable of generating code for Mac OS X and Linux running on x86 CPUs.

The ahead-of-time approach is very different from how traditional JVMs, like [Oracle’s Hotspot](http://en.wikipedia.org/wiki/HotSpot), usually work. Such JVMs typically read in Java bytecode at runtime and somehow execute the virtual machine instructions contained in the bytecode. To speed up this process the JVM employs a technique called [just-in-time compilation](http://en.wikipedia.org/wiki/Just-in-time_compilation). In simple terms this process translates the virtual machine instructions of a method to native machine code for the current physical CPU the first time the method is invoked by the program.

Due to technical restrictions that Apple has built into iOS just-in-time compilation of any kind is impossible in an iOS app. The only alternatives are to use an interpreter, which is too slow and power consuming, or use ahead-of-time compilation like in RoboVM. The ahead-of-time compilation process takes place at compile time on the developer machine so at runtime, on an iOS device, the generated machine code runs at full speed, comparable to or even faster than code compiled from Objective-C.

By consuming Java bytecode rather than Java source code the RoboVM ahead-of-time compiler can, at least in theory, be used with any JVM language that compiles down to bytecode. Scala, Clojure and Kotlin are JVM languages already known to work. Another benefit with this approach is that RoboVM can be used with 3rd party libraries in standard JAR files without any need for the original source code enabling the use of proprietary and closed-source libraries.

## Incremental compilation

The first launch of a RoboVM app, even an app as simple as the `IOSDemo` app we saw in the demo app, takes some time. When compiling an app the RoboVM compiler starts with the app’s main class. It will then compile all classes needed by the main class and then the classes needed by those classes and so on until all classes needed by the app have been compiled. This process also compiles the standard runtime classes such as `java.lang.Object` and `java.lang.String`. This is a one-time thing only. RoboVM keeps a cache of compiled classes and only recompiles a class when it or any of its direct dependencies have changed.

> TIP: By default RoboVM uses `$HOME/.robovm/cache/` as the cache folder. By deleting this folder you can force RoboVM to recompile all classes from scratch.

The benefit of incremental compilation and caching of the object files is that it keeps down compile times. By only including the classes reachable from the main class it also keeps down the size of the produced executable. In some situations (e.g. when loading classes using reflection) the RoboVM compiler is unable to determine that a class should be compiled. Fortunately the compiler can be instructed to always include a particular class or even all classes matching a pattern.

## Android-based runtime class library

Any JVM needs a runtime class library. This is the library which provides the standard packages and classes needed by any Java program such as `java.lang.Object` and `java.lang.String`. RoboVM takes its runtime class library from the Android open-source project and all non-Android specific packages have been ported over to RoboVM. This means that any Java or JVM language code that only uses classes in the standard packages provided on Android should work the same under RoboVM.
