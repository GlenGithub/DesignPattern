# 命令模式


### 模式动机


在软件设计中，我们经常需要向某些对象发送请求，但是并不知道请求的接收者是谁，也不知道被请求的操作是哪个，我们只需在程序运行时指定具体的请求接收者即可，此时，可以使用命令模式来进行设计，使得请求发送者与请求接收者消除彼此之间的耦合，让对象之间的调用关系更加灵活。

命令模式可以对发送者和接收者完全解耦，发送者与接收者之间没有直接引用关系，发送请求的对象只需要知道如何发送请求，而不必知道如何完成请求。这就是命令模式的模式动机。


### 模式定义

命令模式(Command Pattern)：将一个请求封装为一个对象，从而使我们可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。命令模式是一种对象行为型模式，其别名为动作(Action)模式或事务(Transaction)模式。



### 模式结构

- **抽象命令类(Command)**: 声明执行操作的接口。调用接收者相应的操作，以实现执行的方法Execute。
- **具体命令类(ConcreteCommand)**: 创建一个具体命令对象并设定它的接收者。通常会持有接收者，并调用接收者的功能来完成命令要执行的操作。 
- **调用者(Invoker)**: 要求该命令执行这个请求。通常会持有命令对象，可以持有很多的命令对象。
- **接收者(Receiver)**: 知道如何实施与执行一个请求相关的操作。任何类都可能作为一个接收者,只要它能够实现命令要求实现的相应功能。 
- **客户类(Client)**: 创建具体的命令对象，并且设置命令对象的接收者。真正使用命令的客户端是从Invoker来触发执行。 


### 模式分析

- 每一个命令都是一个操作：请求的一方发出请求，要求执行一个操作；接收的一方收到请求，并执行操作。
- 命令模式允许请求的一方和接收的一方独立开来，使得请求的一方不必知道接收请求的一方的接口，更不必知道请求是怎么被接收，以及操作是否被执行、何时被执行，以及是怎么被执行的。
- 命令模式使请求本身成为一个对象，这个对象和其他对象一样可以被存储和传递。
- 命令模式的关键在于引入了抽象命令接口，且发送者针对抽象命令接口编程，只有实现了抽象命令接口的具体命令才能与接收者相关联。


### 实例

电视机是请求的接收者，遥控器是请求的发送者，遥控器上有一些按钮，不同的按钮对应电视机的不同操作。抽象命令角色由一个命令接口来扮演，有三个具体的命令类实现了抽象命令接口，这三个具体命令类分别代表三种操作：打开电视机、关闭电视机和切换频道。显然，电视机遥控器就是一个典型的命令模式应用实例。



### 优点

- 降低系统的耦合度。
- 新的命令可以很容易地加入到系统中。
- 可以比较容易地设计一个命令队列和宏命令（组合命令）。
- 可以方便地实现对请求的Undo和Redo。

### 缺点

- 使用命令模式可能会导致某些系统有过多的具体命令类。因为针对每一个命令都需要设计一个具体命令类，因此某些系统可能需要大量具体命令类，这将影响命令模式的使用。



### 适用环境


- 系统需要将请求调用者和请求接收者解耦，使得调用者和接收者不直接交互。
- 系统需要在不同的时间指定请求、将请求排队和执行请求。
- 系统需要支持命令的撤销(Undo)操作和恢复(Redo)操作。
- 系统需要将一组操作组合在一起，即支持宏命令



### 模式扩展

宏命令又称为组合命令，它是命令模式和组合模式联用的产物。

- 宏命令也是一个具体命令，不过它包含了对其他命令对象的引用，在调用宏命令的execute()方法时，将递归调用它所包含的每个成员命令的execute()方法，一个宏命令的成员对象可以是简单命令，还可以继续是宏命令。执行一个宏命令将执行多个具体命令，从而实现对命令的批处理。





```java

/**
 * Receiver
 */
class  Tv{
    public void turnUp(){
        System.out.println("turn up");
    }
    public void turnDown(){
        System.out.println("turn down");
    }
}

/**
 * Command
 */
interface Command{
    void execute();
}

/**
 * Concreate Command 1
 */
class  TurnUpCommand implements  Command{
    Tv mTv;
    TurnUpCommand(Tv tv){
        this.mTv=tv;
    }

    @Override
    public void execute() {
        mTv.turnUp();
    }
}

/**
 * Concreate Command 2
 */
class TurnDownCommand implements Command{
    Tv mTv;
    TurnDownCommand(Tv tv){
        this.mTv=tv;
    }

    @Override
    public void execute() {
        mTv.turnDown();
    }
}


/**
 * Invoker
 */
class  KeyPad
{
    Command turnDownCommand;
    Command turnUpCommand;

    public void setTurnUpCommand(Command turnUpCommand) {
        this.turnUpCommand = turnUpCommand;
    }

    public void setTurnDownCommand(Command turnDownCommand) {
        this.turnDownCommand = turnDownCommand;
    }

    public void turnDown(){
        turnDownCommand.execute();
    }

    public void turnUp(){
        turnUpCommand.execute();
    }

}


/**
 * 宏命令
 */
interface MacroCommand extends Command{
    void add(Command command);
    void remove(Command command);
}


class TvMacroCommand implements MacroCommand{

    List<Command>commands=new ArrayList<>();

    @Override
    public void add(Command command) {
        commands.add(command);
    }

    @Override
    public void remove(Command command) {
        commands.remove(command);
    }

    @Override
    public void execute() {
        for(Command cmd:commands){
            cmd.execute();
        }
    }
}

class Invoker{
    Command command;
    Invoker(Command command){
        this.command=command;
    }
    public void invoke(){
        command.execute();
    }
}



/**
 * Client
 */

public class CommandDemo {

    public static void main(String []args){
        Tv tv=new Tv();
        Command turnDownComand=new TurnDownCommand(tv);
        Command turnUpCommand=new TurnUpCommand(tv);

        KeyPad keyPad=new KeyPad();
        keyPad.setTurnDownCommand(turnDownComand);
        keyPad.setTurnUpCommand(turnUpCommand);

        keyPad.turnUp();
        keyPad.turnDown();




        TvMacroCommand macroCommand=new TvMacroCommand();
        macroCommand.add(turnUpCommand);
        macroCommand.add(turnDownComand);
        Invoker invoker=new Invoker(macroCommand);

        invoker.invoke();
    }

}

```