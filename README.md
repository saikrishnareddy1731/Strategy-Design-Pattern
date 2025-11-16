# Strategy Design Pattern - UML Diagrams

## 1. Strategy Pattern - Class Diagram

```mermaid
classDiagram
    class PaymentStrategy {
        <<interface>>
        +pay(amount: double)*
    }
    
    class CreditCardPayment {
        -String cardNumber
        -String name
        +CreditCardPayment(cardNumber: String, name: String)
        +pay(amount: double)
    }
    
    class PayPalPayment {
        -String email
        +PayPalPayment(email: String)
        +pay(amount: double)
    }
    
    class UpiPayment {
        -String upiId
        +UpiPayment(upiId: String)
        +pay(amount: double)
    }
    
    class PaymentContext {
        -PaymentStrategy paymentStrategy
        +setPaymentStrategy(paymentStrategy: PaymentStrategy)
        +payAmount(amount: double)
    }
    
    class StrategyDemo {
        +main(args: String[])$
    }
    
    PaymentStrategy <|.. CreditCardPayment : implements
    PaymentStrategy <|.. PayPalPayment : implements
    PaymentStrategy <|.. UpiPayment : implements
    PaymentContext o-- PaymentStrategy : uses
    StrategyDemo ..> PaymentContext : creates
    StrategyDemo ..> CreditCardPayment : creates
    StrategyDemo ..> PayPalPayment : creates
    StrategyDemo ..> UpiPayment : creates
```

---

## 2. Sequence Diagram - Payment Flow

```mermaid
sequenceDiagram
    participant Client as StrategyDemo
    participant Context as PaymentContext
    participant Strategy as PaymentStrategy
    participant CC as CreditCardPayment
    participant PP as PayPalPayment
    participant UPI as UpiPayment
    
    Client->>Context: new PaymentContext()
    activate Context
    Context-->>Client: context
    deactivate Context
    
    Note over Client,CC: Payment 1: Credit Card
    Client->>CC: new CreditCardPayment("1234...", "John")
    activate CC
    CC-->>Client: creditCard strategy
    deactivate CC
    
    Client->>Context: setPaymentStrategy(creditCard)
    activate Context
    Context-->>Client: void
    deactivate Context
    
    Client->>Context: payAmount(1000)
    activate Context
    Context->>CC: pay(1000)
    activate CC
    CC-->>Context: "Paid 1000 using Credit Card..."
    deactivate CC
    Context-->>Client: void
    deactivate Context
    
    Note over Client,PP: Payment 2: PayPal
    Client->>PP: new PayPalPayment("john@example.com")
    activate PP
    PP-->>Client: paypal strategy
    deactivate PP
    
    Client->>Context: setPaymentStrategy(paypal)
    activate Context
    Context-->>Client: void
    deactivate Context
    
    Client->>Context: payAmount(500)
    activate Context
    Context->>PP: pay(500)
    activate PP
    PP-->>Context: "Paid 500 using PayPal..."
    deactivate PP
    Context-->>Client: void
    deactivate Context
    
    Note over Client,UPI: Payment 3: UPI
    Client->>UPI: new UpiPayment("john@upi")
    activate UPI
    UPI-->>Client: upi strategy
    deactivate UPI
    
    Client->>Context: setPaymentStrategy(upi)
    activate Context
    Context-->>Client: void
    deactivate Context
    
    Client->>Context: payAmount(750)
    activate Context
    Context->>UPI: pay(750)
    activate UPI
    UPI-->>Context: "Paid 750 using UPI..."
    deactivate UPI
    Context-->>Client: void
    deactivate Context
```

---

## 3. Strategy Pattern Flow

```mermaid
graph TB
    subgraph "Strategy Pattern Flow"
        A[Client<br/>StrategyDemo] -->|1. Creates| B[Context<br/>PaymentContext]
        A -->|2. Creates| C[Strategy<br/>CreditCard/PayPal/UPI]
        A -->|3. Sets Strategy| B
        B -->|4. Stores| C
        A -->|5. Calls payAmount| B
        B -->|6. Delegates to| C
        C -->|7. Executes algorithm| D[Payment Processing]
        D -->|8. Returns result| B
        B -->|9. Returns to| A
    end
    
    style A fill:#e1f5ff
    style B fill:#fff4e1
    style C fill:#ffe1f5
    style D fill:#e8f5e9
```

---

## 4. Strategy Selection at Runtime

