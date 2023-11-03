## Version A (callback)
The function A defines its expectation in form of a callback-parameter which defines
the signature of the function to call.
```mermaid
flowchart TD
    Initiator["Initiator (knows of funcB)"] -->|funcB as callback| funcA
    funcA -->|1| callback["callback (which is funcB)"]
    callback -->|"B"| funcA
```
```typescript
function funcA(callback: (paramA: number) => string) {
    console.log(callback(1));
}

funcA(function (paramA: number) {
    return "B" + paramA;
}); // console: "B1"
funcA(function (paramA: number) {
    return "C" + paramA;
}); // console: "C1"
```

## Version B (DI):
The function A has access to a variable which is of type IFuncB. This interface defines 
the signature of the function to call.
```mermaid
flowchart TD
    Initializer -->|creates with funcB| Service
    Initiator -->|calls funcA| Service
    Service -->|result| Initiator
```
```typescript
interface IFuncB {
    funcB(paramA: number): string;
}

class A {
    private funcB: IFuncB;

    constructor(private func: IFuncB) {
        this.funcB = func;
    }

    funcA() {
        console.log(this.funcB(1));
    }
}

(new A(function (paramA: number) {
    return "B" + paramA
})).funcA(); // console: "B1"
(new A(function (paramA: number) {
    return "C" + paramA
})).funcA(); // console: "C1"
```

## Version C (Pub/Sub)
```mermaid
flowchart TD
    Initiator -->|subscribes| OutputQueue
    funcB -->|subscribes| InputQueue
    InputQueue -->|calls subscriber with input| funcB
    funcB --> |publishes result| OutputQueue
    Initiator -->|publishes| InputQueue
    OutputQueue -->|calls subscriber with output| Initiator
```
