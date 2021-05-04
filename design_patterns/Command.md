# Command
## Intro
This pattern can be considered as object oriented encapsulation of operations(tasks, requests). This pattern can be used within the following contexts:
- Undo/redo
- Command pipelines/streams/queue
- Logging
- Transactions

## Usage

### Stateless
```cpp
class ICommand{
public:
    virtual ~ICommand() = default;
    virtual void execute() = 0;
};

// Plain
class DoSthCommand : public ICommand{
    void run() { /**/ }
public:
    void execute() override { run(); }
};

// through callback
class A{
public:
    void do_sth(){/**/}
};

class DoSthCommand : public ICommand{
    std::function<void()> fn_;
public:
    DoSthCommand(std::function<void()> f) : fn_(f) {}
    void execute() override { fn_(); }
};

// Client
std::unique_ptr<ICommand> command = nullptr;
{
    A obj{};
    command = std::make_unique<DoSthCommand>(std::bind(&A::do_sth, std::ref(obj)));
}
command->execute();
```

### Receiver as argument
The receiver object is passed as argument. This approach is useful in network gaming i.e. passing the receiver object and the series(collection) of command to the network seperately.

```cpp
class ICharacter{
public:
    virtual ~ICharacter() = default;
    virtual void jump() = 0;
    virtual void crawl() = 0;
};
class ICommand {
public:
    virtual ~ICommand() = default;
    virtual void execute(const ICharacter& c) = 0;
};
// Impl
class JumpCommand : public ICommand{
public:
    void execute(const ICharacter& c) override { c.jump(); }
};

class CrawlCommand : public ICommand{
public:
    void execute(const ICharacter& c) override { c.crawl(); }
};

```

### Stateful commands
The command objects has state by holding the receiver object as data member.

```cpp

class ICommand{
public:
    virtual ~ICommand() = default;
    virtual void execute() = 0;
};

class ICharacter{
public:
    virtual ~ICharacter() = default;
    virtual void jump() = 0;
    virtual void crawl(int x, int y) = 0;
};
// Impl
class JumpCommand : public ICommand{
    std::weak_ptr<ICharacter> character_;
public:
    JumpCommand(std::shared_ptr<ICharacter> the_character) : character_(the_character) {}
    void execute() override { 
        if(auto the_char = character_->lock()) {
            the_char->jump();
        }
    }
};

class CrawlCommand : public ICommand{
    std::weak_ptr<ICharacter> character_;
    int diff_x, diff_y;
public:
    CrawlCommand(std::shared_ptr<ICharacter> the_character, int x, int y) 
    : character_(the_character), diff_x(x), diff_y(y){}

    void execute() override {
        if(auto the_char = character_->lock()){
            the_char->crawl(diff_x, diff_y);
        }
    }
};

```


## Undo/ Redo
There are two requirements to have undo/redo capabilities:
- the command should be aware of the previous state before the command runs and that info should be stored. 
- The client should keep track of executed commands through linked-list like structure to execute undo/redo in the right order.

### Keeping track of the previous state
There are several ways to achieve this:
<details>
<summary>Hold the required bits of info from the current state of the receiver and then run the command.</summary>

```cpp
class ICommand{
public:
    virtual ~ICommand() = default;
    virtual void execute() = 0;
    virtual void undo() = 0;
};

class ICharacter{
public:
    virtual ~ICharacter() = default;
    virtual void jump() = 0;
    virtual void crawl(int x, int y) = 0;
};
// Impl
class CrawlCommand : public ICommand{
    std::weak_ptr<ICharacter> character_;
    int x_, y_;
    int prev_x, prev_y;
public:
    CrawlCommand(std::shared_ptr<ICharacter> the_character, int x, int y) 
    : character_(the_character), x_(x), y_(y), prev_x(0), prev_y(0){}

    void execute() override {
        if(auto the_char = character_->lock()){
            prev_x = GetPosition(*the_char, POS::X);
            prev_y = GetPosition(*the_char, POS::y);
            the_char->crawl(x_, y_);
        }
    }

    void undo() override {
        if(auto the_char = character_->lock()) {
            the_char->crawl(prev_x, prev_y);
        }
    }
};
```
</details>

<details>

<summary>Hold a snapshot of receiver's entire or partial current state and then run the command [Memento]</summary>

</details>

<details>

<summary>Use persistent data structures to hold the info for each step</summary>

</details>


### Command history
The commands have to be kept in a linked-list like data structure. We have the keep an iterator on the last executed command in the list and rewind/forward as needed.

## Macro Command
This is a composition of multiple commands under a single command class.
```cpp
class MacroCommands : public ICommand{
    std::vector<std::weak_ptr<ICommand>> cmds_;
public:
    void add(std::shared_ptr<ICommand> cmd) {
        cmds_.emplace_back(cmd);
    }

    void remove(std::shared_ptr<ICommand> cmd) {
        auto found = std::find_if(cmds_.begin(), cmds_.end(), [&](auto& c) { return cmd == c.lock(); });
        if(found != cmds_.end())
            cmds_.erase(found);
    }

    void execute() override {
        for(auto c : cmds_)
            if(auto command = c.lock())
                command->execute();
    }
};
```

## Template approach
Using `std::function` provides more felxibility. Also binding lets the part or all of the arguments to be stored.



## References
- Modern C++ Design, Andrei Alexandrescu
- Design Patterns - Elements of Reusable Object-Oriented Software, Gamma et. al., 1994
- [https://gameprogrammingpatterns.com/command.html](https://gameprogrammingpatterns.com/command.html)
- [Undo/Redo Redux - Al Stevens](https://www.drdobbs.com/cpp/c-programming/184410722?queryText=undo%252Fredo%2Bredux)
- [An Unlimited Undo/Redo Stack Pattern For PowerBuilder](https://www.drdobbs.com/parallel/an-unlimited-undoredo-stack-pattern-for/184410214)