```mermaid
stateDiagram-v2
    [*] --> ContextCreated: new PaymentContext()
    
    ContextCreated --> CreditCardSet: setPaymentStrategy(CreditCard)
    CreditCardSet --> PayWithCC: payAmount(1000)
    PayWithCC --> CreditCardSet: Can switch strategy
    
    CreditCardSet --> PayPalSet: setPaymentStrategy(PayPal)
    PayPalSet --> PayWithPP: payAmount(500)
    PayWithPP --> PayPalSet: Can switch strategy
    
    PayPalSet --> UpiSet: setPaymentStrategy(UPI)
    UpiSet --> PayWithUPI: payAmount(750)
    PayWithUPI --> UpiSet: Can switch strategy
    
    UpiSet --> [*]
    
    note right of CreditCardSet
        Strategy can be changed
        at any time during runtime
    end note
```

---

## 5. Comparison: Without vs With Strategy Pattern

```mermaid
graph TB
    subgraph "Without Strategy Pattern - Using if/else"
        W1[PaymentContext]
        W2["payAmount(type, amount) {<br/>  if (type == 'creditcard')<br/>    // credit card logic<br/>  else if (type == 'paypal')<br/>    // paypal logic<br/>  else if (type == 'upi')<br/>    // upi logic<br/>}"]
        W3[❌ Violates Open/Closed Principle<br/>❌ Hard to add new methods<br/>❌ Complex conditionals<br/>❌ Hard to test]
        W1 --> W2 --> W3
    end
    
    subgraph "With Strategy Pattern"
        B1[PaymentContext]
        B2["payAmount(amount) {<br/>  strategy.pay(amount)<br/>}"]
        B3[✅ Follows Open/Closed Principle<br/>✅ Easy to add new strategies<br/>✅ Clean, simple code<br/>✅ Easy to test]
        B1 --> B2 --> B3
    end
    
    style W3 fill:#f44336,color:#fff
    style B3 fill:#4CAF50,color:#fff
```

---

## 6. Design Explanation

### What is the Strategy Pattern?

**Strategy Pattern** is a behavioral design pattern that defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

### Key Components

1. **Strategy Interface (PaymentStrategy)**:
   - Declares the common interface for all strategies
   - Method: `pay(double amount)`
   - All concrete strategies must implement this

2. **Concrete Strategies (CreditCardPayment, PayPalPayment, UpiPayment)**:
   - Implement the Strategy interface
   - Each encapsulates a specific algorithm/behavior
   - Can have their own instance variables
   - Independent and interchangeable

3. **Context (PaymentContext)**:
   - Maintains a reference to a Strategy object
   - Delegates the algorithm execution to the strategy
   - Can switch strategies at runtime
   - Provides `setPaymentStrategy()` to change strategy

4. **Client (StrategyDemo)**:
   - Creates concrete strategy objects
   - Passes strategy to context
   - Calls context methods
   - Can change strategy dynamically

---

## 7. How Your Code Works

### Step-by-Step Execution

```mermaid
sequenceDiagram
    autonumber
    participant Main as StrategyDemo
    participant Ctx as PaymentContext
    participant Strat as Strategy Object
    
    Main->>Ctx: Create context
    Main->>Strat: Create strategy (CreditCard)
    Main->>Ctx: setPaymentStrategy(strategy)
    Note over Ctx: Context stores strategy reference
    Main->>Ctx: payAmount(1000)
    Ctx->>Strat: pay(1000)
    Strat-->>Ctx: Execute payment logic
    Ctx-->>Main: Complete
    
    Main->>Strat: Create new strategy (PayPal)
    Main->>Ctx: setPaymentStrategy(newStrategy)
    Note over Ctx: Context switches to new strategy
    Main->>Ctx: payAmount(500)
    Ctx->>Strat: pay(500)
    Strat-->>Ctx: Execute payment logic
    Ctx-->>Main: Complete
```

---

## 8. Strategy Pattern Structure

```mermaid
graph LR
    subgraph "Strategy Pattern Components"
        Client[Client<br/>StrategyDemo]
        Context[Context<br/>PaymentContext]
        Interface[Strategy Interface<br/>PaymentStrategy]
        S1[Concrete Strategy<br/>CreditCardPayment]
        S2[Concrete Strategy<br/>PayPalPayment]
        S3[Concrete Strategy<br/>UpiPayment]
    end
    
    Client -->|creates & configures| Context
    Client -->|creates| S1
    Client -->|creates| S2
    Client -->|creates| S3
    Context -->|uses| Interface
    S1 -.->|implements| Interface
    S2 -.->|implements| Interface
    S3 -.->|implements| Interface
    
    style Client fill:#e1f5ff
    style Context fill:#fff4e1
    style Interface fill:#ffe1f5
    style S1 fill:#e8f5e9
    style S2 fill:#e8f5e9
    style S3 fill:#e8f5e9
```

