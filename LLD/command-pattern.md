# Command Pattern

The Command pattern turns a request into a stand-alone object that contains all information about the request. This transformation lets you pass requests as a method arguments, delay or queue a request's execution, and support undoable operations.

## Real-Time Use Case: Text Editor (Undo/Redo) or Remote Control
Imagine a **Text Editor** like MS Word. Each action (Type, Delete, Paste, Bold) is a "Command".
By making "Delete" an object, we can store it in a `historyStack`. When the user hits Ctrl+Z, we pop the last command and call its `Unexecute()` method to restore the text.

## Example Code (C#)

```csharp
public interface ICommand {
    void Execute();
    void Undo();
}

public class TextEditor {
    public string Content { get; set; } = "";
}

public class TypeCommand : ICommand {
    private TextEditor _editor;
    private string _text;

    public TypeCommand(TextEditor editor, string text) {
        _editor = editor;
        _text = text;
    }

    public void Execute() => _editor.Content += _text;
    public void Undo() => _editor.Content = _editor.Content.Substring(0, _editor.Content.Length - _text.Length);
}

// Invoker
public class CommandInvoker {
    private Stack<ICommand> _history = new Stack<ICommand>();

    public void Execute(ICommand command) {
        command.Execute();
        _history.Push(command);
    }

    public void Undo() {
        if (_history.Count > 0) _history.Pop().Undo();
    }
}
```

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Undo/Redo Support**: Natural fit for history tracking. | **Over-engineering**: Can be too complex for simple request-response flows. |
| **Decoupling**: The object that triggers the command doesn't need to know how the command is executed. | **Class Proliferation**: You need a separate class for every specific command. |
| **Queuing**: Commands can be serialized and sent over a network or stored in a queue. | |

## When to use?
Use when you need to parameterize objects by an action to perform, or when you need to support Undo, Logging, or Transactions (Rollback).
