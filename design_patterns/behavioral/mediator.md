# Mediator
It is an abstraction mechanism for complex communication of same level objects. In some cases, the different types of objects in the **same** layer has to communicate with each other or warn others about the changes occured within. If the number of objects is numereous, this will bring an huge amount of communication code within the object's class definitions and also will break encapsulation because each component will have to know about the ohter one to communicate.

In order to alleviate this burden, *Mediator* class come into the picture and handles communications between each other. This is a two-way communication so each component will only know the mediator and mediator will know all the communicated components. This will decouples the same level components and centralize the communications. It also abstracts how object will communicate.

## References
- Modern C++ Design, Andrei Alexandrescu
- Design Patterns - Elements of Reusable Object-Oriented Software, Gamma et. al., 1994
- [https://gameprogrammingpatterns.com/command.html](https://gameprogrammingpatterns.com/command.html)
- [Undo/Redo Redux - Al Stevens](https://www.drdobbs.com/cpp/c-programming/184410722?queryText=undo%252Fredo%2Bredux)
- [An Unlimited Undo/Redo Stack Pattern For PowerBuilder](https://www.drdobbs.com/parallel/an-unlimited-undoredo-stack-pattern-for/184410214)