---

## 9. Advantages & Disadvantages

### ✅ Advantages

```mermaid
mindmap
    root((Strategy Pattern<br/>Advantages))
        Open/Closed Principle
            Add new strategies easily
            No modification to existing code
            Extend without breaking
        Runtime Flexibility
            Switch algorithms at runtime
            Dynamic behavior change
            Adapt to user choice
        Eliminates Conditionals
            No if-else chains
            No switch statements
            Cleaner code
        Testability
            Test each strategy independently
            Mock strategies easily
            Isolated unit tests
        Single Responsibility
            Each strategy = one algorithm
            Separation of concerns
            Easy to maintain
```

### ❌ Disadvantages

| Disadvantage | Description |
|-------------|-------------|
| **More Classes** | Need to create multiple strategy classes |
| **Client Awareness** | Client must know about different strategies |
| **Object Overhead** | Creates many objects at runtime |
| **Simple Cases** | Overkill for simple algorithms |

---

## 10. When to Use Strategy Pattern

```mermaid
flowchart TD
    Start{Need different<br/>algorithms?}
    
    Start -->|No| Direct[Use Direct<br/>Implementation]
    Start -->|Yes| Q1{Algorithms change<br/>at runtime?}
    
    Q1 -->|No<br/>Fixed at compile time| Config[Use Configuration<br/>or Factory]
    Q1 -->|Yes| Q2{Many conditional<br/>statements?}
    
    Q2 -->|No<br/>Simple logic| Simple[Keep Simple<br/>if-else]
    Q2 -->|Yes| Q3{Algorithms are<br/>interchangeable?}
    
    Q3 -->|No| Other[Consider other<br/>patterns]
    Q3 -->|Yes| Strategy[✅ Use Strategy<br/>Pattern]
    
    Strategy --> Benefits[✅ Runtime flexibility<br/>✅ Clean code<br/>✅ Easy to extend]
    
    style Strategy fill:#4CAF50,color:#fff,stroke:#2E7D32,stroke-width:3px
    style Benefits fill:#81C784,color:#fff
    style Start fill:#FF9800,color:#fff
```

---

## 11. Real-World Examples

### Common Use Cases

```mermaid
mindmap
    root((Strategy Pattern<br/>Use Cases))
        Payment Processing
            Credit Card
            PayPal
            UPI
            Cryptocurrency
            Wallet
        Sorting Algorithms
            Bubble Sort
            Quick Sort
            Merge Sort
            Heap Sort
        Compression
            ZIP
            RAR
            GZIP
            TAR
        Navigation
            Walk
            Drive
            Public Transport
            Fly
        Validation
            Email Validator
            Phone Validator
            Age Validator
        Pricing
            Regular Price
            Discount Price
            Member Price
            Seasonal Price
```

---

## 12. Adding New Strategy - Example

### Adding BitcoinPayment

```mermaid
sequenceDiagram
    participant Dev as Developer
    participant Bitcoin as BitcoinPayment
    participant Interface as PaymentStrategy
    participant Context as PaymentContext
    participant Client as StrategyDemo
    
    Note over Dev,Bitcoin: Step 1: Create new strategy
    Dev->>Bitcoin: Create BitcoinPayment class
    Bitcoin->>Interface: implements PaymentStrategy
    Interface-->>Bitcoin: ✓ Contract satisfied
    
    Note over Dev,Context: Step 2: No context changes needed!
    Dev->>Context: Context unchanged
    Context-->>Dev: Already works with interface
    
    Note over Dev,Client: Step 3: Use new strategy
    Client->>Bitcoin: new BitcoinPayment("1A2b3C...")
    Bitcoin-->>Client: bitcoin strategy
    Client->>Context: setPaymentStrategy(bitcoin)
    Context->>Bitcoin: pay(1500)
    Bitcoin-->>Client: "Paid 1500 using Bitcoin..."
```

### Code to Add Bitcoin Payment

