## **1. What Is Reactive Programming?**

Reactive programming is an **asynchronous, non-blocking, event-driven** approach where data is treated as **streams** and consumers “react” to changes as they happen.

**Key characteristics:**

- Non-blocking execution
    
- Event-driven
    
- Backpressure-aware
    
- Scales well with high concurrency
    
- Ideal for I/O-heavy systems
    

---

## **2. Popular Reactive Libraries**

|Library|Notes|
|---|---|
|**Project Reactor**|Used with Spring WebFlux (`Mono`, `Flux`)|
|**RxJava**|ReactiveX for Java (`Observable`, `Flowable`)|
|**Akka Streams**|Highly scalable, actor-based|
|**Mutiny**|Quarkus reactive library|
|**Java Flow API**|Standard Java 9+ minimal reactive API|

---

## **3. What Is Backpressure?**

Backpressure = a **control mechanism** that allows slow consumers to tell fast publishers:

> “Send data at the speed I can handle.”

In Java Flow API, backpressure is implemented with:

```java
subscription.request(n);
```

This prevents overload and ensures stability.

---

## **4. Java Flow API (Java 9+)**

The Flow API defines **4 core interfaces** used by all reactive libraries:

### **1. Publisher**

Produces data and allows subscribers to register.

### **2. Subscriber**

Receives data, errors, and completion events.

### **3. Subscription**

Controls data flow (request / cancel).  
Enables backpressure.

### **4. Processor<T,R>**

A component that is **both** Subscriber **and** Publisher.  
Used to transform or filter data in a reactive pipeline.

---

## **5. When to Use Reactive Programming**

Use reactive when:

✔ Many parallel I/O tasks (web calls, DB calls, messaging)  
✔ High concurrency with limited threads  
✔ Need streaming, real-time updates  
✔ Need backpressure control  
✔ Microservices communicating asynchronously  
✔ Event-driven or pipeline-like applications

---

## **6. When NOT to Use Reactive Programming**

Avoid reactive when:

✘ You have CPU-heavy or blocking logic  
✘ Code becomes too complex for the team  
✘ Your system is simple and synchronous is enough  
✘ You need strong debugging, simple stack traces

---

## **7. When to Use a Processor**

A **Processor** is used when you need:

- Transformation inside the pipeline
    
- Filtering, mapping, validation
    
- Multi-stage pipelines
    
- Custom processing between publisher and subscriber
    

Example:  
Sensor → Processor (convert raw JSON → object) → Subscriber

---

# **8. Real-Life Java Flow API Example (With Short, Clear Explanation)**

```java
import java.util.concurrent.Flow;
import java.util.concurrent.SubmissionPublisher;

public class ReactiveExample {
    public static void main(String[] args) throws Exception {

        SubmissionPublisher<Integer> publisher = new SubmissionPublisher<>();

        MyProcessor processor = new MyProcessor();
        MySubscriber subscriber = new MySubscriber();

        // Build pipeline: publisher → processor → subscriber
        publisher.subscribe(processor);
        processor.subscribe(subscriber);

        // Publish data
        for (int i = 1; i <= 5; i++) {
            publisher.submit(i);
        }

        publisher.close();
        Thread.sleep(1000); // wait for async work
    }
}
```

---

## **Processor**

```java
class MyProcessor extends SubmissionPublisher<Integer>
        implements Flow.Processor<Integer, Integer> {

    private Flow.Subscription subscription;

    @Override
    public void onSubscribe(Flow.Subscription subscription) {
        this.subscription = subscription;
        subscription.request(1); // backpressure: request items slowly
    }

    @Override
    public void onNext(Integer item) {
        submit(item * 2);  // transform value
        subscription.request(1); // ask for next item
    }

    @Override
    public void onError(Throwable t) { }
    @Override
    public void onComplete() { close(); }
}
```

### **Processor Explanation (short)**

- It receives items from upstream
    
- Transforms each item by doubling it
    
- Publishes the transformed item downstream
    
- Uses `request(1)` to control flow (backpressure)
    

---

## **Subscriber**

```java
class MySubscriber implements Flow.Subscriber<Integer> {

    @Override
    public void onSubscribe(Flow.Subscription subscription) {
        subscription.request(1); // ask for one item at a time
    }

    @Override
    public void onNext(Integer item) {
        System.out.println("Received: " + item);
    }

    @Override
    public void onError(Throwable t) { }
    @Override
    public void onComplete() {
        System.out.println("Done!");
    }
}
```

### **Subscriber Explanation (short)**

- Requests data slowly (`request(1)`)
    
- Prints each item it receives
    
- Ends with "Done!" when stream completes
    

---

## **Output**

```
Received: 2
Received: 4
Received: 6
Received: 8
Received: 10
Done!
```

---

# **9. Summary **

### 🔹 Reactive Programming

Async, non-blocking, event-driven, stream-based.

### 🔹 Libraries

Reactor, RxJava, Akka Streams, Mutiny, Java Flow API.

### 🔹 Flow API Interfaces

Publisher, Subscriber, Subscription, Processor.

### 🔹 Backpressure

Mechanism to prevent overload using `request(n)`.

### 🔹 When to use reactive

I/O heavy, streaming, high concurrency, event-driven pipelines.

### 🔹 Processor

A middle step that transforms data in the stream.

### 🔹 Example Pipeline

Publisher → Processor → Subscriber (with backpressure).
