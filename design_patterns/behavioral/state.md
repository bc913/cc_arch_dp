# State Pattern
`State` is the condition of something variable. The `State` pattern is to find solutions to the following design challenges:

## When to use?
- An object's behavior depends on its state and the behavior must be changed accordingly when the state is changed during run-time.
- Some/all operations might behave differently using internal switch/if-else statements and this flow is repeated among other operations. Typically, enums are used to in this scheme but it becomes overwhelming as the requirements grow.

## Components
- Context: Maintains an instance of a concrete state as the current state by holding the abstract state instance.
- Abstract State: Interface for all state-specific behaviors
- State implementations: Custom implementations for abstract state

## Implementations
- Context delegates the execution of its state-specific behaviors to one state object at a time.
- Context MIGHT pass itself as an argument to the corresponding State object. 
    - [C++] If access to the internals of the context is required, a `friend`ship can be given to the State object.
- The state transitions can either be handled by the context or the state object.
    - If the State impl is responsible, it requires to add specific API (i.e. TransitionToState() ) to Context interface.
    - This also yields to the fact that at least one State impl will have the knowledge of an other State impl.  

### C#
```csharp
// Use interface and/or abstract class sytax to define the abstraction
public abstract class StateBase
{
    public abstract void EnterState(Context the_context);
    public abstract void Action1(Context the_context);
    public abstract void Action2(Context the_context);

    public void ChangeState(Context the_context, StateBase newState)
    {
        the_context.ChangeState(newState);
    }
}

// Transitions are defined in the state implementations
public class NewState : StateBase
{
    public void EnterState(Context the_context)
    {
        the_context.DoSomething("This is the new state");
    }

    public void Action1(Context the_context)
    {
        the_context.DoSomething1();
        ChangeState(the_context, new OtherState());
    }

    public void Action2(Context the_context)
    {
        the_context.DoSomething2();
        ChangeState(the_context, new DiffState());
    }
}

// context
public class Context
{
    private StateBase _state;
    public Context(StateBase state)
    {
        // can be initialized internally also but I prefer dep injection
        ChangeState(state);
    }

    public void ChangeState(StateBase newState)
    {
        _state = newState ?? throw new ArgumentNullException(nameof(newState));
        _state.EnterState(this);
    }

    // some other behaviors
    /*
    ...
    */
    // State related behaviors
    // All of them delegated to the state impls
    // so state impls should have access those methods
    public void Action1()
    {
        _state.Action1(this);
    }

    public void Action2()
    {
        _state.Action2(this);
    }

}
```
### C++
- If the state implementations are only specific for a single context's use, it is better to define them in the same file or translation unit.
- If the state implementations do not maintain any local state, they can be defined as Singleton.

```cpp
// interface or abstract base class for state
class StateBase
{
public:
    virtual ~StateBase() = default;

    virtual void EnterState(const Context& the_context) = 0; // can be non-const
    virtual void Action1(Context& the_context) = 0;
    virtual void Action2(Context& the_context) = 0;

    virtual void ChangeState(Context& the_context, const StateBase& newState)
    {
        the_context.ChangeState(newState);
    }
};
// NewState.h
// State implementations can be singleton
class NewState : public StateBase
{
private:
    static NewState* m_instance = nullptr;
protected:
    NewState() = default; //for singleton
public:
    virtual ~NewState() = default;
    static NewState* Instance();

    void EnterState(const Context& the_context) final;
    void Action1(Context& the_context) final;
    void Action2(Context& the_context) final;
};

// NewState.cpp
NewState* NewState::m_instance = nullptr;

NewState* NewState::Instance()
{
    if(!m_instance) m_instance = new NewState();
    return m_instance;
}

void NewState::EnterState(const Context& the_context)
{
    the_context.DoSomething("This is the new state");
}

void NewState::Action1(Context& the_context)
{
    the_context.DoSomething1();
    ChangeState(the_context, new OtherState());
}

void NewState::Action2(Context& the_context)
{
    the_context.DoSomething2();
    ChangeState(the_context, new DiffState());
}

// Context
class Context
{
private:
    StateBase* m_state;
public:
    Context(StateBase* state)
    {
        // can be initialized internally also but I prefer dep injection
        ChangeState(state);
    }

    void ChangeState(StateBase* newState)
    {
        if(!newState)
            throw std::exception();
        
        _state = newState;
        _state->EnterState(*this);
    }

    // some other behaviors
    /*
    ...
    */
    // State related behaviors
    // All of them delegated to the state impls
    // so state impls should have access those methods
    void Action1()
    {
        _state->Action1(*this);
    }

    void Action2()
    {
        _state->Action2(*this);
    }
};
```

## Consequences
- It minimizes the conditional complexity.
- Encapsulates state-specific behaviors within seperate state objects

## Use cases
- Booking states: New, closed, pending and booked.
- TCPConnection state: Established, listen and closed.

## References
- [Design Patterns - Elements of Reusable Object-Oriented Software]
- [C# Design Patterns: State by Marc Gilbert]