```java
// 1. Create new strategy class - implements interface
package com.example.strategy;

public class BitcoinPayment implements PaymentStrategy {
    private String walletAddress;

    public BitcoinPayment(String walletAddress) {
        this.walletAddress = walletAddress;
    }

    @Override
    public void pay(double amount) {
        System.out.println("Paid " + amount + " using Bitcoin: " + walletAddress);
    }
}

// 2. Context class - NO CHANGES NEEDED!

// 3. Client code - Just use the new strategy
public class StrategyDemo {
    public static void main(String[] args) {
        PaymentContext context = new PaymentContext();
        
        // Use new Bitcoin payment strategy
        context.setPaymentStrategy(new BitcoinPayment("1A2b3C4d5E6f"));
        context.payAmount(1500);
        // Output: Paid 1500.0 using Bitcoin: 1A2b3C4d5E6f
    }
}
```

---

## 13. Strategy vs Other Patterns

```mermaid
graph TB
    Strategy[Strategy Pattern]
    
    Strategy -->|Similar but different| State[State Pattern<br/>Changes behavior based on state<br/>State transitions automatic]
    Strategy -->|Alternative| Template[Template Method<br/>Define skeleton in base class<br/>Subclasses override steps]
    Strategy -->|Can combine| Factory[Factory Pattern<br/>Factory creates strategies<br/>Strategy executes algorithm]
    Strategy -->|Can combine| Decorator[Decorator Pattern<br/>Decorator adds features<br/>Strategy changes algorithm]
    
    style Strategy fill:#4CAF50,color:#fff,stroke:#2E7D32,stroke-width:3px
    style State fill:#2196F3,color:#fff
    style Template fill:#FF9800,color:#fff
    style Factory fill:#9C27B0,color:#fff
    style Decorator fill:#00BCD4,color:#fff
```

---

## 14. SOLID Principles Applied

### How Strategy Pattern Follows SOLID

```mermaid
graph TB
    subgraph "SOLID Principles in Strategy Pattern"
        S[Single Responsibility<br/>Each strategy = one algorithm<br/>Context delegates only]
        O[Open/Closed Principle<br/>Open for new strategies<br/>Closed for modification]
        L[Liskov Substitution<br/>All strategies substitutable<br/>through common interface]
        I[Interface Segregation<br/>Simple interface<br/>Only necessary methods]
        D[Dependency Inversion<br/>Context depends on abstraction<br/>Not concrete strategies]
    end
    
    S --> Strategy[Strategy Pattern ✓]
    O --> Strategy
    L --> Strategy
    I --> Strategy
    D --> Strategy
    
    style Strategy fill:#4CAF50,color:#fff,stroke:#2E7D32,stroke-width:3px
    style S fill:#2196F3,color:#fff
    style O fill:#FF9800,color:#fff
    style L fill:#9C27B0,color:#fff
    style I fill:#00BCD4,color:#fff
    style D fill:#F44336,color:#fff
```

---

## 15. Your Implementation Analysis

### Code Structure Breakdown

**PaymentStrategy.java** (Strategy Interface):
```java
✅ Simple, focused interface
✅ Single method: pay(amount)
✅ All strategies implement this
✅ Defines contract for all payment methods
```

**CreditCardPayment.java** (Concrete Strategy):
```java
✅ Implements PaymentStrategy
✅ Has specific data: cardNumber, name
✅ Encapsulates credit card logic
✅ Independent of other strategies
```

**PayPalPayment.java** (Concrete Strategy):
```java
✅ Implements PaymentStrategy
✅ Has specific data: email
✅ Encapsulates PayPal logic
✅ Can be tested independently
```

**UpiPayment.java** (Concrete Strategy):
```java
✅ Implements PaymentStrategy
✅ Has specific data: upiId
✅ Encapsulates UPI logic
✅ Easy to add validations
```

**PaymentContext.java** (Context):
```java
✅ Holds reference to strategy
✅ Delegates to strategy.pay()
✅ Allows strategy switching
✅ Decoupled from concrete strategies
```

**StrategyDemo.java** (Client):
```java
✅ Creates context once
✅ Creates different strategies
✅ Switches strategies at runtime
✅ Clean, readable code
```

---

## 16. Improvement Suggestions

