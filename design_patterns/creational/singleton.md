# Singleton Pattern
A Singleton is a class designed to only have one single instance during a lifetime of an application.

## Features
- Only allowed to create 0 or 1 instance during a lifetime of an application.
- Created with default ctor.
    - If you need variety over instances and arguments required, consider `Factory` pattern instead.
    - Consider lazy initialization as default implementation.
- Single, parameterless, private ctor, mark as sealed(C#) or final(C++)
- Private static field holds the only reference to the instance and access is allowed through public static `Instance()` method.

## C# - .NET Implementations

## C++ Implementations
