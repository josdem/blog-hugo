+++
itle = "Executors"
tags = ["josdem", "techtalks","programming","technology","java"]
categories = ["techtalk", "code","java"]
description = "Executors are part of concurrent Java API, and the intention is to have a high-level managing thread; therefore, executors typically manage a pool of threads."
date = 2024-07-23T09:25:01-04:00
+++
Executors are part of concurrent Java API, and the intention is to have a high-level managing thread; therefore, executors typically manage a pool of threads.
```java
ExecutorService executor = Executors.newFixedThreadPool(3);
```
In the previous code, we created a thread pool with three threads of capacity. It would be best if you stopped executors at some point; otherwise, they will keep listening for new tasks; the usual way to stop it is:
```java
executor.shutdown();
```
Here is a simple executor example:
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ExecutorExample {

  private ExecutorService executor = Executors.newSingleThreadExecutor();

  private void start() throws InterruptedException {
    executor.execute(task);
    executor.shutdown();
  }

  private Runnable task = () -> System.out.println("Asynchronous task");

  public static void main(String[] args) throws InterruptedException {
    new ExecutorExample().start();
  }
}
```
Sometimes, when you are working with executors, you need to use atomic operations; this is an example using `AtomicInteger`; internally, it has an int value and has atomic operations like `incrementAndGet()`:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.stream.IntStream;

public class ExecutorAtomic {

  private static final Integer MAX_PERIOD_TIME = 5;
  private AtomicInteger atomic = new AtomicInteger(0);
  private ExecutorService executor = Executors.newFixedThreadPool(3);

  private Integer start() throws InterruptedException {
    IntStream.range(0, 3).forEach(i -> executor.execute(atomic::incrementAndGet));
    executor.shutdown();
    executor.awaitTermination(MAX_PERIOD_TIME, TimeUnit.SECONDS);
    return atomic.get();
  }

  public static void main(String[] args) throws InterruptedException {
    var result = new ExecutorAtomic().start();
    assert result == 3;
  }
}
```

**Callables and Futures**

Executors support another kind of task named Callable, which is a functional interface just like runnable, but instead of being void, they return a value. Callables can be submitted to executor services. Since the `submit()` method doesn’t wait until the task is completed, the executor service cannot return the callable result directly. Instead, the executor returns a particular result of type `Future`, which can be used to retrieve the actual result at a later point in time.

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class ExecutorCallable {

  private static final Integer MAX_PERIOD_TIME = 10;

  private ExecutorService executor = Executors.newFixedThreadPool(3);

  private Integer start() throws InterruptedException, ExecutionException {
    var future = executor.submit(new CallableThread());
    final var result = future.get();
    executor.shutdown();

    executor.awaitTermination(MAX_PERIOD_TIME, TimeUnit.SECONDS);
    return result;
  }

  public static void main(String[] args) throws InterruptedException, ExecutionException {
    var result = new ExecutorCallable().start();
    assert result == 3;
  }
}

class CallableThread implements Callable<Integer> {

  @Override
  public Integer call() throws InterruptedException {
    final var wait = 3;
    TimeUnit.SECONDS.sleep(wait);
    return wait;
  }
}
```

Since Java 8, the `CompletableFuture` class was introduced and also implements this `Future` interface. It provides an `isDone()` method to check whether the computation is done or not, and a `get()` method to retrieve the result of the computation when it is done.

```java
import java.util.concurrent.*;

public class ExecutorCompletableFuture {

  private static final Integer MAX_PERIOD_TIME = 30;

  private ExecutorService executor = Executors.newFixedThreadPool(3);

  private Integer start() throws InterruptedException, ExecutionException {
    CompletableFuture<Integer> completableFuture = new CompletableFuture<>();

    executor.submit(
        () -> {
          try {
            final Integer wait = 3;
            completableFuture.complete(wait);
            TimeUnit.SECONDS.sleep(wait);
          } catch (InterruptedException ie) {
            ie.printStackTrace();
          }
        });

    final Integer result = completableFuture.get();
    executor.shutdown();

    executor.awaitTermination(MAX_PERIOD_TIME, TimeUnit.SECONDS);
    return result;
  }

  public static void main(String[] args) throws InterruptedException, ExecutionException {
    Integer result = new ExecutorCompletableFuture().start();
    assert result == 3;
  }
}
```

A cool functionality is to use asynchronous staic methods such as `runAsync`, `supplyAsync` and `thenApplyAsync`. If you want to run some background task asynchronously and don’t want to return anything from the task, then you can use `runAsync()`. It returns `CompletableFuture<Void>`

```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.TimeUnit;

public class CompletableFutureRunAsync {

  private void start() throws InterruptedException, ExecutionException {
    CompletableFuture<Void> completableFuture =
        CompletableFuture.runAsync(
            () -> {
              try {
                TimeUnit.SECONDS.sleep(3);
              } catch (InterruptedException ie) {
                ie.printStackTrace();
              }
            });
    completableFuture.get();
  }

  public static void main(String[] args) throws InterruptedException, ExecutionException {
    new CompletableFutureRunAsync().start();
  }
}
```

`CompletableFuture.supplyAsync()` is used when you want to provide a value using a `Supplier<T>` running a taks that is asynchronously completed, then use that value in a function with `thenApply()` method


```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.function.Function;
import java.util.function.Supplier;

public class CompletableFutureSupplyAsync {

  private String start() throws InterruptedException, ExecutionException {
    final Supplier<String> supplier = () -> "hello";
    final Function<String, String> function = value -> value + " world";

    CompletableFuture<String> completableFuture =
        CompletableFuture.supplyAsync(supplier).thenApply(function);
    return completableFuture.get();
  }

  public static void main(String[] args) throws InterruptedException, ExecutionException {
    String result = new CompletableFutureSupplyAsync().start();
    assert result.equals("hello world");
  }
}
```

If you want to run async logic in a separate `CompletableFuture` you can use `thenApplyAsync()`

```java
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.function.Supplier;

public class CompletableFutureThenApplyAsync {

  private String start() throws InterruptedException, ExecutionException {
    final Supplier<String> supplier = () -> "hello";
    CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(supplier);

    CompletableFuture<String> future = completableFuture.thenApplyAsync(value -> value + " world");

    return future.get();
  }

  public static void main(String[] args) throws InterruptedException, ExecutionException {
    String result = new CompletableFutureThenApplyAsync().start();
    assert result.equals("hello world");
  }
}
```

To browse the project [here](https://github.com/josdem/java-workshop), to download the project:

```bash
git clone https://github.com/josdem/java-workshop.git
cd executors
```

To run the code:

```bash
javac ${JAVA_PROGRAM}.java
java -ea ${JAVA_PROGRAM}
```


[Return to the main article](/techtalk/java)