```mermaid
graph TB
    subgraph "Current Implementation"
        C1[Basic strategy switching]
        C2[No validation]
        C3[Direct strategy creation]
    end
    
    subgraph "Possible Enhancements"
        E1[Add strategy validation<br/>Check null strategy]
        E2[Add payment validation<br/>Amount > 0, etc.]
        E3[Use Strategy Factory<br/>Create strategies dynamically]
        E4[Add logging/auditing<br/>Track all payments]
        E5[Add return types<br/>Success/failure status]
        E6[Add exception handling<br/>Payment failures]
    end
    
    C1 -.-> E1
    C2 -.-> E2
    C2 -.-> E4
    C3 -.-> E3
    C2 -.-> E5
    C2 -.-> E6
    
    style E1 fill:#4CAF50,color:#fff
    style E2 fill:#4CAF50,color:#fff
    style E3 fill:#2196F3,color:#fff
    style E4 fill:#FF9800,color:#fff
    style E5 fill:#9C27B0,color:#fff
    style E6 fill:#F44336,color:#fff
```

### Enhanced Version with Validation

```java
// Enhanced Strategy Interface with return type
public interface PaymentStrategy {
    boolean pay(double amount) throws PaymentException;
}

// Enhanced Context with validation
public class PaymentContext {
    private PaymentStrategy paymentStrategy;

    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        if (paymentStrategy == null) {
            throw new IllegalArgumentException("Payment strategy cannot be null");
        }
        this.paymentStrategy = paymentStrategy;
    }

    public boolean payAmount(double amount) {
        if (paymentStrategy == null) {
            throw new IllegalStateException("Payment strategy not set");
        }
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        
        try {
            return paymentStrategy.pay(amount);
        } catch (PaymentException e) {
            System.err.println("Payment failed: " + e.getMessage());
            return false;
        }
    }
}

// Enhanced Concrete Strategy with validation
public class CreditCardPayment implements PaymentStrategy {
    private String cardNumber;
    private String name;

    public CreditCardPayment(String cardNumber, String name) {
        if (cardNumber == null || cardNumber.length() < 13) {
            throw new IllegalArgumentException("Invalid card number");
        }
        this.cardNumber = cardNumber;
        this.name = name;
    }

    @Override
    public boolean pay(double amount) throws PaymentException {
        // Simulate payment processing
        if (amount > 10000) {
            throw new PaymentException("Transaction limit exceeded");
        }
        System.out.println("Paid " + amount + " using Credit Card: " + cardNumber);
        return true;
    }
}
```

---

## 17. Strategy Pattern with Factory

### Combining Strategy with Factory Pattern

```mermaid
graph TB
    Client[Client] -->|1. Requests strategy| Factory[StrategyFactory]
    Factory -->|2. Creates appropriate strategy| Strategy[PaymentStrategy]
    Client -->|3. Sets in context| Context[PaymentContext]
    Context -->|4. Uses| Strategy
    
    Factory -.->|Creates| CC[CreditCardPayment]
    Factory -.->|Creates| PP[PayPalPayment]
    Factory -.->|Creates| UPI[UpiPayment]
    
    style Factory fill:#FF9800,color:#fff
    style Strategy fill:#4CAF50,color:#fff
```

```java
// Strategy Factory
public class PaymentStrategyFactory {
    public static PaymentStrategy createStrategy(String type, String... params) {
        switch (type.toLowerCase()) {
            case "creditcard":
                return new CreditCardPayment(params[0], params[1]);
            case "paypal":
                return new PayPalPayment(params[0]);
            case "upi":
                return new UpiPayment(params[0]);
            default:
                throw new IllegalArgumentException("Unknown payment type: " + type);
        }
    }
}

// Usage
PaymentContext context = new PaymentContext();

// Create strategies using factory
PaymentStrategy strategy1 = PaymentStrategyFactory.createStrategy(
    "creditcard", "1234-5678", "John Doe"
);
context.setPaymentStrategy(strategy1);
context.payAmount(1000);

PaymentStrategy strategy2 = PaymentStrategyFactory.createStrategy(
    "paypal", "john@example.com"
);
context.setPaymentStrategy(strategy2);
context.payAmount(500);
```

---

## 18. Complete Execution Flow

