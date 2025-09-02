# Strategy Design Pattern in Java

The **Strategy Pattern** is a behavioral design pattern that defines a family of algorithms, encapsulates each one, and makes them interchangeable.  
It allows the algorithm to vary independently from clients that use it.

---

## 📖 Concept
- **Strategy (interface)** → Declares a method that all supported algorithms must implement.  
- **Concrete Strategies** → Implement the algorithm using the Strategy interface (`OperationAdd`, `OperationSubtract`, `OperationMultiply`).  
- **Context** → Maintains a reference to a Strategy object and executes the algorithm via the Strategy interface.

---

## 📂 UML Diagram (Mermaid)
```mermaid
classDiagram
    class Strategy {
        <<interface>>
        + doOperation(int num1, int num2) int
    }

    class OperationAdd {
        + doOperation(int num1, int num2) int
    }

    class OperationSubtract {
        + doOperation(int num1, int num2) int
    }

    class OperationMultiply {
        + doOperation(int num1, int num2) int
    }

    class Context {
        - Strategy strategy
        + Context(Strategy strategy)
        + executeStrategy(int num1, int num2) int
    }

    Strategy <|.. OperationAdd
    Strategy <|.. OperationSubtract
    Strategy <|.. OperationMultiply
    Context --> Strategy