```mermaid
flowchart TD
    Start([Application Start]) --> Main[StrategyDemo.main]
    Main --> CreateContext[Create PaymentContext]
    
    CreateContext --> Payment1[Payment 1: Credit Card]
    Payment1 --> CreateCC[new CreditCardPayment]
    CreateCC --> SetCC[setPaymentStrategy]
    SetCC --> PayCC[payAmount 1000]
    PayCC --> OutputCC[Output: Paid 1000 using Credit Card...]
    
    OutputCC --> Payment2[Payment 2: PayPal]
    Payment2 --> CreatePP[new PayPalPayment]
    CreatePP --> SetPP[setPaymentStrategy]
    SetPP --> PayPP[payAmount 500]
    PayPP --> OutputPP[Output: Paid 500 using PayPal...]
    
    OutputPP --> Payment3[Payment 3: UPI]
    Payment3 --> CreateUPI[new UpiPayment]
    CreateUPI --> SetUPI[setPaymentStrategy]
    SetUPI --> PayUPI[payAmount 750]
    PayUPI --> OutputUPI[Output: Paid 750 using UPI...]
    
    OutputUPI --> End([End])
    
    style Start fill:#4CAF50,color:#fff
    style End fill:#F44336,color:#fff
    style CreateContext fill:#2196F3,color:#fff
    style PayCC fill:#FF9800,color:#fff
    style PayPP fill:#FF9800,color:#fff
    style PayUPI fill:#FF9800,color:#fff
```

---

## 19. Testing Strategies Independently

```mermaid
graph LR
    subgraph "Unit Testing Benefits"
        T1[Test CreditCard<br/>independently]
        T2[Test PayPal<br/>independently]
        T3[Test UPI<br/>independently]
        T4[Mock Context<br/>easily]
        T5[Test each algorithm<br/>in isolation]
    end
    
    T1 --> Result[Easy Testing<br/>High Coverage]
    T2 --> Result
    T3 --> Result
    T4 --> Result
    T5 --> Result
    
    style Result fill:#4CAF50,color:#fff,stroke:#2E7D32,stroke-width:3px
```

### Example Unit Test

```java
import org.junit.Test;
import static org.junit.Assert.*;

public class PaymentStrategyTest {
    
    @Test
    public void testCreditCardPayment() {
        PaymentStrategy strategy = new CreditCardPayment("1234-5678", "John");
        // Test that it doesn't throw exception
        strategy.pay(100.0);
    }
    
    @Test
    public void testPayPalPayment() {
        PaymentStrategy strategy = new PayPalPayment("john@example.com");
        strategy.pay(50.0);
    }
    
    @Test
    public void testContextSwitchesStrategies() {
        PaymentContext context = new PaymentContext();
        
        // Set and use first strategy
        PaymentStrategy cc = new CreditCardPayment("1234", "John");
        context.setPaymentStrategy(cc);
        context.payAmount(100);
        
        // Switch to second strategy
        PaymentStrategy pp = new PayPalPayment("john@example.com");
        context.setPaymentStrategy(pp);
        context.payAmount(50);
        
        // Both should work without issues
    }
}
```

---

## 20. Summary

### Strategy Pattern Overview

| Aspect | Description |
|--------|-------------|
| **Pattern Type** | Behavioral |
| **Purpose** | Define family of algorithms, make them interchangeable |
| **Problem Solved** | Eliminates conditional statements for algorithm selection |
| **Key Benefit** | Runtime algorithm switching, clean code |
| **Trade-off** | More classes vs cleaner logic |
| **Best For** | Multiple interchangeable algorithms, runtime selection |

### Your Implementation Summary

✅ **Perfectly implemented** - All Strategy pattern principles followed  
✅ **Clean separation** - Each payment method is independent  
✅ **Runtime flexibility** - Can switch payment methods anytime  
✅ **Easy to extend** - Add new payment methods without changing existing code  
✅ **Testable** - Each strategy can be tested independently  
✅ **Follows SOLID** - Open/Closed, Single Responsibility, Dependency Inversion  

### Expected Output

```
Paid 1000.0 using Credit Card: 1234-5678-9876-5432
Paid 500.0 using PayPal: john@example.com
Paid 750.0 using UPI: john@upi
```

---

## GitHub Integration

Copy this entire markdown file to your GitHub repository. All Mermaid diagrams will render automatically!

### Recommended File Structure:
```
your-repo/
├── README.md (this documentation)
├── src/
│   └── com/example/strategy/
│       ├── PaymentStrategy.java
│       ├── CreditCardPayment.java
│       ├── PayPalPayment.java
│       ├── UpiPayment.java
│       ├── PaymentContext.java
│       └── StrategyDemo.java
├── test/
│   └── PaymentStrategyTest.java
└── docs/
    └── STRATEGY_PATTERN.md
```
