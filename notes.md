## Apropos
* Content is versioned in git.  commits, issues and pull-requests welcome!
  @[https://www.github.com/earizon/JAVA]
* This __"cheat-sheet with steroids"__ presents an always-growing set of
  "TOP Java recipes" well ordered&amp;classified extracted from 
  * DZone
  * InfoQ
  * Redhat Developers
  * Medium
  * baeldung
  * github
  * Author's own experience in different projects.
  * ...

## External Links [[{]]
* @[https://openjdk.java.net/jeps/0] JAVA Enhancements proposals
* @[https://docs.oracle.com/javase/specs/] Lang & VM specs
* @[https://docs.oracle.com/javase/17/docs/api/]
* @[https://docs.oracle.com/javase/11/docs/api/]
* @[https://docs.oracle.com/javase/8/docs/api/]
* @[https://adoptopenjdk.net/]
* @[https://hg.openjdk.java.net/jdk/jdk] OpenJDK@Mercurial
* @[https://wiki.openjdk.java.net/]
* @[https://openjdk.java.net/]
* @[https://en.wikipedia.org/wiki/List_of_Java_virtual_machines]
* Eclipe Tools for [QA]
  * @[https://help.eclipse.org/neon/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Ftasks%2Ftask-improve_code_quality.htm&cp=1_3_9]
* @[https://github.com/douglascraigschmidt/LiveLessons] Douglas Craigs Schmidt Java Lessons
* @[https://java-source.net/] Production-ready software developed with Java, from DDBBs, caches, servers, ...

## Bibliography

- ```
  (Must READ)
  Effective Java 3rd Edition, Joshua Bloch
  ISBN-10: 0134685997
  ISBN-13: 978-0134685991
  ```

- ```
  Java Performance: The Definitive Guide, by Scott Oaks
  ISBN-10: 1449358454
  ISBN-13: 978-1449358457
  ```
[[}]]

# JAVA LANGUAGE 101 [[{java_lang.101]]

## Hello World
  ```
  import static java.lang.System.out;
  public class HelloWorld {
    public static void main(String[] args){
      if ( args == null || args.length != 1 ){
         throw RuntimeException("Try again!");
      }
      out.printf("Hello %s !!", args[0]);
    }
  }
                                                 BEFORE JAVA 11:
  $ javac ./HelloUniverse.java                   <·· compile (./HelloUniverse.class generated)
  $ java  ./HelloUniverse  arg1                  <·· start up JVM, load class, execute code
  $ java  SomeClass.class                        <·· Java launcher 1.0+
  $ java  SomeLib.jar                            <·· Java launcher 1.0+: Run    JAR's main class
  $ java  SomeLib.jar                            <·· Java launcher 9.0+: Run Module's main class

                                                 AFTER JAVA 11 (JEP 330)
  $ java HelloUniverse.java arg1                 <·· compile, start up JVM, load class, execute code
    --add-modules=ALL-DEFAULT                        (no *.class generated on disk). Options include:
    └─────────┬─────────────┘                        --source ${version}: Mandatory if '.java' extension
              ·                                          is missing or to set a specific version
              ·
              └································· By default compiled code is part of an 'unnamed' module.
                                                 This provides access to ALL standard modules in JDK.

  $ cat script.java
  #!/path/to/java --source $version  <·· UNIX scripting using "Shebang notation"
  public class HelloUniverse{
    ...
  }
  ```

## JBang: Simplified Java [[{]]
* [Official Doc](https://www.jbang.dev/documentation/guide/latest/debugging.html)
* @[https://www.youtube.com/watch?v=X4x2jM3Y0uE]

* Install JDKs:
  ```
  $ jbang jdk list                                        <·· List installed JDKs
  $ jbang jdk install 17                                  <·· Install JDK 17
  $ jbang jdk home                                        <·· Show installation path
  ```

* Daily ussage:
  ```
    helloworld.java                                       <·· Exec like: 
                                                              $ jbang helloworld.java
                                                              $ jbang --jfr helloworld.java    # Run with Flight Recorder
                                                              $ jbang --debug helloworld.java  # Suspend execution and 
                                                                                               # wait until you connect 
                                                                                               # a debugger
    
    ///usr/bin/env jbang "$0" "$@" ; exit $?

    //DEPS ch.qos.reload4j:reload4j:1.2.19                <·· gradle style locator to compiled repository
    //DEPS com.github.jbangdev:jbang:v1.2.3               <·· Git link like dependency "a la Go". 
    //DEPS my.comp.proj:mypck:${VER}                      <·· OS Enviromental variables allowed

    //JAVA 17+                                            <·· Force Java 17 or higher.
                                                              //JAVA 17 <·· Force 17 version

    //REPOS id=https://maven.google.com/                  <·· Add custom repository

    //FILES resource.properties                           <·· Add resource
    //FILES META-INF/resources/index.html=index.html      <·· Add resource "mounted" on META-INFO/resource/index.html
  
    class helloworld { 
      public static void main(String[] args) {
        if(args.length==0) {
            System.out.println("Hello World!");
        } else {
            System.out.println("Hello " + args[0]);
        }
      }
    }
  ```
[[}]]

## Strings  [[{java_lang.strings,java_lang.101,PM.WiP]]
  ```
  final String
     output1 = String .format ("%s = %d", "joe", 35), ← Format string
     output2 = String .format ("%4d",100);            ←
  See also notes about "Format String Checker"

  final String[] args = ...
  final String s1 = String.join(List.of(args),"'" )); // ← alt 1: String array to CSV
  final String s1 = String.join(","          ,args)); // ← alt 2: String array to CSV

  s1.isBlank()                  <·· Java 11+, true for Empty | only-white-spaces
  s1.lines()                    <·· Java 11+, returns string[] collecting all substrings split by lines.
                                    System.out.println(
                                       "JD\nJD\nJD".str.lines().collect(Collectors.toList())
                                    );
  s1.strip()                    <·· Java 11+, like trim() but unicode-aware
  s1.stripLeading()
  s1.stripTrailing()
  s1.repeat(int n)              <·· Java 11+: repeats string  n times.

  final String textBlock = """  <·· Java 15+: https://openjdk.org/jeps/378
                                    Rules:
                                    - \r\n is converted to \n.
       line1                        - Indent (spaces, tabs) are removed by default.
       line2                        - Traling whitespace is removed by default.
       line3    \s                  <·· \s: Avoid removing trailing white-spaces
  |1234                             <·· from here on, indent with 4 white-spaces.
       """

  java.util.StringJoiner  (1.8+) Concatenate Strings
  https://docs.oracle.com/javase/8/docs/api/java/util/StringJoiner.html
  ```
- Ex: `"[George:Sally:Fred]"` may be constructed as follows:
  ```
  final StringJoiner sj = new StringJoiner(
                             ":" /* Delimiter */,
                             "[" /* prefix */,
                             "]" /* sufix */);
  sj.add("George").add("Sally").add("Fred");
  String desiredString = sj.toString();

  Collectors.joining(CharSequence):

  List<Integer> numbers = Arrays.asList(1, 2, 3, 4);
  String commaSeparatedNumbers = numbers.stream()
      .map(i -> i.toString())
      .collect( Collectors.joining(", ") );
  ```

## StringBuffer/StringBuilder

* Much faster when concatenating strings at runtime.
  (See also java.io.StringWriter: 'character stream' collecting output to StringBuffer).
* StringBuffer   is thread-safe.
* StringBuilder  is faster. (when thread-safety is not needed)
  ```
  for (int i = 0; i < 0 ; i++) { sbuffer.append(""); } // ← 2241 millisec
  for (int i = 0; i < 0 ; i++) { sbuildr.append(""); } // ←  753 millisec  ~3.0x faster!!!
  ```

### forcing String pool reuse [[{jvm.memory,performance.memory,scalability.jvm,java_lang.strings]]

- Strings are immutable.
- Stored on the heap.
- Java manages a string pool in memory, reusing strings whenever possible.
  ```
  String string01 = "297",                                string01 == string02 : true
         string02 = "297",                                string01 == string03 : false
         string03 = new Integer(297).toString(),          string01 == string04 : true
         string04 = new Integer(297).toString()           string05 == string01 : false
                    .intern() ;
         string05 = new String("297")

   1: WARN:  Pool reuse does not work for dynamically created strings.
      If we consider that the computed String will be used quite often,
      we can force the JVM to add it to the string pool by adding the
      .intern() method at the end of computed string.
  ```
[[performance.memory}]]
[[java_lang.strings}]]

## Loading a resource: [[{]]
 ```
 URL u = this.getClass().getResource("foo.txt");      // Alt 1.1. Attemp to load foo.txt relative to "this" package
 InputStream i = this.getClass().                        and with class-loader of "this". Use "/x/y/foo.txt" for
                        .getResourceAsStream("foo.txt")  <·· Fetch as stream.

 Thread.currentThread()                          // Alt 2.  Use context-class-loader. It will NOT resolve
    .getContextClassLoader()                                the name according to any package (it must be absolutely referenced)
    .getResource("foo.txt");

 System.class.getResource(name);                 // Alt 3.  Use system-class-loader. It will NOT resolve
                                                            the name according to any package.
                                                            you won't be able to put anything into the java.lang package .
  ```
[[}]]

[[{scalability.101,jvm.101,java_lang.101.best_patterns]]
## Optimization Tips 

- To minimize the memory footprint, limit scope-of-variables as much as possible.
- Explicitly refer to null obsolete references making them eligible for GC.
- Avoid finalizers. They slow down the process and they do not guarantee anything.
  Prefer phantom references for cleanup work.
- Do not use strong references where weak or soft references apply.
  The most common memory pitfalls are caching scenarios,when data
  is held in memory even if it might not be needed.
- Explicitly specify heap size for the JVM when running the application:
  * allocate a reasonable initial and maximum amount of memory for the heap.
    ```
     Initial heap size -Xms512m   – set initial heap     size to  512 megabytes
     Maximum heap size -Xmx1024m  – set maximum heap     size to 1024 megabytes
     Thread stack size -Xss128m   – set thread stack     size to  128 megabytes
     Young genera.size -Xmn256m   – set young generation size to  256 megabytes
    ```
  * REF: @[https://dzone.com/articles/heap-memory-in-java-performance-testing?utm_source=www.oficina24x7.com]
    ```
    - Initial Heap Size: -Xms: >= 1/64th of physical memory || reasonable minimum.
    - Maximum Heap Size: -XmX: <= 1/4 th of physical memory || 1GB.
                         ^^^^^
                  - Set -Xms equal to -Xmx to prevent pauses caused by heap expansion
                  ☞  Setting Xms/Xmx increase GC predictability
    ```
  * JVM settings are recommended for:
    ```
    PRODUCTION TIER       REPLICA SERVERS           STANDALONE
    -server               -server                   -server
    -Xms24G -Xmx24G        -Xms4G -Xmx4G            -Xms32G -Xmx32G
    └───────────────────────────┬─────────────────────────────────┘
                      PRODUCTION/REPLICA/STANDALONE:

                      -XX:PermSize=512m
                      -XX:+UseG1GC
                      -XX:MaxGCPauseMillis=200     ← soft goal (JVM) best effort
                      -XX:ParallelGCThreads=20     ← value depends on hosting hardware
                      -XX:ConcGCThreads=5          ← value depends on hosting hardware
                      -XX:InitiatingHeapOccupancyPercent=70 ← Use 0 to force constant
                                                              GC cycles

       WARN, WARN, WARN:
       There are 600+ arguments that you can pass to JVM to fine-tune GC and memory
       If you include other aspects, the number of JVM arguments will easily cross
       1000+.
       (Or why Data Scientist end up using Python)
    ```
* If app OutOfMemoryError-crashes, extra info about memory leak can be obtained through
    –XX:HeapDumpOnOutOfMemory , creating a heap dump file
* Use `-verbose:gc`  to get the garbage collection output.
* See also [Eclipse Memory Analyzer Manual:
  @[https://help.eclipse.org/2019-06/index.jsp?topic=%2Forg.eclipse.mat.ui.help%2Fwelcome.html&cp=62]

### Commom Memory Leaks pitfalls

* [REF@jiderhamn.se](http://java.jiderhamn.se/2012/02/26/classloader-leaks-v-common-mistakes-and-known-offenders/)
* Logging frameworks (Apache Commons Logging/log4j/java.util.logging/...)
  trigger classloader leaks if   logging framework is supplied outside of
  the web application, such as within the Application Server.
  *  Add next cleanup code to ServletContextListener :
     ```
     org.apache.commons.logging.LogFactory.                    // Alt.1
       release(Thread.currentThread().getContextClassLoader());

     org.apache.commons.logging.LogFactory.release(            // Alt.2
         this.getClass().getClassLoader()  );
     ```
[[scalability.101}]]

[[{java.101.modules]]
## Module JDK 9+ 

* foundational JDK APIs of Java SE
* [REF@Oracle.com](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/module-summary.html)
* The JDK implementation of this module provides an implementation of the jrt file system provider to
  enumerate and read the class and resource files in a run-time image.
* The jrt file system can be created by calling `FileSystems.newFileSystem(URI.create("jrt:/"))`

### Packages <!-- { -->
 ```
 java.io 	       Provides for system input and output through data streams,
                       serialization and file system.
 java.lang 	       core classes to the design of the Java programming language.
 java.lang.annotation  Library support for annotations.
 java.lang.invoke      low-level primitives for interacting with the JVM.
 java.lang.module      Classes to support module descriptors and creating configurations
                       of modules by means of resolution and service binding.
 java.lang.ref 	       Provides reference-object classes, with a limited degree of
                       interaction with the garbage collector.
 java.lang.reflect     classes and interfaces for obtaining reflective information
 java.math 	       BigInteger and BigDecimal
 java.net 	       Classes for implementing networking applications.
 java.net.spi 	       Service-provider classes for the java.net package.
 java.nio 	       Defines buffers: containers for data, and an overview of the
                       other NIO packages.
 java.nio.channels     Defines channels, representing connections to entities capable
                       of performing I/O operations, such as files and sockets;
                       selectors, for multiplexed, non-blocking I/O operations.
 java.nio.channels.spi Service-provider classes for the java.nio.channels package.
 java.nio.charset      charsets, decoders, and encoders, for translating bytes <··> Unicode
 java.nio.charset.spi  Service-provider classes for the java.nio.charset package.
 java.nio.file 	       interfaces and classes for the JVM to access files, attributes and FS.
 java.nio.file.attribute Interfaces and classes providing access to file+FS attributes.
 java.nio.file.spi     Service-provider classes for the java.nio.file package.
 java.security 	       Classes+interfaces for the security framework.
 java.security.acl     deprecated.
 java.security.cert    Classes+interfaces for parsing and managing certificates,
                       certificate revocation lists (CRLs), and certification paths.
 java.security.interfaces  Interfaces for generating RSA (Rivest, Shamir and Adleman
                       AsymmetricCipher algorithm) keys as defined in the RSA Laboratory
                       Technical Note PKCS#1, and DSA (Digital Signature Algorithm) keys
                       as defined in NIST's FIPS-186.
 java.security.spec    Classes+interfaces for key specifications and algorithm parameter
                       specifications.
 java.text 	       library to manage text, dates, numbers, and messages in a manner
                       independent of natural languages.
 java.text.spi 	       Service provider classes for the classes in the java.text package.
 java.time 	       Main API for dates, times, instants, and durations.
 java.time.chrono      Generic API for calendar systems other than the default ISO.
 java.time.format      Provides classes to print and parse dates and times.
 java.time.temporal    Access to date and time using fields and units, and date time adjusters.
 java.time.zone        Support for time-zones and their rules.
 java.util 	       Contains the collections framework, i18n support, a service loader,
                       properties, random number generation, string parsing and scanning classes,
                       base64 encoding and decoding, a bit array, and several miscellaneous
                       utility classes.
 java.util.concurrent Utility classes commonly useful in concurrent programming.
 java.util.concurrent.atomic  Small toolkit of classes that support lock-free thread-safe
                       programming on single variables.
 java.util.concurrent.locks Interfaces and classes providing a framework for locking
                       and waiting for conditions that is distinct from built-in
                       synchronization and monitors.
 java.util.function    Functional interfaces for lambda expressions and method references.
 java.util.jar 	       lib. for reading and writing the JAR (Java ARchive) file format,
 java.util.regex       lib for matching character sequences against patterns specified by
                       regular expressions.
 java.util.spi 	       Service provider classes for the classes in the java.util package.
 java.util.stream      Support for functional-style operations on streams of elements, such
                       as map-reduce transformations on collections.
 java.util.zip 	       lib for reading and writing ZIP and GZIP file formats.
 javax.crypto 	       lib for cryptographic operations.
 javax.crypto.interfaces interfaces for Diffie-Hellman keys as defined in RSA Laboratories' PKCS #3.
 javax.crypto.spec     Classes and interfaces for key specifications and algorithm parameter specifications.
 javax.net 	       Provides classes for networking applications.
 javax.net.ssl 	       Provides classes for the secure socket package.
 javax.security.auth   Framework for authentication and authorization.
 javax.security.auth.callback classes necessary for services to interact with applications in order
                       to retrieve information (authentication data including usernames or passwords,
                       for example) or to display information (error and warning messages, for example).
 javax.security.auth.login Pluggable authentication framework.
 javax.security.auth.spi Interface to be used for implementing pluggable authentication modules.
 javax.security.auth.x500 classes that should be used to store X500 Principal and X500 Private
                       Credentials in a Subject.
 javax.security.cert   classes for public key certificates.
 ```
<!-- } -->

[[java.101.modules}]]

## JAVA DONT's [[{java_lang.101.donts,scalability.101,qa.101,PM.WiP]]

* Avoid Nulls, prefer final:
  ```
  WRONG:
  ========================
  SomeClass instance = null;
  if (condition1) {   ← Initialization depends on runtime checks (condition1/2/...).
      instance = val1;   We can forget to add some condition wrongly leaving
  }                     instance to a false null. (Probably in a later
  if (condition2) {   ← interaction, weeks or months after first implementatio
      instance = val2;   when instance is not fresh in our memory)
  }
                      ← At this point we forgot to check for condition3, or maybe
                        condition3 didn't apply at code writing, but appear later on
                        after some unrelated change.
  ...
  serviceX.functionN(..., instance, ...) ← At this point instance can be undefined

  RIGHT:
  ========================
  final SomeClass instance;  ← "final" keywords force compiler to check every possible execution
  if (condition1) {     condition and fail to compile is some branch does not initiliaze
      instance = val1;   instance properly.
  }                     - Final values are safer (to and developer mistakes concurent code).
  if (condition2) {
      instance = val2;   - Final values makes JIT happier.
  }
  ...
  servX.funcN(instance) ← At this point compiler will abort if there is some
                          execution branch where instance rest un-initialized.
  ```

* See also:
  * Inmmutable Objects are faster (and safer)
  @[https://docs.oracle.com/javase/tutorial/essential/concurrency/immutable.html]
  (A final variable is the simplest example of an immutable object)
* Catch blocks must rethrow if the exception can not be fixed on the catch block.
  HINT: 99% of times it can NOT be fixed on the catch block.
  ```
  WRONG                        RIGHT
  ========================     ========================
  try { ...                    try { ...
    // try to update data        // try to update data 
  } catch (IOException e) {    } catch (IOException e) {
      e.printStackTrace();         throw new RuntimeException("Data couldn't be updated", e);
  }                            }
  ```
   The correct "right" code looks weaker, since the exception is propagated ... <br/>
  and that's a good thing, because the error has not been fixed, but some other piece 
  of code (or maybe final user or system operator) will be notified.<br/>
    In the "wrong" code **the original bug is just hidden and unreported**.<br/>
   This will trigger undefined behaviour, null pointers, nightmares and developer's
   non-paid extra hours.<br/>
   This way is of thinking can look counter-intuitive to newcommers, but that's how 
  real world works. errors are ... errors, and they can not be fixed, they just can be 
  reported:<br/>
   In the example above, an IOException is always caused by a full disk, a hardware error, 
  a network error, a bug in the OS, ... There is nothing our code can do other than 
  report back to the IT department to fix it.<br/>
  Erlang is a language designed for 99.99999% uptime systems, 
  [makes it clear](https://amplab.github.io/cs262a-fall2016/notes/18-Go-and-Erlang.pdf).
  * **Crash early**:
    * Let some other process do the error recovery.
    * Do not program defensively.
    * **If you cannot handle the error, don’t try to recover**
* **Avoid Strings as much as possible**. You are converting an strongly typed
  language in a weak runtime one.
  If forced to use strings, try to protect them with (Checker framework) `@Fenum("country")`
  More info [here](./QA_and_testing.txt#Checkerframework).
* **Avoid huge interfaces**. Prefer small, decoupled ones, with few exposed methods
  decoupled interfaces related to security from storage from cache from ...
  (This is unfortunatelly not the case if standard interfaces designed in the 
  original Java libraries, just don't imitate wrong design).
* Avoid interfaces that will ever be implemented by a single class, specially if such
  class is a data-like one with all inmutable (final) fields. Data classes can be
* **Avoid checked exceptions**, specially avoid them to signal the non happy path in
  business operation. As a thumb of rule, each time a checked exception is found
  convert to a runtime (subclass) exception.  More info at:
  * @[https://phauer.com/2015/checked-exceptions-are-evil/]
  * @[https://dzone.com/articles/introduction-to-pragmatic-functional-java]
[[java_lang.101.donts}]]

## JAVA Exceptions [[{java_lang.101.exceptions,qa.error_control]]

- (compiler) checked vs unchecked (Error, RuntimeException and their subclasses).
- Checked: All except Error, RuntimeException and their subclasses
- Error: Exceptional conditions external to the application.
  ```
java.lang.Object
│
└─ java.lang.Throwable   ← Only instances of this (sub/)class are thrown
   │                       in JVM, can be thrown in throw statement or can
   │                       be an argument in catch clause.
   │
   ├─ java.lang.Exception
   │  │
   │  ├─  java.lang.RuntimeException (nonfchecked) <·· Most common error raised by
   │  │                                                developer code
   │  │
   │  └─  java.lang.Exception        (    checked) <·· Don't use . checked exceptions end up
   │                                                   being converted to Runtime Excep.
   │                                                   and bloats the code.
   │
   └─ java.lang.Error                (non-checked) <·· serious problems that app code
                                                       should not try to catch.
                                                       ThreadDeath error, though a "normal" condition,
                                                       is also a subclass of Error because most apps
                                                       should not try to catch it.
  ```
[[java_lang.101.exceptions}]]

## "Optional": Avoid Nulls [[{QA.BILLION_DOLAR_MISTAKE]]

- [REF@zeroturnaround.com](http://files.zeroturnaround.com/pdf/zt_java8_best_practices.pdf)
  ```
  import java.util.Optional;
  Optional<Sgtring> optional = Optional.ofNullable(a); // <·· Create an optional
  optional.map    ( s -> "RebelLabs:" + s);            // <·· Process the optional
  optional.flatMap( s -> Optional.ofNullable(s));      // <·· map a function that returns Optional
  optional.ifPresent(System.out::println);             // <·· run if the value is ther
  
  optional.get();                                      // <·· Alt 1: get the value or throw an exception
  optional.orElse("Hello world!");                     // <·· Alt 2: get the value or default
  
  optional.filter( s -> s.startsWith("RebelLabs"));    // <·· return empty Optional if not satisfied
  ```
- A much better alternative for values not known at compile time, but just
  at some **future time at runtime** is to initialize the variable with some
  arbitrary "future" value and provide for the possibility of a "future" value,
  a set of "error values" (timeouts, I/O errors) and final valid "values".
  This simplifies debugging in complex software, speed up execution and 
  protects against some sort of race conditions.

[[}]]

## <<AutoClosable>> (1.7+) [[{jvm.101.autoclosable,IO.101,JVM.GC,qa.101]]

- Reminder: The java garbage collector can not automatically clean any other 
  resource appart from memory (objects not referenced anywhere else in code).
   All resources related to I/O (virtual/physical devices) must be closed
  programatically, specially those that are related to remote machines like 
  sockets, http connections, database connections, ...  since neither the
  compiler, not the runtime can take control of such (non-controlled)
  devices/resources. Under the hood, closing such resource, usually means
  sending a network message and wait for an ACK response or a timeout.
- `<<java.lang.AutoClosable>>` simplifies resource cleaning.
  When a class representing an external resource implement this
  interface and is used inside a try-with-resources , its close
  method will be invoqued automatically (the compiler will add
  the required code).
- Most core java I/O classes already implement this interface.  Ex:
  ```
  public class MyClassWithExternalResources
  implements  java.lang.AutoCloseable , ... {
     private final MyExternalEventListener listener;
     private final MyIODevice device;
     private final MyHTTPConnection connection;
     ...
     public void close() {
         listener  .close();
         device    .close();
         connection.close();
     }
     ...
  }

  public class SomeLongRunningClass {
    ...
    void useManyResourcesManyTimes(String path)  {

      for (int repeat=0; repeat<100; repeat++) {
        try (MyClassWithExternalResources i =
              new MyClassWithExternalResources(...))  {
              ...
        } catch( ... ) {
          //
        }

        // At this point all resouces have been closed.
        // If a runtime exception exits the function the
        // resource is also closed.
      }
    }
  }
  ```
  [[jvm.101.autoclosable}]]

[[{java_lang.records,java_lang.data_structure,QA.101]]
## RECORDS!!! v15+

* Java classes have a bloated syntax when we just want to declare data-like 
  objects. Objects whose only purpose is to "transport" data in an structured
  way.  In that sense, even the "old" C from the 70's provided a much better
  support until the appearence of `record` in Java 15.  

* `record` provides a non-verbose inmutable way to declare inmutable classes.
  ```
  record Person (String name, int age) { } <·· Alt 1: short writing

  record Person (String name, int age) {   <·· Alt 2: Validating constructor
    Person {                                   ←  (optional) constructor: can NOT compute state,
      if (age < 0)                                                        only validate or throw
        throw new IllegalArgumentException("Too young");
    }
  }

  var john = new Person ("john", 76);
  ```

### `record` Basic Rules
- can NOT have any additional (internally computed) private or public instance 
  fields.
- can NOT extend classes.
- are ALWAYS FINAL (cannot be extended).

- **WARN:** Until the release of Hibernate 6, records could NOT be mapped to 
  "anything" in JPA/Hibernate. The could only be used as DTO projections in
  queries. In Hibernate 6 they CAN'T be used to model entity classes but can
  be used as embeddables.
- **TODO**: Can they be used in MyBatis?
[[}]]

[[{java_lang.101.annotations,qa]]
## Annotations 
@[http://docs.oracle.com/javase/tutorial/java/annotations/]

-  predefined annotation types in java.lang include:
  ```
  - @Deprecated
  - @Override
  - @SuppressWarnings
  - @SafeVarargs (SDK 1.? +) applied to a method/constructor,
                             asserts that the code does not perform
                             potentially unsafe operations
                             on its varargs parameter.
                             removing  related warnings
  ```

- Annotation types are a form of interface. They are declared like: 
  ```
  @Documented                                    
  @interface ClassPreamble {                     
     String   author        ()              ;    
     String   date          ()              ;    
     int      currentRev    () default 1    ;    
     String   lastModified  () default "N/A";    
     String   lastModifiedBy() default "N/A";    
     String[] reviewers     ()              ;    
  }                                              
  ```
  and ussed like:                                               
  ```
  @ClassPreamble (
     author         = "John Doe"      ,
     date           = "3/17/2002"     ,
     currentRev     = 6               ,
     lastModified   = "4/12/2004"     ,
     lastModifiedBy = "Jane Doe"      ,
     reviewers      = {"Alice", "Bob"}
  )
  public class Generation3List extends Generation2List {
      // ...
  }
  ```
### Annotation examples:
  ```
  new @Interned MyObject();              // <·· Class instance creation expression
  
  myString = (@NonNull String) str;      // <·· Type cast (1.8+)
  
  class UnmodifiableList<T> implements   // <·· implements clause
        @Readonly List<@Readonly T>
        { ... }
  
  void monitorTemperature() throws       // <·· throws exception declaration
  @Critical TemperatureException { ... }
  
  @SuppressWarnings(value = "unchecked") ← Predefined standard annotations
  void myMethod() { ... }
  @SuppressWarnings({"unchecked", "deprecation"})
  void myMethod() { ... }
  ```
[[}]]

## Meta-annotattons [[{java_lang.101,qa]]

- Meta-annotattons == Annotations applying to other annotations.

- Meta-annotattons `@Retention`:
  ```
  RetentionPolicy.SOURCE    <·· retained only in source (ignored by the compiler)
  RetentionPolicy.CLASS     <·· retained by compiler    (ignored by the JVM)
  RetentionPolicy.RUNTIME   <·· retained by JVM, can be queried at Runtime

  @Documented                       @Repeatable
  - indicates that whenever the     - (1.8+)
    specified annotation is used    - targeted annotation can be applied
    those elements should be          more than once to the same
    documented using the Javadoc      declaration or type use.
    tool. (By default, annotations    Ex:
    are not included in Javadoc.)     @Author(name = "Jane Doe")
                                      @Author(name = "John Smith")
                                      class MyClass { ... }
 
 
  @Target                            @Inherited
  (field,type,class..)              - targeted annotation type can be inherited
 - restrict targeted java-language    from the super class. (false by default.)
   elements where the annotation      When the user queries the annotation type
   can be applied:                    and the class has no annotation for this
   - ElementType.ANNOTATION_TYPE      type, the class'superclass is queried for
   - ElementType.CONSTRUCTOR          the annotation type.
   - ElementType.FIELD
   - ElementType.LOCAL_VARIABLE
   - ElementType.METHOD
   - ElementType.PACKAGE
   - ElementType.PARAMETER
   - ElementType.TYPE (1.8+)
  ```
[[}]]

[[{java_lang.101,java_lang.date_time,concurrency]]
## `java.time.*` (1.8+)
* JDK 1.8+, "deprecates" `java.util.(Date|Calendar|TimeZome)`
  New classes are IMMUTABLE and THREAD-SAFE.

### Ussage:
  ```
  import java.time.Instant;
  import java.time.ZonedDateTime;
  import java.time.ZoneId;
  import java.util.concurrent.TimeUnit;
  ...
  Instant   timestamp  =   Instant .now();              // Create from system clock
            timestamp .plus(Duration.ofSeconds(10));    // Add 10 seconds
  ```
* Instant <··> String:
  ```
  │  Instant  to String                 │   Instant  from String
  │(format with time-zone)              │ (parse string)
  ├─────────────────────────────────────┼───────────────────────────────────────────────────
  │  ZonedDateTime  zdt1 =              │
  │       ZonedDateTime .of             │ String sExpiresAt="2013-05-30T23:38:23.085Z";
  │       (                             │   ZonedDateTime zdt2 =   ZonedDateTime .parse(sExpiresAt);
  │         2017, 6, 30           ,     │
  │         1, 2, 3               ,     │   Instant i1 =   Instant .from(zdt1),
  │         (int) TimeUnit.             │           i2 =   Instant .from(zdt2);
  │               MILLISECONDS.         │
  │               toNanos(100),         │
  │         ZoneId.of("Europe/Paris")   │
  │       );          ^^^               │
  │     Ex: "Z","-02:00","Asia/Tokyo",..│
  │String s1 = zdt1.toString();         │
          ^^
  2017-06-30T01:02:03.100+02:00[Europe/Madrid]

  java.text.DateFormat:
  date  (none)           DateFormat.getDateInstance(DateFormat.DEFAULT, getLocale())
        short            DateFormat.getDateInstance(DateFormat.SHORT, getLocale())
        medium           DateFormat.getDateInstance(DateFormat.DEFAULT, getLocale())
        long             DateFormat.getDateInstance(DateFormat.LONG, getLocale())
        full             DateFormat.getDateInstance(DateFormat.FULL, getLocale())
        SubformatPattern new SimpleDateFormat(subformatPattern, getLocale())
  
  time  (none)           DateFormat.getTimeInstance(DateFormat.DEFAULT, getLocale())
        short            DateFormat.getTimeInstance(DateFormat.SHORT, getLocale())
        medium           DateFormat.getTimeInstance(DateFormat.DEFAULT, getLocale())
        long             DateFormat.getTimeInstance(DateFormat.LONG, getLocale())
        full             DateFormat.getTimeInstance(DateFormat.FULL, getLocale())
        SubformatPattern new SimpleDateFormat(subformatPattern, getLocale())
  ```
### Compatibility with Java <=1.8
  
* (java.util.) Date, Calendar and TimeZone
  "buggy" classes/subclasses were used:
  * Calendar class was NOT type safe.
  * Mutable non-threadsafe classes.
  * Favored programming errors (unusual numbering of months,..)

* Next compatibility conversion methods were added in 1.8:
  ```
  Calendar.toInstant()
  GregorianCalendar.toZonedDateTime()
  GregorianCalendar.from(ZonedDateTime) (Using default local)
  Date.from(Instant)
  Date.toInstant()
  TimeZone.toZoneId()
  ```

* java.time. Package summary
  ```
                     Description
  -----------------------------------------------------------------------------------
  Clock              A clock providing access to the current instant, date and
                     time using a time-zone.
  -----------------------------------------------------------------------------------
  Duration           A time-based amount of time, such as '34.5 seconds'.
  -----------------------------------------------------------------------------------
  Instant            An instantaneous point on the time-line.
  -----------------------------------------------------------------------------------
  LocalDate          A date without a time-zone in the ISO-8601 calendar system,
                     such as 2007-12-03.
  -----------------------------------------------------------------------------------
  LocalDateTime      A date-time without a time-zone in the ISO-8601 calendar
                     system, such as 2007-12-03T10:15:30.
  -----------------------------------------------------------------------------------
  LocalTime          A time without a time-zone in the ISO-8601 calendar system,
                     such as 10:15:30.
  -----------------------------------------------------------------------------------
  MonthDay           A month-day in the ISO-8601 calendar system, such as --12-03.
  -----------------------------------------------------------------------------------
  OffsetDateTime     A date-time with an offset from UTC/Greenwich in the ISO-8601
                     calendar system, such as 2007-12-03T10:15:30+01:00.
  -----------------------------------------------------------------------------------
  OffsetTime         A time with an offset from UTC/Greenwich in the ISO-8601
                     calendar system, such as 10:15:30+01:00.
  -----------------------------------------------------------------------------------
  Period             A date-based amount of time in the ISO-8601 calendar system,
                      such as '2 years, 3 months and 4 days'.
  -----------------------------------------------------------------------------------
  Year               A year in the ISO-8601 calendar system, such as 2007.
  -----------------------------------------------------------------------------------
  YearMonth          A year-month in the ISO-8601 calendar system, such as 2007-12
  -----------------------------------------------------------------------------------
  ZonedDateTime      A date-time with a time-zone in the ISO-8601 calendar system,
                     such as 2007-12-03T10:15:30+01:00 Europe/Paris.
  -----------------------------------------------------------------------------------
  ZoneId             A time-zone ID, such as Europe/Paris.
  -----------------------------------------------------------------------------------
  ZoneOffset         A time-zone offset from Greenwich/UTC, such as +02:00.
  -----------------------------------------------------------------------------------
  ```

  ```
  Enum               Description
  -----------------------------------------------------------------------------------
  DayOfWeek          A day-of-week, such as 'Tuesday'.
  -----------------------------------------------------------------------------------
  Month              A month-of-year, such as 'July'.
  -----------------------------------------------------------------------------------
  
  Exception          Description
  -----------------------------------------------------------------------------------
  DateTimeException  Exception used to indicate a problem while calculating a date-time.
  ```

* Note: Java 9 [REF](https://www.infoq.com/news/2017/02/java9-cldr-ldml)
> A number of parsing and formatting changes have been incorporated in Java 9 to
> bring the functionality closer to Unicode Locale Data Markup Language (LDML).
> providing better interoperability with non-Java systems.
> ... supervised by Stephen Colebourne (JodaTime), precursor of java.time, 
> LDML is used by the Unicode Common Locale Data Repository (CLDR) project,
> (Unicode Consortium) to gather and store locale data from different parts of
> the world ... Among others, LDML deals with date formatting and parsing.
* New features coming in Java 9 that bring java.time: 
  * DateTimeFormatter: letter ‘g’, as specified in LDML, indicates a 
    “Modified Julian day” ... different from a normal Julian day in the sense
    that:  
    a) it depends on local time, rather than GMT,
    b) it demarcates days at midnight, as opposed to noon.
  * Add date-time patterns 'v' and 'vvvv’: LDML formats to indicate 
    “generic non-location format”, e.g. “Pacific Time”, as opposed to
    the “generic location format” with specifies a city, like “Los Angeles Time”.
  * DateTimeFormatter pattern letters ‘A’, ’n’, ’N’: although LDML
    doesn’t specify formats ’n’ and ’N’, it does specify ‘A’, but the current
    behaviour in Java doesn’t match that of the spec. ‘A’ is meant to represent the
    total number of milliseconds elapsed in the day, with variable width, but
    currently Java treats this as fixed with: if ‘AA’ is specified as a pattern, it
    will fail to parse any value that is further than 99 milliseconds in the day.
    ’n’ and ’N’ are just Java extensions to the standard to represent nanoseconds
    within the second, and nanoseconds within the day, respectively.
  * JDK-8079628, java.time DateTimeFormatter containing "DD" fails on three-digit
    day-of-year value: similar to the previous problem, but with ‘D’ representing
    days within a year. If one specifies “DD” as a pattern, it will fail to parse
    “123” as the 123th day of the year.

### TimeUnit (JDK 1.5+)
@[https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/TimeUnit.html]
* Represents time durations at a given unit of granularity and
  provides utility methods to convert across units, and to perform
  timing and delay operations in these units.
* Enums: 
  ```
  DAYS HOURS MICROSECONDS MILLISECONDS MINUTES NANOSECONDS SECONDS

  void       sleep(long timeout)
  void   timedJoin(Thread thread, long timeout)
  void   timedWait(Object obj, long timeout)
  ...
  ```
[[}]]

[[java_lang.101}]]


[[{PM.WHATS_NEW,02_doc_has.ext_resource,async/reactive,java_lang.debugging]]
## What's new
*## v.17 2021-09-14 <!-- { -->
* 1st long-term support (LTS) release after JDK 11 (2018).
* 14 JEPs includes:
  * 409: Sealed Classes
         ```
         package com.example.geometry;

         public abstract sealed class Shape           ← Syntax 1: Permitted classes in
             permits com.example.polar.Circle,                    different file
                     com.example.quad.Rectangle,
                     com.example.quad.simple.Square { ... }
         
         abstract sealed class Root { ...             ← Syntax 2: Permitted classed inside
             final class A extends Root { ... }                   parent class.
             final class B extends Root { ... }
             final class C extends Root { ... }
         }
         ```
* 412: Foreign Function& Memory API (Incubator):
  API to efficiently interoperate with native code through foreign
  functions while safely accessing foreign memory (not managed by 
  the JVM). Future replacement for the brittleness and danger of JNI.
  Easy of use and able to work with different kinds of foreign memory
  (native memory, persistent memory, managed heap memory,..).
* 306: Restore Always-Strict Floating-Point Semantics for
  numerically-sensitive libraries, including `java.lang.Math` and
  `java.lang.StrictMath`. 
* 356: Enhanced Pseudo-Random Number Generators [[{security.cryptography,PM.TODO}]]
* 403: **Strongly Encapsulate JDK Internals**. it will no longer be
        possible to bypass strong encapsulation via --illegal-access flag.
* 406: Pattern Matching for switch (Preview)
* 407: Remove RMI Activation
* 410: Remove the Experimental AOT and JIT Compiler
* 411: Deprecate the Security Manager for Removal
* 414: Vector API (Second Incubator)
* 415: Context-Specific Deserialization Filters
* 382: New macOS Rendering Pipeline
* 391: macOS/AArch64 Port
* 398: Deprecate the Applet API for Removal
<!-- } -->

### v.15 2020-09-15 <!-- { -->
@[https://www.itprotoday.com/programming-languages/java-15-gains-garbage-collection-text-block-features]

* **RECORDS!!!**: non-verbose inmutable classes.  [[{java_lang.records}]]
* production ready ZGC low-latency garbage collector, expected to be
  quite impactful for a multitude of workloads.
* text blocks (JEP 378): strings spanning several lines ("templates", ...)
* JEP 360: Sealed Classes (Preview): Avoid extending class designed to NOT 
  be extended by third parties. 
* JEP 383: Foreign (C/Assembler) Memory Access API (Preview) (Project Panama).
<!-- } -->

### v.14 2020-03-??  <!-- { -->
* [REF@infoq.com](https://www.infoq.com/news/2019/12/java14-feature-freeze/)
  * More container awareness...  
  * NUMA container support added to hotspot (JDK-8198715)
  * Add Container MBean to JMX (JDK-8199944)
* Record-types (Preview)
* Shenandoah GC: 
  [REF1@redhat.com](https://developers.redhat.com/blog/2020/03/04/shenandoah-gc-in-jdk-14-part-1-self-fixing-barriers/)
  """ ... self-fixing barriers aims to reduce local latencies
  spent in barrier mid-and slow paths. The second will cover
  concurrent root processing and concurrent class unloading. """  
  [REFw@redhat.com](https://developers.redhat.com/blog/2020/03/09/shenandoah-gc-in-jdk-14-part-2-concurrent-roots-and-class-unloading/)
  """... concurrent roots processing and concurrent class unloading,
         aim to reduce GC pause time by moving GC work from the pause
         to a concurrent phase."""
<!-- } -->

### v 12,13  <!-- { -->
* REFs:
  * @[https://www.infoq.com/news/2019/06/java13-feature-freeze/]
  * @[https://developers.redhat.com/blog/2019/06/27/shenandoah-gc-in-jdk-13-part-1-load-reference-barriers/]
  * @[https://developers.redhat.com/blog/2019/06/28/shenandoah-gc-in-jdk-13-part-2-eliminating-the-forward-pointer-word/]
  * @[https://developers.redhat.com/blog/2019/07/01/shenandoah-gc-in-jdk-13-part-3-architectures-and-operating-systems/]
* add container support to jhsdb command (JDK-8205992)
* Impove systemd slice memory limit suppot (JDK-8217338)
* More container awareness:  [[{devops.containarization]]<br/>
  * Flight Recorder impovements for containers (JDK-8203359)
  * Improve container support when Join Contollers option is used (JDK-8217766)
  * JFR jdk.CPUInformation event epots incorrect info. when running in
    Docker container (JDK-8219999) [[}]]
<!-- } -->

### v.11(LTS) 2018/09 <!-- { -->
* REFs:
  * @[https://www.journaldev.com/24601/java-11-features]
  * @[https://www.infoq.com/news/2018/09/java11-released]
* More container awareness...                                             [[{devops.containarization]]
  * Remove -XX:+UnlockExperimentalVMOptions, -XX:+UseGroupMemoryLimitFoHeap (JDK-8194086)
  * jcmd -l & jps: do not list JVMs in Docker containers (JDK-8193710)
  * Container Metrics (-XshowSettings:system) (JDK-8204107)
  * Update CPU count algorithm when both cpu shares and quotas are used (JDK-8197867)
    --XX:+PrefectContainerQuotaForCPUCount                              [[}]]

#### New major features:
* Autocompilation(JEP 330)
  ```
  $ java someFile.java
  ```
* New string methods.
* Local-Variable Syntax for Lambda Parameters (JEP 323)
  ```
  (var s1, var s2) -> s1 + s2
  ```
  (While it's possible to  just skip the type in the lambda
   it becomes a need when for annotations like @Nullable )
* Nested Based Access Control: fix some issues when using (**discouraged**) reflection.
* Dynamic Class-File Constants(JEP 309):<br/>
  `class-file` format now extends support a new constant pool form:  
  CONSTANT_Dynamic , reduce the cost and disruption of developing
  new forms of materializable class-file constraints.

* Epsilon: A No-Op Garbage Collector(JEP 318): [[{performance,arch.batch]]<br/>
  (Experimental)
  Unlike the JVM GC which is responsible for allocating memory
  and releasing it, Epsilon only allocates memory. Useful for:
  * Extremely short lived jobs
  * Performance testing
  * Memory pressure testing
  * VM interface testing
  * Last-drop latency improvements
  * Last-drop throughput improvements        [[}]]

* Remove the JavaEE and CORBA Modules(JEP 320)

* HTTP/1.1, HTTP/2 and WebSockets Client (JEP 321)  [[{io.http]]
  designed to improve overall performance.          [[}]]

* TLS 1.3                                           [[{SECURITY.CRYPTOGRAPHY]]
* ChaCha20,Poly1305 Crypto (JEP 329) implemented in
  the SunJCE provider.
                                                    [[}]]

* Convenient Reading/Writing Strings to/from Files  [[{PM.TODO}]]

* ZGC: (JEP 333) Experimental (Production in 15+)
  - Scalable Low-Latency Garbage Collector.
  - low latency GC.
  - sub-10ms pause times, less than 15% perf.penalty.
<!-- } -->

### v.10 (2018/03)  <!-- { -->

#### More container awareness...                                 [[{devops.containarization]]
* Improve heap memory allocations (JDK-8196595):
  ```
  --XX:InitialRAMPercentage, --X:MaxRAMPercentage and -XX:MinRAMPercentage
  --XX:InitialRAMFraction  , --X:MaxRAMFraction   and -XX:MinRAMFraction   deprecated
  ```
* Total number of CPUs available to the Java Process calculated from
  ```
  --cpus, --cpu-shares, --cpu-quota (JDK-8146115)
  ```
  (Use --XX:-UseContainerSupport to return to the old behaviour)
* Attach in linux became elative to `/proc/pid/root` and namespace aware (jcmd, jstack,...)
* See [REF@aboullaite.me](https://aboullaite.me/docker-java-10/)
  JVMs before 10 had been implemented before cgroups, hence not
  optimized for executing inside a container.
 [[}]]

* Application Data-Class Sharing extends                    [[{devops,performance]]
  Class-Data Sharing ("CDS") allowing application classes
  to be placed in the shared archive in order to improve
  startup and footprint.                                    [[}]]
* Parallel Full GC for G1: improves G1 worst-case latencies  [[{JVM.GC}]]
* Consolidate JDK Forest into a Single Repository  !!!!
* Local-Variable Type Inference: var a = ....;
* javah superseded by functionality in javac.
* Thread-Local Handshakes allows to execute a callback on   [[{JVM,performance]]
  threads without performing a global VM safepoint.
   Makes it both possible and cheap to stop individual
  threads and not just all threads or none. [[}]]
* default set of Root Certificates CA in the JDK.
* Heap Allocation on Alternative Memory Devices
  enables the HotSpot VM to allocate the Java object
  heap on an alternative memory device, such as an
  NV-DIMM, specified by the user.
<!-- } -->

### v.9(2017/09)  <!-- { -->
* Containerization work [[{devops.containarization]]
  ```
  └ --XX:ParallerGCThreads and --XX:CICompilerCount are set based on
    Containers CPU limits (can be overriden)
    - Calculated from --cpuset-cpus

  └ Memory Configuration for containers
    -XX:+UnlockExperimentalVMOptions
    -XX:+UseGroupMemoryLimitFoHeap
    - set -XX:MaxRAMFraction to 2 (default is 4)   [[}]]
  ```

#### **Java Platform Module System** [[{]]
* divides the JDK into a set of modules for combining at run, compile, or build time.
* enabling understanding of dependencies across modules.
* allows developers to more easily assemble and maintain sophisticated applications.
* allows to scale down to smaller devices.
* improves security and performance.
* aspects include:
  * application packaging
  * JDK modularization
  * reorganizing source code into modules.
  * Build system is enhanced to compile modules
    and enforce module boundaries at build time.
    (Java 9 allows illegal reflective access to help migration)
[[}]]



* ahead-of-time (AoT) compilation (experimental)                [[{performance]]
  improve startup time, with limited impact on peak performance.[[}]]
* **REPL (read-eval-print loop) jShell:**<br/>
  jShell: interactively evaluates statements "a la script" with
  tab completion, automatic terminal semicolons and API for IDE integration.

[[{java_lang.functional]]
* Reactive Streams<br/>
(https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.1/README.md#specification)
  Small spec, also adopted in Java 9, that defines the interaction
  between asynchronous components with back pressure. For example a
  data repository — acting as Publisher, can produce data that an
  HTTP server — acting as Subscriber, can then write to the
  response. The main purpose of Reactive Streams is to allow the
  subscriber to control how fast or how slow the publisher will produce
  data.
* Streams API Enhancements:<br/>
  * Java 8 Stream API allows processing data declaratively
    while leveraging multicore architectures.
  * Java 9 adds methods to conditionally take and drop items
    from Stream, iterate over Stream elements, and create a
    stream from a nullable value while **expanding the set of
    Java SE APIs that can serve as Streams sources**.
[[java_lang.functional}]]

* Code cache can be divided into segments to improve performance
  and allow extensions such as fine-grained locking resulting in improved sweep
  times.

* (Datagram Transport Layer Security) DTLS security API,     [[{security]]
  preventing eavesdropping, tampering, and message forgery
  in client/server communications.                           [[}]]

#### Java 9 deprecates and removes ....
* Applet API and appletviewer (alternative: Java Web Start)
* Concurrent Mark Sweep (CMS) GC.
* JVM TI (Tool Interface) hprof (Heap Profiling) agent, superseded in the JVM.
* jhat tool, obsoleted by superior heap visualizers and analyzers.
<!-- } -->

### Java 8 <!-- { -->

* First version to support Containers. **WARN**: Do not use any version below that.

* [[{PM.TODO]]
  @[https://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html]
  @[https://www.qwant.com/?q=what+new+java+8]
  [[}]]

* TLS enhancements backported to 1.8 (HTTP2)
  @[https://www.infoq.com/news/2020/04/tls-alpn-java8/]
<!-- } -->
[[PM.WHATS_NEW}]]

# JVM internals <!-- { JVM internals -->

[[{JVM,java_lang.profiling,architecture.real-time]]
## JVM Safepoints
* @[http://psy-lob-saw.blogspot.com/2015/12/safepoints.html]    [[{PM.TODO}]]

* Definition: Mutator threads: threads which manipulate the JVM heap.
  all Java Threads are mutators, Non*Java (native?) threads may also be
  regarded as mutators when they call into JVM APIs which interact with the heap.
* Safepoint:
  * range of execution where the state of the executing thread
    is well described since thread is NOT interacting with the heap:
  * used to put mutator threads on hold while the JVM 'fixes stuff up'
  * particularly useful to let JVM examine|change the heap (GC, ...)
    (no objects still alive and referenced from the stack)
* thread is at safepoints when:
  * thread de-scheduling events: thread blocked on lock/synch.lock, waiting on a monitor, parked,
    or blocked on blocking*IO.
  * thread is executing JNI code.
* thread is NOT at safepoints when:
  * executing bytecode (maybe, but not for sure).
  * thread interrupted (by the OS) while not at a safepoint.
* JVM cannot force any thread into a safepoint state but ...
  JVM can stop threads from leaving a safepoint state.  
  * Q: How then bring ALL threads into a safepoint?  
    A: Java threads poll a 'safepoint flag' (global or thread level) at 'reasonable' intervals
       and transition into a safepoint state (thread is blocked at a safepoint) when active.
    * Q: how to avoid waste time checking if C1/C2 (client/server) JIT compilers need to stop?
         how to keep safepoint polls to a minimum.  
      A: considerations combined lead to the following locations for safepoint polls:  
         ```
         - Between any 2 bytecodes while running in the interpreter (effectively)
         - On 'non-counted' loop back edge in C1/C2 compiled code
         - Method entry (Zing,...) or exit (OpenJDK,...) in C1/C2 compiled code.
         ```
  * Ex:
    ```
    public class WhenWillItExit {
      public static void main(String[] argc)
        throws InterruptedException {
        const UP = Integer.MAX_VALUE;
        final Thread t = new Thread(() -> {
          long l = 0;
          for (int i = 0; i < UP ; i++) {     ┐  Result:
            for (int j = 0; j < UP ; j++) {   │
              if ((j & 1) == 1) l++;          ├ long-type loops: 'uncounted' code.    safepoints injected at each loop.
            }                                 │  int-type loops:   'counted' code. No safepoints injected.
          }                                   ┘                  -   (Much) Better performance   BUT ...
          System.out.println("How Odd:" + l);                        other threads forced to suspend at their
        });                                                          next safepoint operation.
        t.setDaemon(true);                    ┐
        t.start();                            ├  Expected: exit in ~5 seconds.
        Thread.sleep(5000);                   ┘  Result  : no safepoints means threads, JMX connections, ...
      }                                                    will have to expect to daemon thread to exits to
    }                                                      be able to reach a global safepoint. Use -Xint to
                                                           disable C1/C2 compilation or replace int → long
                                                           in loop index to respect the 5 second behaviour.
    ```
    (See original source for many interesing details on safepoint tunning)

### SUMMARY
- Safepoint polls are dispersed at fairly arbitrary points and depending
  on execution mode, mostly at uncounted loop back edge or method return/entry.
- Bringing the JVM to   GLOBAL safepoint is high cost
- ☞ For real-time applications,  it's critical to know about safepoints
   avoiding 'counted' code.
-XX:+PrintApplicationStoppedTime will log contained safepoint pauses.

##  Problems with (most) Sampling Profilers
([REF]@[http://psy-lob-saw.blogspot.com/2016/02/why-most-sampling-java-profilers-are.html])
* A large number of samples needed to get statistically significant results.
* profiler should sample all points in a program run with equal probability
* Generic profilers rely on the JVMTI spec:
  * JVMTI offers   only safepoint sampling stack trace collection options :
    * only the safepoint polls in the running code are visible  skipping optimized
      (counted-code) for-loops!!!
    * samples are biased towards the next available safepoint poll location
    * A sample profiler can blame a "cheap method" 9 levels down the stack when the
      real culprit is the topmost method loop.
[[}]]

## Inside the JVM [[{jvm.101,PM.WiP]]
* @[https://www.artima.com/insidejvm/ed2/index.html]

* JVM anatomy Park
  * @[https://shipilev.net/jvm-anatomy-park/]
  * @[https://shipilev.net/jvm-anatomy-park/22-safepoint-polls/]

    ```
      ┌────────────────────┐
      │ JVM StartUp thread │
      └─────────┬──────────┘
       ┌────────┼───────────┐
       v        v           v
       GC      Compiler   JAVA
     Threads   Thread    Threads
     ┌┐┌┐┌┐┌┐    ┌┐      ┌┐┌┐┌┐┌┐┌┐┌┐┌┐...
     ││││││││    ││      ││││││││││││││
     ││││││││    ││      ││││││││││││││
     ││││││││    ││      ││││││││││││││
     ││││││││    ││      ││││││││││││││
     ││││││││    ││      ││││││││││││││
     ········    ··      ··············
    ```
  * JIT compiler optimization levels:
    ```
    - cold
    - warm
    - hot
    - very hot (with profiling)
    - scorching.
      ^^^^^^^^
    The hotter the optimization level, the better the
    expected performance, but the higher the cost in terms of
    CPU and memory.  See also @[#jvm_app_checkpoint]
    ```
[[}]]

[[{jvm.101,scalability.jvm,monitoring.jvm]]
## JVM Memory 
* REF: @[https://dzone.com/articles/java-memory-management]
* JAVA MEMORY MODEL:
  ```
           │    STACK ("SMALL")          │ HEAP  ("HUGE")
           │ private to each Thread      │ Shared by Threads
  ─────────┼─────────────────────────────┼──────────────────
  Contain  │ - references to heap objects│ - objects
           │ - value types               │ - instance fields
           │ - formal method params      │ - static fields
           │ - exception handler params  │ - array elements

  * 1: Ref(erence) types on the stack point to real object in HEAP memory.
  ```
* Reference Types regarding how the object on the heap is eligible for garbage collection
  ```
  ┌─────────┬───────────────────────────────────────────────────────────────────────────
  │ STRONG  │ - Most popular.
  │         │ - The object on the heap it is not garbage collected
  │         │   while there is a strong reference pointing to it, or if it is
  │         │   strongly reachable through a chain of strong references.
  ├─────────┼───────────────────────────────────────────────────────────────────────────
  │ WEAK    │ - most likely to not survive after the next garbage collection process.
  │         │ - Is created like
  │         │    WeakReference<StringBuilder> reference =
  │         │     = new WeakReference<>(new StringBuilder());
  │         │ -  Ex.use case: caching:
  │         │   We let the GC remove the object pointed to by the weak reference,
  │         │   after which a null will be returned
  │         │   See JDK implementation at
  │         │   @[https://docs.oracle.com/javase/7/docs/api/java/util/WeakHashMap.html]
  ├─────────┼───────────────────────────────────────────────────────────────────────────
  │ SOFT    │ - used for more memory-sensitive scenarios
  │         │ - Will be garbage collected only when the application is running low on memory.
  │         │ -  Java guarantees that all soft referenced objects
  │         │   are cleaned up before throwing OutOfMemoryError
  │         │ - is created as follows:
  │         │   SoftReference<StringBuilder> reference = new SoftReference<>(new StringBuilder());
  ├─────────┼───────────────────────────────────────────────────────────────────────────
  │ PHANTOM │ - Used to schedule post-mortem cleanup actions, since we know for
  │         │   sure that objects are no longer alive.
  │         │ - Used only with a reference queue, since the .get() method of
  │         │   such references will always return null.
  │         │ -  These types of references are considered preferable to finalizers
  └─────────┴───────────────────────────────────────────────────────────────────────────
  ```
[[}]]

[[{jvm.memory,scalability.cache,scalability.jvm]]
## Mark&Sweep GC:
* JVM analyzes the variables from the stack and "marks" all the objects that need to be kept alive.
  Then, all the unused objects are cleaned up.
* The more garbage there is, and the fewer that objects are marked alive, the faster the process is.
* To optimize even more heap memory actually consists of multiple parts (Java 8+):
  ```
  ┌──────────┬────────────────────────────────────────────────────
  │ HEAP     │
  │ SPACES   │
  ├──────────┼────────────────────────────────────────────────────
  │ Eden     │ * object are place here upon creation.
  │          │ * "small" ··> gets full quite fast.
  │          │ * GC runs on the Eden space and marks objects as alive
  ├──────────┼────────────────────────────────────────────────────
  │ S0       │ * Eden Objects surviving 1st GC are moved here
  │          │
  ├──────────┼────────────────────────────────────────────────────
  │ S1       │ * Eden Objects surviving 2nd GC are moved here
  │          │ * S0   Objects surviving     GC are moved here
  ├──────────┼────────────────────────────────────────────────────
  │ Old      │ * Object survives for "N" rounds of GC (N depends on
  │          │   implementation), most likely that it will survive
  │          │   forever, and get moved here
  │          │ * Bigger than Eden and S0,S1. GC doesn`t run so often
  ├──────────┼────────────────────────────────────────────────────
  │ Metaspace│ * metadata about loaded classes
  │          │   (PermGen Before Java 8)
  ├──────────┼────────────────────────────────────────────────────
  │ String   │
  │   pool   │
  └──────────┴────────────────────────────────────────────────────
  ```
[[}]]

## GC Types [[{jvm.memory]]
* default GC type is based on the underlying hardware
* programmer can choose which one should be used
* ```
     GC TYPE     | Description  / Use-Cases
  +--------------+-----------------------------------------------------------------------------
  |Serial GC     | · Single thread collector.
  |              | ·  Halt all app threads while executing
  |              | · Mostly applies to  small apps with small data usage
  |              | · Can be enabled through :   -XX:+UseSerialGC
  +--------------+-----------------------------------------------------------------------------
  |Parallel GC   | · Multiple threads used for GC
  |              | ·  Halt all app threads while executing
  |              | · Also known as throughput collector
  |              | · Can be enabled through :   -XX:+UseParallelGC
  +--------------+-----------------------------------------------------------------------------
  |Mostly        | · works concurrent to the application, "mostly" not halting threads
  |Concurrent GC | · "mostly": There is a period of time for which the threads are paused.
  |              |    Still, the pause is kept as short as possible to achieve the best GC performance.
  |              | · 2 types of mostly concurrent GCs:
  |              |   * Garbage First · high throughput with a reasonable application pause time.
  |              |                   · Enabled with the option:   ·XX:+UseG1GC
  |              |     Concurrent Mark Sweep: app pause is kept to minimum.  Deprecated as Java9+*
  |              |                   · Enabled with the option:   ·XX:+UseConcMarkSweepGC
  +--------------+-----------------------------------------------------------------------------
  ```
* [See also](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/sizing.html)
[[}]]

## sysctl/ulimit+JAVA [[{performance.jvm.linux]]

Reference (non-mandatory) Linux OS setup for JVM server tasks extracted from:
@[https://docs.sonarqube.org/latest/requirements/requirements/].

* ```
  $ sysctl vm.max_map_count     <·· Ensure it's greater than or equal to 524288
  $ sysctl fs.file-max          <·· Ensure it's greater than or equal to 131072
  $ ulimit -n                   <·· Ensure at least 131072 file descriptors
  $ ulimit -u                   <·· Ensure at least 8192 threads
  ```
* To Modify Kernel limits (permanently) add next lines to `/etc/sysctl.conf`
  (or `/etc/sysctl.d/99-sonarqube.conf`)
  ```
  + sysctl -w vm.max_map_count=524288
  + sysctl -w fs.file-max=131072            
  ```
* To modify temporarelly (changes lost at system restart)
  ```
  $ ulimit -n 131072  
  $ ulimit -u 8192
  ```
* To modify user limits permanently Add next lines to:
  * Alt 1:  /etc/security/limits.conf  (non SystemD)
  ```
  + sonarqube   -   nofile   131072       ← 
  + sonarqube   -   nproc    8192
  ```
  * Alt 2: SystemD unit definition (SystemD)
  ```
    [Service]
    ...
  + LimitNOFILE=131072                      .
  + LimitNPROC=8192
    ...
  ``` 
[[}]]
<!-- JVM internals } -->

[[{monitoring.logging]]
# JAVA Logging 

## Logging Ecosystem Overview <!-- { -->
* Logging Frameworks offer different features, but they all offer the same 
  global structure 
  * LogRecords "data objects" with Log levels.
  * Loggers: 
  * Handlers (also called Appenders).
  * Formatters (Layouts).
  ```
  app ·(LogRecord)·> Logger ··> Handler ··> Formatter ··> Output
  ```

* Common Logging Frameworks include:
  * java.utils.logging : Offers a basic logging API. It used a java properties
    file for configuration. Supports txt and XML formats by default.
  * Log4j(Apache): solid performance, extremely configurable.
    In big projects, consider using it on top of SLF4J with Log4j binding.
    Supports text, XML, JSON and YAML output. 
    * At startup, Log4j searches for a log4j2.$format file. If not found, 
      it will default to console output.
  * Logback: Designed as successor to Log4jv1 before Log4jv2 was released.
  * tinylog:
  * SLF4J(QOS.ch) and Commons Login: Abstraction layers around other frameworks
    allowing to change logging frameworks on demand at runtime based on
    frameworks available on the application's classpath.

* Compared:
  * Log4j and Logback have native support for Syslog.
  * Log4j has an SMTPAppender to send mail.
  * Log4j has a FailoverAppender, that automatically switches to a
    different Appender in case one or more fails.
  * java.util.logging provides two Layouts/Formatters: 
    SimpleFormatter (def for console) and XMLFormatter (def. for files)
  * Log4jv2 looks to be faster than Logback and more stable.
    according to [performance comparative](https://www.loggly.com/blog/benchmarking-java-logging-frameworks/)
    **WARN**: logback dropped arount 36% of logs in Async mode (vs 0% for Log4jv2).


* Some stacks are tied to a given framework. (java.util.logging for Tomcat)


     * Config through logback.xml file. (XML config syntax similar to Log4j).
<!-- } -->

## `java.util.logging.*`  <!-- java.util.logging { -->
https://docs.oracle.com/javase/10/core/java-logging-overview.htm#JSCOR-GUID-B83B652C-17EA-48D9-93D2-563AE1FF8EDA

* core package includes support for delivering text or XML log records
  to memory, output streams, consoles, files, and sockets.
* The core API is are capable of interacting with logging services 
  on the host operating system.

* Loggin Control Flow:
  ```
  |App| ··> Creates LogRecord
    ·               ---------
    ·               1 log level object: "rough guide to the importance and urgency of a log message"
    ·                 Log Level objects encapsulate an integer value: higher value => higher priority
    ·                 Level.FINEST (lowest priority/value) ... Level.SEVERE (highest priority/value).
   logger.log    (Level.WARNING, String srcClass, String srcMethod, String msg) ²
   logger.warning(Level.WARNING, String srcClass, String srcMethod, String msg)
   logger.log    (                                                  String msg) ¹
    ·            
    ·            ¹: Logging framework will make a "best effort" to determine 
    ·               which class and method called into the logging framework
    ·            ²: In other frameworks logp() (log precise) is used to set  source class/method
    ·
 (calls to) <·· calls are "cheap" when logging is disabled for a given log level.
    ·           Logger test and return "fast" with no ops.
    ·                                  ┌─ Also called Appender in other frameworks.
    v                                  v
 │Logger  │ ·········>(LogRecord)····>│Handler│ ··(publish to) ··>│Outside World│
                                                                   (│Handler│, I/O stream,
  ^^^^^^^^                             ^^^^^^^                      console, file, socket,...)
  0+ LogRecords                        
  0+ Filters                           0+ Filters         <·· Filters and Logging Level objects decide 
  1+ Logging Level                     0+ Logging Level       if they are interested in a particular 
     (Log request below level                                 LogRecord object.
      are discarded)                   0+ Formatters      <·· localize and format message before publishing 
  1 "dot-separated" name                  └────────┴─ == "Layouts" in other Log frameworks.
    in a hierarchical namespace,       
    managed by │LogManager│,           LogManager <·· Singleton object. 
    typically aligned with package     ----------     (Retrieve it with LogManager.getLogManager)
    namespace, but not required to.    1 hierarchical namespace of named Loggers
  1+ Handler object                    1 control properties (read from configuration file)
  1 Reference to parent Logger                                         └───────┬────────┘
    (except for root "" logger)        ┌───────────────────────────────────────┘
    By default, loggers inherit next   java.util.Properties file including config for:
    attributes from their parent:      - root-level handlers to install during at startup.
    · Minimum Logging level. Ex:       - levels for particular named loggers. 
      logger.setLevel(Level.WARNING);  - arbitrary properties for use by any handler.
    · Handlers from parent             - Ej:
    · Resource bundle names              handlers=java.util.logging.ConsoleHandler, java.util.logging.FileHandler
    · Resource bundle names              java.util.logging.FileHandler.pattern = %h/java%u.log 
                                         java.util.logging.FileHandler.limit = 50000
                                         java.util.logging.FileHandler.count = 1
                                         java.util.logging.FileHandler.formatter = java.util.logging.XmlFormatter
                                         java.util.logging.MemoryHandler.size=... # <·· default size for ring buffer
                                         └─────────────┬───────────────┘
                                         Convention, handler props. start with handler class name
                                       
  Default Java SE Handlers                                      Default Java SE Formatters:
  ------------------------                                      ---------------------------
  StreamHandler : writes formatted-record to OutputStream       SimpleFormatter: Writes brief "human-readable" summaries 
  ConsoleHandler: writes formatted-record to System.err                          of log records
     FileHandler: writes formatted-log records to:                 XMLFormatter: Writes detailed XML-structured record. 
                  - alt 1: single file                          (Creating new formatters is straightforward)
                  - alt 1: set of rotating log files
   SocketHandler: writes formatted-log records to TCP ports
   MemoryHandler: buffers log records in memory
   (Writting new handlers is straightforward)
  ```

* By default all Logger objects also send their output to their parent Logger.
  but can be set to ignore Handler objects higher up the tree.
* Handlers may direct output to other Handler objects. Ex:
  ```
  MemoryHandler (SDK) ·······> (publish to) ··> Target Handler
  -------------                 @trigger-events
  1 ring buffer<LogRecords> 
  0+filters
  0 formatters   <·· no format applied to intermediate handlers
  ```
* In addition to named Logger objects, it is also possible to create anonymous Logger 
  objects that do NOT appear in the shared namespace. (See the Security section).
  Anonymous loggers have root as their parent.
  
### Examples

* logging with default configuration:
  ```
  /* code relies on root handlers established by
   * LogManager based on configuration file. */

  package com.wombat;
  import java.util.logging.*;
  
  public class Nose {
      private static Logger logger =             
         Logger.getLogger("com.wombat.nose");

      public static void main(String argv[]) {
          logger.fine("doing stuff");           // Obtain a suitable logger.
          try ( stream = ...) {
            ...  
          } catch(IOException e1) {
            logger.log(Level.WARNING, "...", e1);
            throw 
          }
          logger.fine("done");
      }
  }
  ```
* Ex: Change config to send output to file.
  ```
  public static void main(String[] args) {
      Handler fh = new FileHandler("%t/wombat.log"); // %t == system temporary directory
      Logger.getLogger(""/*root*/  ).addHandler(fh);
      Logger.getLogger("com.wombat")
        .setLevel(Level.WARNING); // Ignore all Logs below WARNING level
                  └───────────┴─··· := FINEST < FINER < FINE < CONFIG < INFO < WARNING < SEVERE 
                                       ALL: log all messages regardless of level
                                       OFF: disable logging.
      ...
  }
  ```
* use custom Handler ignoring Global Configuration
  ```
  package com.wombat;
  import java.util.logging.*;
  
  public class Nose {
    private static Logger logger = Logger.getLogger("com.wombat.nose");
    private static FileHandler fh = new FileHandler("mylog.txt");
    public static void main(String argv[]) {
        logger.addHandler(fh);              // Send logger output to our FileHandler.
        logger.setLevel(Level.ALL);         // Request that every detail gets logged.
        logger.info("doing stuff");         // Log a simple INFO message.
        try {
            ...
        } catch (Exception e1) {
            logger.log(Level.WARNING, "...", e1);
        }
        logger.fine("done");
    }
  }
  ```
* Sample XML Output:
  ```
  <log>
    <record>
      <date>2015-02-27T09:35:44.885562Z</date>
      <millis>1425029744885</millis>
      <nanos>562000</nanos>
      <sequence>1256</sequence>
      <logger>kgh.test.fred</logger>
      <level>INFO</level>
      <class>kgh.test.XMLTest</class>
      <method>writeLog</method>
      <thread>10</thread>
      <message>Hello world!</message>
    </record>
  ```

* Format(Layout) config: 
  ```
  Handler ch = new ConsoleHandler();
  ch.setFormatter(new XMLFormatter()); // <·· Use XMLFormatter (txt by default in console)
  logger.addHandler(ch);
  ```
<!-- } -->

[[{monitoring.logging.slf4j]]
## SLF4j Logging  
* @[https://www.slf4j.org/]
* Simple Log Facade or abstraction for various logging frameworks
  (e.g. java.util.logging, logback, log4j) allowing the end user
  to plug in the desired logging framework at deployment time.


* maven configuration for slf4j + logback. <!-- { -->
  ```
  pom.xml
  <dependency>*
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.21</version>
  </dependency>
  ...
  <dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>            ← add   logback  facade
    <version>1.1.7</version>
    <exclusions>
      <exclusion>
        <groupId>org.slf4j</groupId>                    ←  Avoid error next start-up:
        <artifactId>slf4j-jdk14</artifactId>               "SLF4J: Class path contains multiple SLF4J bindings."
      </exclusion>                                         "   slf4j-jdk14-1.7.21.jar!...StaticLoggerBinder.class"
    </exclusions>                                          "logback-classic-1.1.7.jar!...StaticLoggerBinder.class"
  </dependency>
<!-- } -->

* Example Ussage
  ```
  import org.slf4j.Logger;
  import org.slf4j.LoggerFactory;
  ...
    class MyClass {
      private static final Logger log =
         LoggerFactory.getLogger(AbstractRequestHandler.class);
      ...
      if (log.isDebugEnabled()) {  ←······················· composed string "Lorem ipsum..."is built
          log.debug("Lorem ipsum... @{} {}",  ←············ before calling log.debug.
             "param1", "param2");                           "wrapper" if(log.isDebugEnabled())  avoid
      }                                                     unnecesary string processing (saving can
      ...                                                   be "huge" if log.debug is inside a loop.
    }                                                     unnecesary string processing (saving can
  ```
[[monitoring.logging.slf4j}]]

[[{monitoring.logging.log4j,monitoring.logging.logback]]
## Log4j2/Logback config 
 ```
 <xml version="1.0" encoding="UTF-8"?>
 <Configuration status="warn" name="MyApp">

                                                       
   <Appenders> ¹
     <Console name="MyAppender1" target="SYSTEM_OUT">  <·· Console is the default in most frameworks
       <PatternLayout pattern="%m%n"/>
     </Console>
     <File name="MyFileAppender2"
        fileName="myLog.log" append="true" locking="true">
       <PatternLayout pattern="%m%n"/>
     </File>
   </Appenders>

   <Loggers>   ²
     <Root level="error"> ³                            <·· Only root logger is defined in the example.
       <AppenderRef ref="MyAppender1"/>                    which accepts all messages by default, and 
     </Root>                                               logs only about ERROR level.
   </Loggers>
 </Configuration>
 ¹ STEP 1) Configure Appender/Handlers    
 ² STEP 2) Configure Loggers
 ³ 
 ```

### LogBack Config Example <!-- { -->
 ```
 <configuration>
   <appender name="MyAppender"
     class="ch.qos.Logback.core.ConsoleAppender">
     <encoder>
       <pattern>%m%n</pattern>
     </encoder>
   </appender>

   <appender name="FileAppender"
     class="ch.qos.Logback.core.FileAppender">
     <file>myLog.log</file>
     <append>true</append>
     <prudent>true</prudent>                   <·· true => increases cost of writing but safely manages 
                                                   file writes from multiple FileAppenders and/or Java programs
     <encoder>
       <pattern>%m%n</pattern>
     </encoder>
   </appender>

   
   <root level="error">
     <appender-ref ref="MyAppender" />
   </root>
  </configuration>
 ```
<!-- } -->

### Format/Layout config in Log4j/Logback <!-- { -->
* one of the most common Layouts in Log4j and Logback is the PatternLayout.
  ```
  <PatternLayout 
    pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
                                                              └┴─ append new line
                                                           └─┴─·· message
                                             └────┴─············· logger name (up to 36 chars width)
                                     └────┴─····················· severity of the log event.
                               └─┴─······························ current thread
             └──────────────┴─··································· timestamp format
  Example output:
  16:42:14.271 [main] INFO main: initializing worker threads
  16:42:15.990 [worker] DEBUG worker: listening on port 12222
  16:42:20.010 [worker] INFO worker: received request from 192.168.1.200
  16:42:20.100 [worker] ERROR worker: unknown request ID from 192.168.1.200
  ```
<!-- } -->

### Logging Stack Traces With PatternLayout

* Supported by Log4j/Logback:
  ```
  [%p] %t: %m<b>%xEx</b>
                └──┴─·········  full stacktrace with packaging information for each call in the stack
                                [ERROR] main: ... java.io.FileNotFoundException: foo.file 
                                   at java.io.FileInputStream.open(Native Method) ~[?:1.7.0_79]
                                   ...
                                   at FooClass.main(FooClass.java:47)
  [%p] %t: %m<b>%ex</b>
                └─┴─ ·········· request only the stack trace for exceptions.
  [%p] %t: %m<b>%xEx{none}</b>  <·· exclude exception info. altogether (Log4j   only)
  [%p] %t: %m<b>%nopex</b>      <·· exclude exception info. altogether (logback only)
  ```

[[{monitoring.logging.NDC,monitoring.logging.MDC,monitoring.101,qa.best_patterns,devops]]
### ThreadContext (Log4j2 "upgrade" to NDC and MDC)
* built on earlier Nested Diagnostic Context (NDC) and Mapped Diagnostic Context (MDC)
* ThreadContext adds unique data stamps ("fish tags") to individual log entries
  on a per-thread basis.
* ThreadContext enhances over Log4jv1 and Logback NDC and MDC.

### Log4j2 ThreadContext and Nested Diagnostic Context(NDC) <!-- { -->
* NDC is based on the idea of a stack. Information can be 
  pushed/popped from the stack.  The values in the stack can then be 
  accessed by a Logger without having to pass a value to the logging 
  method explicitly.
* Example with Log4j+NDC: Associate username with log entry<br/>
  ```
  import org.apache.logging.Log4j.ThreadContext;
  ...
  try {
    ThreadContext.push("admin" /*user*/     );
    ThreadContext.push("1234"  /*sessionID*/);
    logger.info("Login successful");
    ThreadContext.pop();
    ...
  } final {
    // Reclaim memory used by the stack, prevent memory-leaks
    ThreadContext.removeStack()
  }
  ─────────────────────────────────────────────────────────────
  <PatternLayout pattern="%x %-5p - %m%n" />
                          └┘
  extract values from the NDC. Output will be similar to:
  "admin 1234 INFO - Login successful."
  ```
<!-- } -->

### Log4jv2 ThreadContext and Mapped Diagnostic Context (MDC) <!-- { -->
* MDC stores data as key-value pairs (vs a stack).
  ```
  import org.apache.logging.Log4j.ThreadContext;
  ...
  try {
    ThreadContext.put("username",  "admin"); // ThreadContext.remove("username") to remove.
    ThreadContext.put("sessionID", "1234");
    logger.info("Login successful");
  } final {
    ThreadContext.clearMap();  // clean map. Avoid memory-leaks.
  }
  ```
  Logging configuration will be similar to:
  ```
  <PatternLayout 
     pattern="%X{username} %X{sessionID} %-5p - %m%n" />
  Output will be similar to:
  admin 1234 INFO - Login successful
  <PatternLayout pattern="%X %-5p - %m%n" />
                          └┘
  If no key is specified, output will be in the form {{key, value}, {key, value}}

  When using a JSONFormatter the output will be like:
  {
    "timestamp":"1431970324945",
    "level":"INFO",
    "thread":"main",
    "mdc":{ "username":"admin", "sessionID":"1234" },
    "logger":"MyClass",
    "message":"Login successful",
    "context":"default"
  }
  ```
<!-- } -->

### Logback (and SLF4j-ext) NDC and MDC  <!-- { -->

* Not supported natively but indirectly through SLF4j and the slf4j-ext package:
  ```
  import org.slf4j.MDC;
  ...
  Logger logger = LoggerFactory.getLogger(MDCLogback.class);
  try {
    ...
    MDC.put("username", "admin");
    MDC.put("sessionID", "1234");
    logger.error("Unable to open file.");
  } finally {
    MDC.clear(); // Clean, avoid memory-leaks
  }

  Logback.xml configuration:
  Add next to the Appender:
  <Pattern>[%X{username}] %X{sessionID} %-5p - %m%n</Pattern>

  Output will be similar to
  [admin] 1234 Info - Login successful
  ```
<!-- } -->
[[monitoring.logging.NDC,monitoring.logging.MDC}]]

### Log4j/Logback Filtering on ThreadContext

  ```
  <Configuration name="MyApp">
    <DynamicThresholdFilter 
        key="trace-logging-enabled"       <·· automatically adjusts log level if key matches value
        onMatch="ACCEPT"                      <·· := ACCEPT | DENY | NEUTRAL
        onMismatch="NEUTRAL">                        NEUTRAL: defer to next filter
      <KeyValuePair key="true" value="TRACE" /> <·· Appender will log TRACE-level messages
    </DynamicThresholdFilter>                       if trace-logging-enabled=true, even if
  ...                                               root Logger is set to a higher level.
  </Configuration>
  ```

### Log4j/Logback? ThreadContextMapFilter ("filter")

  ```
  <Console name="ConsoleAppender" target="SYSTEM_OUT">
    <ThreadContextMapFilter                   <·· filter log in Appender/Handle
        onMatch="ACCEPT" onMismatch="DENY">
      <KeyValuePair                           <·· Log if username==admin , drop otherwise
          key="username" value="admin" />
    </ThreadContextMapFilter>
  ...
  </Console>
  ```

### logback.xml full config example <!-- { -->

* [REF@nurkiewicz.com](https://www.nurkiewicz.com/2011/09/logging-exceptions-root-cause-first.html),
  [REF@qos.ch](http://logback.qos.ch/manual/layouts.html)
* logback.xml is more verbose than log4j(v2).
  ```
  (.../src/main/resources/)logback.xml

  <?xml version="1.0" encoding="UTF-8"?>
  <configuration>
    <root level="ALL">                      <·· Apply to all packages/levels
      <appender                             <·· Add file appender.
         name="APPENDER_FILE" 
         class="ch.qos.logback.core.rolling.RollingFileAppender">
         <file>log</file>
         <rollingPolicy
            class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>%d{yyyy-MM-dd}.log.zip</fileNamePattern> <!-- daily rollover -->
              <maxFileSize>1MB</maxFileSize>
              <!-- keep 2 days' worth of history capped at 1MB total size -->
              <maxHistory>2</maxHistory>
              <totalSizeCap>2MB</totalSizeCap>
          </rollingPolicy>
          <encoder>
            ..(see below encoder for APPENDER_STDOUT ..)
          </encoder>
      </appender>

      <appender   
          name="APPENDER_STDOUT"
          class="ch.qos.logback.core.ConsoleAppender">
        <encoder>                                                           [[{troubleshooting]]
          <pattern>%d{HH:mm:ss.SSS} | %-5level | %thread | %logger{1} |
    %m%n%rEx{full,                                      <·· filter "Noise" in stack trace.
              java.lang.reflect.Method,                 <·· remove Java reflection
              sun.reflect,
              org.apache.catalina,                      <·· remove catalina engine (in tomcat)
              org.springframework.aop,                  <·· remove "almost" whole Spring framework
              org.springframework.security,                
              org.springframework.transaction,             
              org.springframework.web,                     
              net.sf.cglib,                             <·· remove CGLIB classes.
              ByCGLIB
            }
            </pattern>
        </encoder>                                                         [[}]]
      </appender>
    </root>

    <root level="WARN">                                 <·· Aply to all packages/WARN+ logs
        <appender-ref   ref="APPENDER_FILE"   />
        <appender-ref   ref="APPENDER_STDOUT" />
    </root>

    <logger name="my.company."          level="INFO" /> <·· Detail level for packages
    <logger name="my.company.package01" level="DEBUG"/>
    <logger name="org.eclipse.jetty"    level="WARN" />

  </configuration>
  ```
<!-- } -->

[[monitoring.logging.log4j,monitoring.logging.logback}]]

### About Syslog <!-- { -->
* syslog server running on a local/central server collecting logs
  from the device's OS, processes, and services.
* Syslog events are categorized by facility, which specifies the
  type of event being logged (auth, ...).
* Natively supported in Log4j and Logback by just indicating 
  the host number, port number, and protocol.
<!-- } -->
[[monitoring.logging}]]

## Security 101 [[{security.101,PM.low_code,spring,security.jvm]]
@[https://snyk.io/blog/10-java-security-best-practices/]
1. Make code immutable:
   * Tag variables as 'final' by default.
     (a final variable is not variable but constant)
   * Try to initialize all classes in constructors. and centralize
     all input checks in the constructor. Raise a runtime exception
     if the constructor is not happy with input data.
     (vs using getters and setters).
      Doing so warrants that a class is properly instantiated and
     safe upon constructor exit.
      This also warrants fail-fast applications. If something is
     undefined at startup time constructors will not be able to
     initialize and the application will fail to start: This is
     life-saving for DevOps and normal deployments.
2. Parameterize SQL:
   ```
   query = "SELECT ... WHERE lastname = "  + parameter ;  // ← Incorrect:
   Statement         stm01 = con.createStatement();

   query = "SELECT ... WHERE lastname = ?";               // ← Correct
   PreparedStatement stm01 = con.prepareStatement(query);
   statementB .setString(1, parameter);

   ...  stm01.executeQuery(query);
   ```
3. Use OpenID Connect with 2FA:<br/>
   * OAuth 2.0: Identity delegation. N applications delegate login
     authentication to a third service that emmits signed tokens
     with claims about authorization for a given authenticated 
     user.
   * OpenID: OAuth 2.0 extension providing user information
     claims to the signed OAuth 2.0 token.
     It adds an ID token in addition to an access token plus
     a /userinfo endpoint to retrieve additional information
     plus endpoint discovery and dynamic client registration.
   * Example low-code OpenID in Spring:
     ```
     STEP 1: Addd next dependencies:
     org.springframework.boot:spring-boot-starter-oauth2-client
     org.springframework.boot:spring-boot-starter-security

      STEP 2: Spring Configuration
      │ spring:
      │  ...
      │  security:
      │    oauth2:
      │      client:
      │        registration:
      │          github:
      │            client─id: ...
      │            client─secret: ....
      │          okta:
      │            client─id: ...
      │            client─secret: ...
      │            client─name: ...
      │          google:
      │            ...
      │        provider:
      │          okta:
      │            issuer─uri: https://.....okta.com/oauth2/default
     ```
4. Scan dependencies for known vulnerabilities<br/>
   Ej.: Use a service like  @[https://snyk.io/test/] !!!
   
5. Handle sensitive data with care
   * Use secrets on-demand and remove from memory as soon as possible.
     Ideally manage secrets through HSM. Secrets (signature keys, password,
     ...) must never leave the HSM.
  
6. Sanitize all input:
   * Example 1, sanitizing HTML
   Add dependency: `org.owasp.encoder:encoder:vX.Y.Z`
   ```
   final String
      untrustedInput = "<script> alert(1); </script>",
        trustedInput = StEncode.forHtml(untrustedInput));
   ```

7. Configure your XML-parsers to disable XXE (eXternal Entity)
   ```
   <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <!DOCTYPE bar [
         <!ENTITY xxe SYSTEM   "file:///etc/passwd" >] >      <·· attack vector
         <song>
            <artist>  &xxe; </artist>
            ...
         </song>
   ```
   * e.g. In xerces 1/2 disable external entities and doctypes like:
   ```
   ...
   factory   = SAXParserFactory.newInstance();
   saxParser = factory.newSAXParser();
   factory.setFeature(
     "https://xml.org/sax/features/external-general-entities", false );
   saxParser.getXMLReader().setFeature(
     "https://xml.org/sax/features/external-general-entities", false );
   factory.setFeature(
     "https://apache.org/xml/features/disallow-doctype-decl" , true );
   ...
   ```
8. Avoid Java serialization as much as possible
   * Java serialization is also called   “the gift that keeps on giving” .
     Oracle is planning to eventually remove Java serialization as part
     of Project Amber.
   * If you really need to implement serializable on your domain entities,
     implement its own readObject().
     ```
     private final void
     readObject(ObjectInputStream in) throws java.io.IOException {
       // check 1
       // check 2
       ...
       throw new java.io.IOException("Deserialized not allowed");
     }
     ```
   * If you need to Deserialize an inputstream yourself, you should use an
     ObjectsInputStream with restrictions.<br/>
     e.j Apache Commons IO ValidatingObjectInputStream, that checks whether
         the object being deserialized is allowed or not.
         ```
         final FileInputStream fileInput = new FileInputStream(fileName);
         ValidatingObjectInputStream in = new ValidatingObjectInputStream(fileInput);
         in.accept(Foo.class);
         Foo foo_ = (Foo) in.readObject();
         ```
   * Object deserialization can also apply to JSON, XML, ...
     [REF@snyk.io](https://snyk.io/blog/jackson-deserialization-vulnerability/)
9. Use strong encryption and hashing algorithms.<br/>              [[{security.cryptography}]]
   TIP: Prefer Google Tink  (vs Low Level Java crypto libraries)
10. Enable the Java Security Manager                               [[{security.jvm]]
    * By default, JVM imposes no restrictions to running apps.
      (file system, network, ..)
    * Ex. By default the Attach API is active allowing to easely
      change bytecode of running apps. (from inside the machine).
    * Activate it like:
      ```
      $ java ... -Djava.security.manager            <·· Use default policy

      $ java ... -Djava.security.manager \          <·· Use custom  policy
        -Djava.security.policy==.../custom.policy       <·· == : replace default
                                                             = :  expand  default
      ```
    * More info at [oracle.com](https://docs.oracle.com/en/java/javase/11/security/permissions-jdk1.html)
      ```
      java.awt.AWTPermission                      java.sql.SQLPermission
      java.io.FilePermission                      java.util.logging.LoggingPermission
      java.io.SerializablePermission              java.util.PropertyPermission
      java.lang.RuntimePermission                 javax.management.MBeanPermission
      java.lang.management.ManagementPermission   javax.management.MBeanServerPermission
      java.lang.reflect.ReflectPermission         javax.management.MBeanTrustPermission
      java.net.NetPermission                      javax.management.remote.SubjectDelegationPermission
      java.net.URLPermission                      javax.net.ssl.SSLPermission
      java.net.SocketPermission                   javax.security.auth.AuthPermission
      java.nio.file.LinkPermission                javax.security.auth.PrivateCredentialPermission
      java.security.AllPermission                 javax.security.auth.kerberos.DelegationPermission
      java.security.SecurityPermission            javax.security.auth.kerberos.ServicePermission
      java.security.UnresolvedPermission          javax.sound.sampled.AudioPermission [[}]]
      ```
11. Centralize logging and monitoring
[[security.101}]]

# Functional Programming [[{ $functional_programming ]]
[[{java_lang.101,java_lang.functional.lambdas,async/reactive.*,java_lang.101]]
## Lambdas 1.8+
* simplifies development of non-blocking style APIs
  (low-level CompletableFuture or higher level ReactiveX).
### Context
  The conventional computing model of a Touring machine takes for granted
  that data is already available to be processed.
  In the Internet era, data is arriving at random and we don't want to block
  our CPU in an infinite loop waiting for such data to arrive.

  The conventional approach is to let the OS scheduler to divide the CPU
  into threads or processes sharing the hardware at defined intervals.
  While this approach works well for standard load scenarios, it fails for
  "moderm" workloads with thousands or tens of thousands of simultaneous
  clients accessing the server. Each new OS thread requires some extra
  memory on the OS kernel (about 2Kilobytes per Thread, and even more
  per Process). Switching from thread to thread or process to process becomes
  expensive or prohibitive with that number of concurent I/O flows.
  This is even worse when our server is virtualized with many other
  competing VMs running on the same physical server.

  Async programming will try to reause the same thread by many different
  clients or flows of I/O data providing a much better ussage of hardware
  resources and avoiding unnecesary context-switches between threads or
  processes.

  The term "reactive" refers to programming models that are built around
  reacting to change — network component reacting to I/O events, UI controller
  reacting to mouse events, etc. In that sense non-blocking is reactive because
  instead of being blocked we are now in the mode of reacting to notifications
  as operations complete or data becomes available.

  Spring Reactive Streams is a small spec, also adopted in Java 9, that defines
  the interaction between asynchronous components with back pressure. For
  example a data repository — acting as Publisher, can produce data that an
  HTTP server — acting as Subscriber, can then write to the response. The main
  purpose of Reactive Streams is to allow the subscriber to control how fast or
  how slow the publisher will produce data.

  Reactive Streams is of interest to low-level reusable libraries but
  no final applications are better suites using a higher level and richer
  (functional) API like Java8+ Collection-Stream API or more ingeneral APIs
  like those provided by ReactiveRX.

  Reactive programming can also be compared with the way data flows in Unix
  pipelines when handling text files. In the next Unix command there is a
  file input (it can be a real file in the hard-disk or a socket receiving
  data) and the different commands in the pipe consume STDIN and result to
  STDOUT for further processing.
  ```
  $ cat input.csv | grep "...." | sort | uniq | ... >  output.csv
  ```
  Reactive Java frameworks are ussually much fasters since everything executes
  on the same process (a Unix pipeline requires the help of the underlying
  OS to work), and the type of input/output data can be any sort of Java
  object (not just file text).

### Sytnax  <!-- { -->
  ```
  (parameters) -> expression
  (parameters) -> { statements; }
  
  Function<Long, String> f = (l) -> l.toString();    // <·· takes a Long, returns a String
  
  Supplier<Thread> s = Thread::currentThread;        // <·· takes nothing, gives you Thread
  
  Consumer<String> c = System.out::println;          // <·· takes a string as the parameter
  
  new ArrayList<String>().stream()....               // <·· use lambdas in streams
  
  peek ( e -> System.out.println(e)). ...            // <·· peek: Debug streams without changes
  
  map ( e -> e.hashCode())...                        // <·· map: Convert every element into something
  
  // filter (hc -> (hc % 2) == 0) ...
  
  collect(Collectors.toCollection(TreeSet::new))    // <·· collect all values from the stream
  ```
<!-- } -->

## jOOλ functional extensions to Java 8 [[{java_lang.functional.jOOλ,PM.TODO,]]

* [jOOλ](https://github.com/jOOQ/jOOL)

**Problem Context**: "Pains with checked exceptions and lambdas"  [[{java_lang.101]]
* http://stackoverflow.com/q/18198176/521799
* http://stackoverflow.com/q/19757300/521799
* http://stackoverflow.com/q/14039995/521799                      [[java_lang.101}]]
**Solution**: `org.jooq.lambda.Unchecked`

* Standard Java 8
```
  Standard Java 8:                 
  ================                 
  Arrays                           
  .stream(dir.listFiles())         
  .forEach(file -> {               
        try {                      
    System.out.println(            
         file.getCanonicalPath()); 
        } catch (IOException e) {  
    throw new RuntimeException(e); 
        }
  });
```
* using jooq labmda wrapper
```
  using jooq labmda wrapper:     
  ==========================     
  Arrays                         
  .stream(dir.listFiles())       
  .forEach(                      
    Unchecked.consumer(file -> { 
      System.out.println(        
        file.getCanonicalPath());
    })
  );
```
* Even simpler
```
  Arrays
  .stream(dir.listFiles())
  .map(Unchecked.function(
       File::getCanonicalPath))
  .forEach(System.out::println);
```
[[java_lang.functional.jOOλ}]]

[[java_lang.functional.lambdas}]]

## Collectors 1.8+ [[{java_lang.101,JAVA_LANG.FUNCTIONAL,PM.TODO}]]

## Monads in Java [[{java_lang.functional,java_lang.101,PM.TODO]]
@[https://dzone.com/articles/what-is-a-monad-basic-theory-for-a-java-developer?edition=672394]
* two of the most commonly known Java 8 features are monad
  implementations, namely Stream and Optional.
* Monad is a concept:  we can view it as a wrapper which puts our
  value in some context and allows us to perform operations on the
  value. In this context, the output of an operation at any step is the
  input to the operation at the next step.
[[}]]

## Unix4j [[{PM.low_code.unix4j,java_lang.functional]]
* [Link@Github](https://github.com/tools4j/unix4j)

> ...Working in the finance industry in Melbourne, the authors of
> Unix4j spent a lot of time writing complex text processing
> applications in bash. Frustrated with the inherent limitations of the
> bash language; lack of language support, IDEs, test frameworks etc,
> the authors decided to try and bring the convenience of some of the
> Unix commands into the Java language.
> You may notice that the implemented commands are more bent towards
> text processing rather than OS and file manipulation. This is
> intended as we see text processing to be the real benefit of Unix4j.
> Not to say that this will always be the case.

* Example Ussage:
  ```
  Unix4j.cat("test.txt").grep("Tuesday").sed("s/kilogram/kg/g").sort();
  ```
[[PM.low_code.unix4j,}]]

## java.util.concurrent.Flow(1.9+) [[{java_lang.functional,async/reactive.io,async/reactive.101,PM.TODO]]
@[https://docs.oracle.com/javase/9/docs/api/java/util/concurrent/Flow.html]
* JDK 1.9+
* Reactive Streams was adopted by the JDK in the form of the java.util.concurrent.Flow API.
* It allows two different libraries that support asynchronous 
  streaming to connect to each other, with well specified semantics 
  about how each should behave, so that backpressure, completion, 
  cancellation and error handling is predictably propagated between the 
  two libraries.
* There is a rich ecosystem of open source libraries that support 
  Reactive Streams, and since its inclusion in JDK9, there are a few in 
  development implementations that are targetting the JDK, including 
  the incubating <a href="http://openjdk.java.net/jeps/321">JDK9 HTTP Client</a>,
  and the [Asynchronous Database Adapter (ADBA)](https://twitter.com/brunoborges/status/915302682939711488)
* See also @[http://www.eclipse.org/community/eclipse_newsletter/2018/may/reactivestreams-ee4j.php]
[[}]]

# RxJava [[{async/reactive.rxjava,qa]]
* @[https://github.com/ReactiveX/RxJava]
* ReactiveX provides a set of very-well thought-out cross-language abstraction to
  implement the reactive patterns.

  ```
 Observable/Operators/Observers
@[http://reactivex.io/documentation/contract.html]
@[http://reactivex.io/documentation/observable.html]
                                         ⇑
                                    <<Subscriber>>
                                    unsubscribe  : - Observable can opt to stop
                                                   event-emission if no more clients
                                                   are subcribed
 Loop-of-Observable-emitted-events:                - unsubscription will cascade back
 ·  Observable → <<IObserver>>: onNext(event)      through the chain of operators
 ·                                                 applying to associated Observable.
 ·  observer   →   observer*1 : handle event    *1 also called "subscriber", "watcher"
 ·                                                 or "reactor" ("reactor pattern")
 end-loop
 Observable    → <<handler>>  : onCompleted()
                  (instance)
 observer      →  observer    : handle event

   WARN:  There is no canonical naming standard in RXJava


  Observable<T>  → operator1 → ... → operatorN  →  Observer
  pushes objects       ^        ^      ^           Subscribes to
 (events) from         ·        ·      ·           the observable
 any source            ·        ·      ·           events
 (ddbb, csv,...)       ·        ·      ·           .onNext()
                       ·        ·      ·           .onCompleted()
          TIP :Learning which operators to use     .onError()
               for a situation and how to combine  .onSubscribe(Disposable d);
               them is the key to master RxJava    ^
┌──────────────────────────────────────────────────┘
├─onSubscribe(Disposible): Free any resources created by the rxJava pipeline.
├─onNext()     : passes each item, one at a time,   to the  Observer
├─onCompleted(): communicates a completion event    to the  Observer
└─onError()    : communicates an error up the chain to the  Observer
                 where the Observer typically defines how to handle it.
                 Unless retry() operator is used to intercept the error,
                 the Observable chain typically terminates, and no
                 more emissions will occur.
                 See also   operators 'Catch' and 'Retry'

By default, Observables execute work on the immediate thread,
which is the thread that declared the Observer and subscribed it.
Not all Observables will fire on the immediate thread, (Observable.interval(),...)

 Creating a source Observable:
  Observable<String> source01 = Observable .just ("value1",...,"valueN");
  Observable<String> source02 = Observable .fromCallable (() -> 1/0);
                                            ^^^^^^^^^^^^        ^^^
                                            Similar to .just() but errors
                                            are captured by the rxJava "pipeline"

  Observable<String> source02 = Observable .create ( emitter ->
  {
        try {
    emitter .onNext (myList.get(0));
    emitter .onNext (myList.get(1));
    ...
    emitter .onComplete() ; // ← Optional
        } catch(Throwable e) {
    emitter .onError(e) ;
        }
  } );
  Observable<String>  source03 = Observable .fromIterable (myIterableList);
  Observable<Integer> source04 = Observable .range (1,10);
  Observable<String>  source05 = Observable .interval (1, TimeUnit.SECONDS);
                                             ^^^^^^^^
                                             Since it operates on a timer →
                                             needs to run on separate thread
                                             and will run on the computation
                                             Scheduler by default

  Observable<String>  source06 = Observable .fromFuture (myFutureValue);
  Observable<String>  source07 = Observable .empty ();
                                             ^^^^^
                                             calls onComplete() and ends

  Observable<String>  source08 = Observable .defer ( () -> Observable.range(start,count));
                                             ^^^^^
                                      Advanced factory pattern.
                                      allows a separate state for each observer
  Observable<String>  source09 = Observable .fromCallable ( () -> Observable.range(start,count));

 creating Single/Maybe/Completable "Utility" Observables:
 ┌─────────────────────────────┬────────────────────────────────┬─────────────────────────────────┐
 │ Single.just("Hello")        │ Maybe.just("Hello")            │ Completable.fromRunnable(       │
 │ .subscribe(...);            │   .subscribe(...);             │   () -> runProcess() )          │
 │                             │                                │ .subscribe(...);                │
 ├─────────────────────────────┼────────────────────────────────┼─────────────────────────────────┤
 │ Emits a single item         │ Emits (or not)a single item    │  does not receive any emissions │
 ├─────────────────────────────┼────────────────────────────────┼─────────────────────────────────┤
 │ SingleObserver              │  MaybeObserver                 │  CompletableObserver            │
 │ .onSubscribe(Disposable d); │  .onSubscribe(Disposable d);   │  .onSubscribe(Disposable d);    │
 │ .onSuccess(T value);        │  .onSuccess(T value);          │  .onComplete();                 │
 │ .onSuccess(Throwable error);│  .onSuccess(Throwable error);  │  .onError(Throwable error);     │
 │                             │  .onComplete();                │                                 │
 └─────────────────────────────┴────────────────────────────────┴─────────────────────────────────┘

 Create Test-oriented observables
   Observable .never ();
   Observable .error ();

 Derive Observables from source:
Observable<Integer> lengths  =  source .map    (String::length);
Observable<Integer> filtered = lengths .filter (i -> i  = 5);

 creating an Observer:
(Lambdas in the source Observable .subscribe can be used in place)
Observer<Integer> myObserver = new Observer<Integer>() {
  @Override public void onSubscribe(Disposable d) { //... }
  @Override public void onNext(Integer value)     { log.debug("RECEIVED: " + value); }
  @Override public void onError(Throwable e)      { e.printStackTrace();}
  @Override public void onComplete()              { log.debu("Done!"); }
};

  Cold/Hot Observables
  └ Cold: - Repeat the same content to different observers.
          - Represent sort-of inmmutable data.
          - A "cold" Observable waits until an observer subscribes to it
            an observer is guaranteed to see the whole sequence of events
  └ Hot : "Broadcast" to all observers at the same time.
          - A "hot" Observable may begin emitting items as soon as it is created.
          - An observer connecting "later" will loose old emissions.
          - Represent real-time events . They are time-sensitive.
          - Emissions will start when first observers calls connect().
          - a cold/hot observable can generate a new hot observable by
            calling publish() that will return a hot ConnectableObservable.
            Helpful to avoid the replay of data on each subscrived Observer.

          - NOTE: A "Connectable" Observable: does NOT begin emitting items
              until its Connect method is called, whether or not any observers
              have subscribed to it.
  ```

* In RxJava 2.0, Observables **NO LONGER SUPPORT EMITTING null VALUES** !!! [[{qa.101,qa.billion_dolar_mistake}]]

[[{doc_has.decission_tree]]
## RxJava Decision Tree  (Choosing the right Operator for a task) 
* [REF](http://reactivex.io/documentation/operators.html#tree)
  ```
  Alphabetical List of Observable Operators
  @[http://reactivex.io/documentation/operators.html#alphabetical

  Core API
  └ rx.Observable "==" [Java 8 Stream + CompletableFuture + "Back-presure" measures ]
    @[http://reactivex.io/documentation/observable.html]    └──────────────┬──────┘
                                                                           │
                                                            probably an intermediate
                                                            buffer for incomming/outgoing
                                                            messages that acts async.
                                                            when not full, and sync when
                                                            full.
    -  rx.Single : specialized version emiting a single item

  └ rx.Operators :
    - Compose Observables in a chain
    - gives the real "reactive" power
    - operators allow to transform, combine, manipulate, and work
      with the sequences of items emitted by Observables.
    - declarative programming
      - AVOID THE THE DRAWBACKS OF CALLBACK-NESTING("callback hell")
      - Most operators operate on an Observable and return an Observable.
        Each operator in the chain modifies the Observable that results
        from the operation of the previous operator. Order matters.
        (the Builder Pattern, also supported, is non-ordered)
  └ rx.Subject :
  @[http://reactivex.io/documentation/subject.html]
    sort of "bridge or proxy" is available in some implementations
    that acts both as an observer and as an Observable.
  └ rx.Scheduler :
  @[http://reactivex.io/documentation/scheduler.html]
    - Needed when using multithreading into the
      cascade of Observable operators.
    - By default, the chain of Observables/operators
      will notify its observers  on the same thread
      on which its Subscribe method is called
    Operator|SubscribeOn         |ObserveOn
    --------+--------------------+---------
            |sets an Scheduler on|sets an Scheduler used
            |which the Observable|by the Observable to
            |should operate.     |send notifications to
            |                    |its observers.
    Scheduler "==" Thread
  ```
[[}]]

## External links:
* Rx Workshop: Introduction
 @[https://channel9.msdn.com/Series/Rx-Workshop/Rx-Workshop-Introduction]
* Introduction to Rx: IObservable
 @[http://introtorx.com/Content/v1.0.10621.0/02_KeyTypes.html#IObservable]
* Mastering observables (from the Couchbase Server documentation)
 @[https://developer.couchbase.com/documentation/server/3.x/developer/java-2.0/observables.html]
* 2 minute introduction to Rx by Andre Staltz (“Think of an Observable as an asynchronous immutable array.”)
 @[https://medium.com/@andrestaltz/2-minute-introduction-to-rx-24c8ca793877]
* Introducing the Observable by Jafar Husain (JavaScript Video Tutorial)
 @[https://egghead.io/lessons/rxjs-introducing-the-observable]
* Observable object (RxJS) by Dennis Stoyanov
 @[http://xgrommx.github.io/rx-book/content/observable/index.html]
* Turning a callback into an Rx Observable by @afterecho
 @[https://afterecho.uk/blog/turning-a-callback-into-an-rx-observable.html]

## Ops.classification 1
* Operators creating new Observables
  ```
@[http://reactivex.io/documentation/operators.html#creating]
    Create     create Observable from scratch programmatically
    Defer      do not create the Observable until the observer subscribes,
               and create a fresh Observable for each observer
    Empty      create Observables that have very precise and limited behavior
    Never        "
    Throw        "
    From       create some object or data structure
    Interval   create Observable that emits a sequence of integers spaced
               by a particular time interval
    Just       convert an object or a set of objects into an Observable that emits that or those objects
    Range      create an Observable that emits a range of sequential integers
    Repeat     create an Observable that emits a particular item or sequence of items repeatedly
    Start      create an Observable that emits the return value of a function
    Timer      create an Observable that emits a single item after a given delay

 Operators Transforming Items:
@[http://reactivex.io/documentation/operators.html#transforming]
    Buffer     periodically gather items from input into bundles and emit
               these bundles rather than emitting the items one at a time

    FlatMap    transform the items emitted by an Observable into Observables,
               then flatten the emissions from those into a single Observable
    GroupBy    divide an Observable into a set of Observables that each emit a
               different group of items from the original Observable,
               organized by key
    Map        transform each input-item by applying a function
    Scan       apply a function to each item emitted by an Observable,
               sequentially, and emit each successive value
    Window     periodically subdivide items from an Observable into
               Observable windows and emit these windows rather than emitting
               the items one at a time
  ```
* Operators selectively filtering emitted events from a source Observable
  ```
@[phttp://reactivex.io/documentation/operators.html#filtering]
    Debounce   only emit an item from an Observable if a particular timespan has passed
               without it emitting another item
    Distinct   suppress duplicate items emitted by an Observable
    ElementAt  emit only item n emitted by an Observable
    Filter     emit only those items from an Observable that pass a predicate test
    First      emit only the first item, or the first item that meets a condition, from an Observable
    IgnoreElements   do not emit any items from an Observable but mirror its termination notification
    Last       emit only the last item emitted by an Observable
    Sample     emit the most recent item emitted by an Observable within periodic time intervals
    Skip       suppress the first n items emitted by an Observable
    SkipLast   suppress the last n items emitted by an Observable
    Take       emit only the first n items emitted by an Observable
    TakeLast   emit only the last n items emitted by an Observable
  ```
* Operators Combining multiple source Observables into a new single Observable
  ```
@[http://reactivex.io/documentation/operators.html#combining]
    And        combine sets of items emitted by two or more Observables by means
    Then       of Pattern and Plan intermediaries
    When

    CombineLatest   when an item is emitted by either of two Observables, combine the
               latest item emitted by each Observable via a specified function and
               emit items based on the results of this function
    Join       combine items emitted by two Observables whenever an item from one
               Observable is emitted during a time window defined according to an
               item emitted by the other Observable
    Merge      combine multiple Observables into one by merging their
               emissions
    StartWith  emit a specified sequence of items before beginning to emit the
               items from the source Observable
    Switch     convert an Observable that emits Observables into a single
               Observable that emits the items emitted by the most-recently-
               emitted of those Observables
    Zip        combine multiple Observables emissions together via a function
               function → emit single items for each input tuple
  ```
* Operators handling Errors and helping to recover from error-notifications
  ```
@[http://reactivex.io/documentation/operators.html#error]
    Catch   recover from an onError notification by continuing the sequence without error
    Retry   if a source Observable sends an onError notification, resubscribe to it
            in the hopes that it will complete without error
  ```
* Utility Operators "toolbox"
  ```
@[http://reactivex.io/documentation/operators.html#utility]
    Delay       shift the emissions from an Observable forward in time by a particular amount
    Do          register an action to take upon a variety of Observable lifecycle events

    Materialize    represent both the items emitted and the notifications sent
    Dematerialize   as emitted items, or reverse this process

    ObserveOn   specify the scheduler on which an observer will observe this Observable
    Serialize   force an Observable to make serialized calls and to be well-behaved
    Subscribe   operate upon the emissions and notifications from an Observable
    SubscribeOn   specify the scheduler an Observable should use when it is subscribed to
    TimeInterval  convert an Observable that emits items into one that emits indications
                  of the amount of time elapsed between those emissions
    Timeout     mirror the source Observable, but issue an error notification if a
                particular period of time elapses without any emitted items
    Timestamp   attach a timestamp to each item emitted by an Observable
    Using       create a disposable resource that has the same lifespan as the Observable
  ```
* Conditional and Boolean Operators evaluating one or more
  Observables or items emitted by Observables
  ```
@[http://reactivex.io/documentation/operators.html#conditional]
    All         determine whether all items emitted by an Observable meet some criteria
                Mathematical and Aggregate Operators
@   Amb         given two or more source Observables, emit all of the items from only the
                first of these Observables to emit an item
              @[http://reactivex.io/documentation/operators.html#mathematical]
    Contains   determine whether an Observable emits a particular item or not
               Average, Concat, Count, Max, Min, Reduce, and Sum
C   DefaultIfEmpty  emit items from the source Observable, or a default item if the
               source Observable emits nothing  onverting Observables
@   SequenceEqual   determine whether two Observables emit the same sequence of items
    SkipUntil   discard items emitted by an Observable until a second Observable emits an item To
C   SkipWhile   discard items emitted by an Observable until a specified condition becomes false
  ```
* Connectable Observable Operators
  ```
[http://reactivex.io/documentation/operators.html#connectable]
@   TakeUntil   discard items emitted by an Observable after a second Observable emits an item or terminates
    TakeWhile   discard items emitted by an Observable after a specified condition becomes false
    Connect
    Publish
    RefCount
    Replay
  ```
* Mathematical and Aggregate Operators
  ```
- Operators that operate on the entire sequence of items emitted by an Observable
    Average     calculates the average of numbers emitted by an Observable and emits this average
    Concat      emit the emissions from two or more Observables without interleaving them
    Count       count the number of items emitted by the source Observable and emit only this value
    Max         determine, and emit, the maximum-valued item emitted by an Observable
    Min         determine, and emit, the minimum-valued item emitted by an Observable
    Reduce      apply a function to each item emitted by an Observable, sequentially, and emit the final value
    Sum         calculate the sum of numbers emitted by an Observable and emit this sum
  ```
* Backpressure Operators
  ```
a variety of operators that enforce particular flow-control policies
    @[http://reactivex.io/documentation/operators/backpressure.html]
- backpressure operators  strategies  for coping with Observables that produce items more
  rapidly than their observers consume them
  ```
* Connectable Observable Operators
  ```
Specialty Observables that have more precisely-controlled subscription dynamics
    Connect   instruct a connectable Observable to begin emitting items to its subscribers
    Publish   convert an ordinary Observable into a connectable Observable
    RefCount  make a Connectable Observable behave like an ordinary Observable
    Replay    ensure that all observers see the same sequence of emitted items, even if they
              subscribe after the Observable has begun emitting items
  ```
* Operators to Convert Observables
  ```
    To        convert an Observable into another object or data structure
  ```

## Ops.classification 2
* Basic Operators:
  * Suppressing operators:
    * filter, take, skip, takeWhile/skipWhile, distinct, distinctUntilChanged
  * Transforming operators:
    * map, cast, startWith, defaultIfEmpty, switchIfEmpty, sorted, deplay,repeat, scan
  * Reducing operators:
    * count, reduce, all, any, contains
  * Collection operators:
    * toList, toSortedList, toMap, toMultiMap, collect
  * Error recovery Operators
    * onErrorReturn, onErrorReturnItem, onErrorResumeNext, retry
  * Action ("stream life*cicle") Operators:
    * doOnNext, doOnComplete, doOnError, doOnSubscribe, doOnDispose
* Combining Observables:
  * Merging:
    * merge, mergeWith
    * flatMap
  * Concatenation:
    * concat, concatWith
    * concatMap
  * Ambiguous:
    * amb
  * Zipping
  * Combine Latest:
    * withLatestFrom
  * Grouping:
    * groupBy
* Multicasting, Replaying and Caching:
  ( Multicasting is helpful in preventing redundant work being done by multiple
    Observersand instead makes all Observers subscribe to a single stream,
    at least to the point wherethey have operations in common)
  * "Hot" operators. (TODO)
  * Automatic connection:
    * autoConnect, refCount, share
  * replay
  * cache
* Subjects
  * Just like mutable variables are necessary at times even though you
    should strive forimmutability, Subjects are sometimes a necessary tool
    to reconcile imperative paradigmswith reactive ones.
  * PublishSubject
  * Serializin Subject
  * BehaviourSubject
  * ReplaySubject
  * AsyncSubject
  * UnicastSubject

## TODO [[{PM.TODO,troubleshooting,qa.error_control]]
* Custom Ops
@[http://reactivex.io/documentation/implement-operator.html]
[[{async/reactive.rxjava,qa,PM.TODO}]]
* 5 Not So Obvious Things About RxJava
@[https://medium.com/@jagsaund/5-not-so-obvious-things-about-rxjava-c388bd19efbc] 
* Error control 
@[https://github.com/ReactiveX/RxJava/wiki/Error-Handling-Operators]
* Dealing with RxJava's never-ending Observables
@[https://tech.xing.com/dealing-with-rxjavas-never-ending-observables-1ca8f18797ff]
[[}]]

[[{java_lang.functional,qa,async/reactive.*,PM.WiP]]
## java.utility.function
@[https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html]
@[https://docs.oracle.com/javase/8/docs/api/java/lang/FunctionalInterface.html]
* JDK 1.8+
* Incomplete but good enough to cover the "shape" of many lambda 
  expressions and method references representing abstract concepts like 
  functions, actions, or predicates
* The @FunctionalInterface is used to capture design intent (not needed by compiler).
* In documenting functional interfaces, or referring to variables 
  typed as functional interfaces, it is common to refer directly to 
  those abstract concepts, for example using "this function" instead of 
  "the function represented by this object".
* Each functional interface has a single abstract method, called the 
  functional method for that functional interface, to which the lambda 
  expression's parameter and return types are matched or adapted.
* Functional interfaces can provide a target type in multiple 
  contexts, such as assignment context, method invocation, or cast 
  context:
  ```
  Predicate<String> p = String::isEmpty;           // Assignment context
  stream.filter(e -> e.getSize() > 10)...          // Method invocation context
  stream.map((ToIntFunction) e -> e.getSize())...  // Cast context
  ```
* Defined functions in 1.8
  ```
  ────────────────────────────────────────────┬────────────────────────────────────────────────────────────────────────────────
             Interface Summary                │           Interface Description
  ────────────────────────────────────────────┼────────────────────────────────────────────────────────────────────────────────
                    BiConsumer‹T,U›           │opt. accepting two input arguments and returns no result
    (|Double|Int|Long)Consumer‹T›             │opt. accepting a single (Object|double|int|long)input argument and returns no result
  Obj(Double|Int|Long)Consumer‹T›             │opt. accepting an object-valued and a (double|int|long)-valued argument, and returns no result
  ────────────────────────────────────────────│────────────────────────────────────────────────────────────────────────────────
          (|Double|Long|Int)Function‹(T,)R›   │func. that accepts an (T|double,long,int) argument and produces a result
         (|Double|Long)ToIntFunction          │func. that accepts a (T|double|long)argument and produces an int-valued result
  (ToDouble|ToLong|ToInt|)BiFunction‹(T,)U,R› │func. that accepts two arguments and produces an (T,double,long,int) result.
             To(Double|Long)Function‹T›       │func. that produces a (double|long)-valued result
  (Int|Long|Double)To(Int|Long|Double)Function│func. that accepts a (int|long|double) argument and produces a (int|long|double) result
  ────────────────────────────────────────────│────────────────────────────────────────────────────────────────────────────────
   (|Int|Long|Double)UnaryOperator‹T›         │op. on a single (T|int|long|double) operand that produces a result of the same type
  (Double|Long|Int|)BinaryOperator‹T›         │op. upon two (T|int|long|double) operands and producing a result of the same type
  ────────────────────────────────────────────│────────────────────────────────────────────────────────────────────────────────
                  BiPredicate‹T,U›            │predicate (boolean-valued function) of two arguments
  (|Int|Long|Double)Predicate‹T›              │predicate (boolean-valued function) of one (T|int|long|double) argument
  ────────────────────────────────────────────│────────────────────────────────────────────────────────────────────────────────
  (|Boolean|Int|Long|Double)Supplier(‹T›)     │supplier of (T|Boolean|Int|long|double) results
  ────────────────────────────────────────────┴────────────────────────────────────────────────────────────────────────────────
  ```
[[java_lang.functional}]]


[[ $functional_programming }]]

# Collections [[{ $collections ]]
[[{data_structures.101,java_lang.101,scalability.101,02_doc_has.decission_tree,02_doc_has.diagram,qa.data]]
## Collections 101 
* COLLECTION DECISSION TREE
  ```
                                  ┌──────────┐
                                  │  Allows  │
                    ┌─── YES ─────┤Duplicates├──  NO  ───────┐
                    │   List to   └──────────┘  Set to       │
                    │  be selected              be selected  │
                    │                                        v
                    v                                    ┌───────────┐☜ order established at
        ┌─────────────────────┐                          │ Maintains │  write time
        │  Unknown number     │                          │ INSERTION │
   ┌─NO─┤of elements will be  ├YES─┐           ┌───YES───┤   ORDER ? ├──NO──┐  order requested
   │    │added and/or index   │    │           │         └───────────┘      │  at read time
   │    │based search will not│    │           v                            ↓  ☟
   │    │be frequent?         │    │       LinkedHashSet            ┌────────────┐
   │    └─────────────────────┘    │                                │ Mantains   │
   v                               v                           ┌─NO─┤ READ ORDER ├YES┐
  ArrayList              LinkedList                            │    │(alpha,...)?│   │
                                                               │    └────────────┘   │
                                                               │                     │
                                                               v                     v
                                                            HashSet              TreeSet
  ```
### Collection Comparative
  ```
Standard   non-concurrent  SDK:
       ┌──────────────────────────────────────────────────────────────────────────────────────────
       │                                IMPLEMENTATIONS
       ├──────────────────────────────────────────────────────────────────────────────────────────
       │ Hash Table        │ Resizable Array   │Balanced Tree │ Linked List │ HashTable+LinkedList
       │                   │                   │              │             │
┌──────┼───────────────────┼───────────────────┼──────────────┼─────────────┼─────────────────────
│<Set> │ HashSet           │                   │  TreeSet     │             │ LinkedHashSet
│      │                   │                   │              │             │
├──────┼───────────────────┼───────────────────┼──────────────┼─────────────┼─────────────────────
│<List>│                   │ ArrayList         │              │ LinkedList  │
│      │                   │ Vector            │              │ LinkedList  │
├──────┼───────────────────┼───────────────────┼──────────────┼─────────────┼─────────────────────
│<Map> │ HashMap,Hashtable │                   │  TreeMap     │             │ LinkedHashMap
└──────┴───────────────────┴───────────────────┴──────────────┴─────────────┴─────────────────────

  WARN : There is a huge performance difference LinkedList and ArrayList.
         - when there is a large number of add/remove operations LinkedList is much faster.
         - When there is a lot of random access operations ArrayList is much faster.
  ```

### INTERFACES
* @[http://download.oracle.com/javase/6/docs/api/java/util/HashSet.html]
* @[http://download.oracle.com/javase/6/docs/api/java/util/TreeSet.html]
* @[http://download.oracle.com/javase/6/docs/api/java/util/LinkedHashSet.html]
* @[http://download.oracle.com/javase/6/docs/api/java/util/ArrayList.html]
* @[http://download.oracle.com/javase/6/docs/api/java/util/LinkedList.html]
* @[http://download.oracle.com/javase/6/docs/api/java/util/HashMap.html]
* @[http://download.oracle.com/javase/6/docs/api/index.html]
* @[http://download.oracle.com/javase/6/docs/api/java/util/TreeMap.html]
* @[http://download.oracle.com/javase/6/docs/api/java/util/LinkedHashMap.html]

### Cheat-sheet
* REF: @[http://files.zeroturnaround.com/pdf/zt_java_collections_cheat_sheet.pdf]
  ```
  ┌─────────────────┬─────────────────────────────────────────────────────────────┐
  │Collection       │ Thread-safe                ┃ DATA      ┃ OPERATIONS ALLOWED │
  │                 │ alternative                ┃  PROPER-  ┃────────────────────┤
  │class            │                            ┃   TIES    ┃Iterat. ┃  ┃Random  │
  │                 │                            ┃           ┃ Order  ┃  ┃ Access │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┬──┬──┃──┃──┬──┬──┤
  │HashMap          │ ConcurrentHashMap          ┃  │OK│  │  ┃  │  │  ┃OK┃OK│  │  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │SortedMap        │ ?                          ┃  │OK│  │  ┃  │  │  ┃? ┃OK│  │  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │NavigableMap @1  │ ?                          ┃  │OK│  │  ┃  │  │  ┃? ┃OK│  │  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │HashBiMap(Guava) │ Maps.syncrhonizedBiMap     ┃  │OK│  │  ┃  │  │  ┃OK┃OK│OK│  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │ArrayListMultimap│ Maps.synchronizedMultiMap  ┃  │OK│OK│  ┃  │  │  ┃OK┃OK│  │  │
  │(Guava)          │ (new ArrayListMultimap())  ┃  │  │  │  ┃  │  │  ┃  ┃  │  │  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │LinkedHashMap    │ Collections.syncrhonizedMap┃  │OK│  │  ┃OK│  │  ┃OK┃OK│  │  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │TreeMap          │ ConcurrentSkipListMap      ┃  │OK│  │  ┃  │OK│  ┃OK┃OK│  │  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │Int2IntMap (Fastutils)                        ┃  │OK│  │OK┃  │  │  ┃OK┃OK│  │OK│
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │ArrayList        │ CopyOnWriteArrayList       ┃OK│  │OK│  ┃OK│  │OK┃  ┃  │  │OK│
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │HashSet          │ Collections.newSetFromMap  ┃OK│  │  │  ┃  │  │  ┃OK┃  │OK│  │
  │                 │ (new ConcurrentHashMap())  ┃  │  │  │  ┃  │  │  ┃  ┃  │  │  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │IntArrayList(FastUtils)                       ┃OK│  │OK│OK┃OK│  │OK┃  ┃  │  │OK│
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │PriorityQueue    │ PriorityBlockingQueue      ┃OK│  │OK│  ┃  │OK│  ┃  ┃  │  │  │
  ┼─────────────────┼────────────────────────────┃──┼──┼──┼──┃──┼──┼──┃──┃──┼──┼──┤
  │ArrayDeque       │ ArrayBlockingQueue         ┃OK│  │OK│  ┃OK│  │OK┃  ┃  │  │  │
  └─────────────────┴────────────────────────────┃──┴──┴──┴──┃──┴──┴──┃──┃──┴──┴──┘
                          Individual elements ·····┘  │  │  │  │  │  │  ^  │  │  └ By Index
                          Key─value pairs  ···········┘  │  │  │  │  │  │  │  └··· By Value
                          Duplicate element support ·····┘  │  │  │  │  │  └······ By Key
                          Primite support ··················┘  │  │  │  │
                                                       FIFO  ··┘  │  │ Fast
                                                     Sorted  ·····┘  │'has(el)'
                                                       LIFO  ········┘

   Collection class │  Random access by idx/key │ Search/Contains │ Instert
   ─────────────────┼───────────────────────────┼─────────────────┼───────────
   ArrayList        │  O(1)                     │ O(n)            │ O(n)
   HashSet          │  O(1)                     │ O(1)            │ O(1)
   HashMap          │  O(1)                     │ O(1)            │ O(1)
   TreeMap          │  O(log(n))                │ O(log(n))       │ O(log(n))

@1 NavigableMap: SortedMap with additional methods for finding entries
                 by their ordered position in the key set.
                 So potentially this can remove the need for iterating
                 in the first place - you might be able to find the
                 specific entry you are after using the higherEntry,
                 lowerEntry, ceilingEntry, or floorEntry methods. The
                 descendingMap method even provides an explicit method
                 of reversing the traversal order.
  ```

## GRAPH STRUCTURES  [[{data_structures.graphs]]
* [REF](https://guava.dev/releases/20.0/api/docs/com/google/common/graph/package-summary.html)

```
Interface               Description
Graph<N>                An interface for graph-structured data, whose edges are
                        anonymous entities with no identity or information of their own.
MutableGraph<N>         A subinterface of Graph which adds mutation methods.
MutableNetwork<N,E>     A subinterface of Network which adds mutation methods.
MutableValueGraph<N,V>  A subinterface of ValueGraph which adds mutation methods.
Network<N,E>            An interface for graph-structured data, whose edges are unique objects.
ValueGraph<N,V>         An interface for graph- structured data, whose edges have associated
                        non-unique values.
```
[[data_structures.graphs}]]

## Lists [[{data_structures.lists,data_structures.101,qa]]
* INITIALIZING A LIST
```
final List<String> myList =                  ← final forbid re-asigning the list,
      Arrays.asList("one", "two", "three");     but its content is still mutable
      myList.add("four");

final List<String> myInmmutableList =        ← inmutable version (thread safe)
      Collections.unmodifiableList(myList);

String[] array01 = ...;
final      List<String> readOnlyList  ← Inmutable ArrayList (static class inside Arrays),
       = Arrays.asList(arr);            lacks set(), get(), contains() or "adding" methods
                                        providing for a read-only view.

final ArrayList<String> mutableList   ← Mutable list.
       = new ArrayList<String>(
         Arrays.asList(arr));
```

* Walk over Lists(Set/...) collections 
```
 for ( int idx = 0; idx < collection   .length ; idx++) { ← Alt 1: Java 1.0 for-walk over collection index.
   type array_element = collection   .get(idx);           ←   WARN:  Very slow for LinkedLists
 }                                                                   (Faster for other List implementations)

 for ( Iterator iterator = collection   .iterator() ;     ← Alt 2: for-walk over iterator. Preferred:
        iterator.hasNext(); ) {                                    safer when removing/modifying the collection
   type type = (type)   iterator.next() ;                          while iterating over it.
 }

 for ( iterable_type iterable_element   :collection ) {   ← Alt 3: Best option when NOT remov./modify. elements
   ...
 }

 collection .forEach ((element) -> {                      ← Alt 4: STREAMS (Java 8+): UNIX pipe like iteration.
   System.out::println(element)                             Functional approach. In this case we can "shortcut" to:
 });                                                        collection.forEach(System.out::println);
```
[[data_structures.lists}]]

## Maps ("Dictionaries") [[{data_structures.maps]]
* Initializing maps
```
final Map<String,String> myMap =                          ← Mutable map (WARN: even if 'final' used)
      new HashMap<String,String>();
  myMap.put("k1", "k2");
  ...
  final Map<String,String> myInmutableMap =               ← Inmutable version of map
        Collections.unmodifiableMap(temp);

  final HashMap<String,String> data =  HashMapBuilder     ← Java 7+ must have even number of elements
        .build( "k1","v1", "k2","v2", ...);

  final Map<String, String> inumtableMap01 =              ← Java 9+ must have even number of elements
        Map.of("k1", "v1", "k2", "v2");

  final Map<String, String> inmutableMap02 =              ← Java 9+ (Safer syntax)
        Map.ofEntries( entry("k1", "k2"),...);

  final Map<String, String> test =                        ← Guava ImmutableMap
        ImmutableMap.of("k1","v1", ...);                    works only with up to 5 key/value pairs

  final Map<String, String> test =                        ← Guava ImmutableMap alternative
        ImmutableMap.<String, String>builder()
        .put("k1", "v1").put("k2", "v2")
        ...
        .build();
```
* WALK-OVER/ITERATE A MAP
```
 Map<String, String> map = ...
 for ( Map.pair<String, String>   pair  :
       map .pairSet() ) {                                 ← Alt 1: pairSet (Java 5+)
  ... pair.getKey() ... pair.getValue() ...
 }

 Iterator<Map.Entry<String, String>> it =                 ← Alt 2: (Java 7?) using iterators
       map.entrySet .iterator() ;
 while (it.hasNext()) {
   const Map.Entry<Integer, Integer> pair =  it.next();
  ... pair.getKey() ... pair.getValue() ...
 }

 for ( Map.Entry<Integer, Integer> pair :                 ← Alt 3: USING "for : entrySet"
          myMap .entrySet() ) {
  ... pair.getKey() ... pair.getValue() ...
 }

 map.forEach((k, v) -> {                                  ← forEach (java 8+)
  ... pair.getKey() ... pair.getValue() ...
 }

 map.entrySet().stream().forEach( (pair) -> {            ← Stream API (1.8+)
 ... pair.getKey() ... pair.getValue() ...                 "Functional" Unix Pipe like style
 });

 map.entrySet().stream().                                ← Stream API parallel (1.8+)
 .parallel().forEach( pair -> {                            "Functional" Unix Pipe like style
 ... pair.getKey() ... pair.getValue() ...
 } ) );
```
* Updating maps
```
Map<String, Double> map01 = new HashMap<>();
  map01.put("key1", 1);
  map01.put("key2", 2);
  map01.put("key3", 3);                    ← Update (Before Java 8). Alt 1: Do not check for key existence.
  if (map01.containsKey("key3")) {
    map01.put("key3", 3);                  ← Update (Before Java 8). Alt 2:        check for key existence.
  }

  Integer oldValue = map01                 ← Java 8+ . Returns nulls if key1 didn't exists
         .replace("key1", 10);
  boolean isOK = map01.replace("key1",     ← Java 8+. Safer variant. Update fails if old value
           1 /*old value*/,                  is null or changed. Most probably false will throw
          10 /* new value */ );              a RuntimeError.

  map01.getOrDefault("key4", 0));          ← Return default for non-existing key (vs null)    [qa]

  map01.putIfAbsent ("key4", 4));          ← Update only if absent or value null.             [qa]


  const BiFunction<Integer, Integer, Integer>
      sumFun = (x1, x2) -> x1 + x2;

  map01.compute("key1",                         ← Use bifunction to update. NullPointerException
    (key, currentValue)                           if key doesn't exit.
        -> sumFun.apply(currentValue, 2));        (alternatively computeIfAbsent / computeIfPresent )

  map01.merge("key1", defaultValue,             ← Update with BiFunction if key exists.
    (key, currentValue)                           or add defValue to key otherwise.
        -> sumFun.apply(v, 2));
```
[[data_structures.maps}]]

## java.util.Collections [[{data_structures.collections,]]
(@[https://docs.oracle.com/javase/7/docs/api/java/util/Collections.html] )
* Utility class with static methods that operate on or return collections
  ```
  Collections.EMPTY_SET  ( == Collections.emptySet()         ) See also Collections.singleton(T o)
  Collections.EMPTY_LIST ( == Collections.emptyList()        ) See also Collections.singletonList(T o)
  Collections.EMPTY_MAP  ( == Collections.emptyMap()         ) See also Collections.singletonMap(K key, V value)
                              Collections.emptyEnumeration()
                              Collections.emptyIterator()
                              Collections.emptyListIterator()

  boolean      Collections.addAll(Collection c, T... elements)           Adds all elements to collection 'c'
  Queue        Collections.asLifoQueue(Deque deque)                      deque to Last-In/First-Out "LIFO" Queue view
  int          Collections.binarySearch(List list, T key)                Searches key in list using binary search.
  int          Collections.binarySearch(List list, T key, Comparator c)  Searches key in list using binary search + comparator.

  Collection   Collections.checkedCollection(Collection c, Class type)   Returns a dynamically typesafe view of input collection/list/...
  List         Collections.checkedList(List list, Class type)
  Map          Collections.checkedMap
                           (Map m, Class keyType, Class valueType)
  Set          Collections.checkedSet(Set s, Class type)
  SortedMap    Collections.checkedSortedMap
                           (SortedMap m, Class keyType, Class valueType)


  SortedSet    Collections.checkedSortedSet(SortedSet s, Class type)
  void         Collections.copy(List dest, List src)                     Copies src list elements to dest list
  boolean      Collections.disjoint(Collection c1, Collection c2)        true if c1/c2 have no elements in common.

  Enumeration  Collections.enumeration(Collection c)                     Returns an enumeration over the specified collection.

  void         Collections.fill(List list, T obj)                        Replaces all of the elements of the specified list with the specified element.
  int          Collections.frequency(Collection c, Object o)             Returns the number of elements in the specified collection equal to the specified object.
  int          Collections.indexOfSubList(List list, List sublist)       -1 if not found.
  int          Collections.lastIndexOfSubList(List list, List sublist)   -1 if not found.
  ArrayList    Collections.list(Enumeration e)                           Enum to array list in the order they are returned by input enum.
  T            Collections.max/min(Collection coll (, Comparator comp))  max/min element of collection, according to comparator order.(Def to natural ordering)
  List         Collections.nCopies(int nCopies, T inputObject)
  Set          Collections.newSetFromMap(Map map)
  boolean      Collections.replaceAll(List list, T oldVal, T newVal)     ← NOTE: Replaces in list
  void         Collections.reverse(List list)
  Comparator   Collections.reverseOrder()                                Return <<Comparable>> Comparator imposing reverse natural ordering.
  Comparator   Collections.reverseOrder(Comparator cmp)                  Returns               comparator imposing reverse ordering of input comparator.
  void         Collections.rotate(List list, int distance)               Rotates elements in list.
  void         Collections.shuffle(List list (, Random rnd) )            Randomly permutes elements using source of randomness (or def. src of randomness).
  void         Collections.sort(List list (, Comparator c))              Sorts in comparator (def. to natural) order.
  void         Collections.swap(List list, int pos1, int pos2)           Swap elemnts in pos 1 and 2.
  Collection   Collections.synchronizedCollection(Collection c)          Returns thread-safe collection       [qa]
  List         Collections.synchronizedList(List list)                   Returns thread-safe list             [qa]
  Map          Collections.synchronizedMap(Map m)                        Returns thread-safe map              [qa]
  Set          Collections.synchronizedSet(Set s)                        Returns thread-safe set              [qa]
  SortedMap    Collections.synchronizedSortedMap(SortedMap m)            Returns thread-safe sorted map       [qa]
  SortedSet    Collections.synchronizedSortedSet(SortedSet s)            Returns thread-safe sorted set       [qa]
  Collection   Collections.unmodifiableCollection(Collection c)          Returns inmutable view               [qa]
  List         Collections.unmodifiableList(List list)                   Returns inmutable view               [qa]
  Map          Collections.unmodifiableMap(Map m)                        Returns inmutable view               [qa]
  Set          Collections.unmodifiableSet(Set s)                        Returns inmutable view               [qa]
  SortedMap    Collections.unmodifiableSortedMap(SortedMap m)            Returns inmutable view               [qa]
  SortedSet    Collections.unmodifiableSortedSet(SortedSet s)            Returns inmutable view               [qa]
  ```
[[data_structures.collections}]]

## Fastutil @[http://fastutil.di.unimi.it/] [[{data_structures,performance.101,PM.backlog]]
* Fast and compact type-specific collections for Java
  Great default choice for collections of primitive types,
  like int or long. Also handles big collections with more than 2
  31 elements well.

* [Eclipse Collections](https://www.eclipse.org/collections/)
  (Originated from Goldman Sachs [gs-collection](https://github.com/goldmansachs/gs-collections)
  Features you want with the collections you need Previously known as 
  gs-collections, this library includes almost any collection you might 
  need: primitive type collections, multimaps, bidirectional maps and 
  so on.
[[}]]

[[{data_structures.iterator,data_structures.101,02_doc_has.comparative]]
## `<<Enumeration>>`(1.0) vs `<<Iterator>>`(1.7)
@[https://stackoverflow.com/questions/948194/difference-between-java-enumeration-and-iterator]
* both interfaces will give successive elements
* Iterators allow the caller to remove elements from the underlying 
  collection during the iteration with well-defined semantics.
  (additional remove method)
* Iterators Method names have been improved.
* Iterators are fail-fast:
  * If thread A changes the collection, while thread B is traversing 
    it, the iterator implementation will try to throw a 
    ConcurrentModificationException (best effort since it can not always 
    be guaranteed)
  * The fail-fast behavior of iterators can be used only to
    detect bugs sin the best effort doesn't warrant its trigger.
  * newer 'concurrent' collections will never throw it.
    Reading thread B will traverse the collection "snapshot" at
    the start of read.

* Iterator should be preferred over Enumeration taking the place of 
  Enumeration in collections framework
  ```
  Enumeration     │ Iterator
  ────────────────┼────────────
  hasMoreElement()│ hasNext()
  nextElement()   │ next()
                  │ remove() ← optional: not implemented in many classes
  ```
[[data_structures.iterator}]]
[[data_structures.101}]]

[[ $collections }]]

# Non-Classified / TODO [[{ $non_classified]]

[[{async/reactive.javalite,PM.low_code,messaging.jms,messaging.activemq,architecture.batch,architecture.distributed]]
## JavaLite lightweight async.job processing 

* Use Cases:
  * website that needs to run batch process in background
  * We receive batch of inputs to be processed in a "best-effort"
    but replay as-soon-as-possible to client that we have the
    input batch ready for processing.
* easy-to-use "Wrapper" on top o Apache Artemis (ActiveMQ "next Gen")
  the Async adds an abstraction layer based on a Command Pattern,
  which makes it trivial to add asynchronous processing.
* Embedded broker instance with reasonable defaults
* Ex:
  ```
  Async async = new Async(            ←  CREATE one or more Queues
            "/opt/project1",          ← place to store persistent messages
            false,
            new QueueConfig(
               "MESSAGES_QUEUE",      ← queue 1 name
               new CommandListener(),
               5)                     ← number of listeners(threads) to
            ,
            new QueueConfig(
                  "ERRORS_QUEUE",     ← queue 2 name (No limit in the number o
                  new ErrorListener(),                queues)
               5)
            );
        async .start();

   public class HelloCommand             ←  Create a command
       extends Command {
       private String message;

       public HelloCommand(String message) {
         this.message = message;
       }

       public HelloCommand() {}          ←   necessary (forbid finals)

       @Override
       public void execute() {
           System.out.println(message);
       }
   }


   for(int i = 0; i < 100; i++){         ←  sending commands async
      async.send(
        "MESSAGES_QUEUE",
        new HelloCommand("Hello Number "+ i));
   }
   Output will be similar to
   → Hello Number 0
   → Hello Number 1
   → ...

   List<Command> topCommands             ←  Peek (vs Consume) 3 "top"
      =  async .getTopCommands(            commands from "ERROR_QUEUE"
           3, "ERROR_QUEUE");
  ```
* Commands can be read and processed synchronously (one-by-one)
  from an individual queue one at the time without a listener.<br/>
  Ex:   you do not want to process errors automatically . To do so:
  ```
  ErrorCommand errorCommand =           ←  Consume message
       (ErrorCommand)receiveCommand("ERROR_QUEUE");
  ... // Process manually
  ```

* Text vs Binary messages
  * To be compatible with JMS the  communication protocol is limited to
    ```
    - javax.jms.TextMessage   ← Default mode.
    - javax.jms.BytesMessage  ← async.setBinaryMode(true);
    ```
    In both cases, the   serialization of a command is first done to XML
    with the use of XStream.
  * If a given command has transient field that must NOT  be serialized,
    use the field annotation @XStreamOmitField to ignore it.
  *  WARN:  Do not switch from mode to mode while having persistent
            messages stored in your queues.
* Commands with DB access
  * If queue processing requires a database connetion, DBCommandListener
    can be used:
    ```
    Async async = new Async(filePath, false, new
        QueueConfig("MESSAGES_QUEUE", new
        DBCommandListener(                 ← If JNDI connection is setup, the
         "java:comp/env/jdbc/conn01"), 5)    listener will find and open it
    );                                        Use your tomcat/Jboss/... container
    ```

* (Artemis) Config API: For complex app configuration, the underlying Artemis API
  can be used:
  ```
    org.apache.activemq.artemis.core.config.Condifuration
       artemisCOnfig = async.getConfig();
  ```
* See also filequeue. It's faster but doesn't support Queue to DDBBs.
[[async/reactive.javalite}]]

[[{async/reactive.FileQueue,PM.low_code,messaging.jms,messaging.activemq,scalability.101]]
## FileQueue  
@[https://github.com/stimulussoft/filequeue]
* KISS alternative using MVStore
@[http://www.h2database.com/html/mvstore.html]
* All producers and consumers run within a JVM.
* H2 MVStore DB used for storage.
* Queue items are   POJOs serialized into Json using jackson .
* faster than JavaLite due to performance shortcut :
  *  File Queue will transfer queued items directly to consumers
     without hitting the database provided there are consumers
     available, otherwise, message will be persisted
*  Doesn't support persistence to JNDI DDBB
* fixed and exponential back-off retry is present.

* PRE-SETUP :
  * maven/gradle package dependency:
    `com.stimulussoft:filequeue:1.1.4`

* Usage:
  * Implement POJO extending FileQueueItem
  * Implement consume(FileQueueItem) on <<Consumer>> to process items
  * Instantiate a FileQueue object and call config() to configure
  * Call startQueue() to start the queue
  * Call stopQueue() to stop the queue processing
  * Call FileQueue.destroy() to shutdown all static threads (optional)

* Queue Ussage example:
  ```
  FileQueue queue = FileQueue.fileQueue();
  FileQueue.Config config = FileQueue.
      config(
        queueName,
        queuePath,
        TestFileQueueItem.class,
        new TestConsumer()
      )
      .maxQueueSize(MAXQUEUESIZE)           // ← queueItem will block until an slot becomes
                                                 available or ExceptionTimeout thrown
      .retryDelayAlgorithm(
         QueueProcessor.RetryDelayAlgorithm.EXPONENTIAL)
      .retryDelay(RETRYDELAY)
      .maxRetryDelay(MAXRETRYDELAY)
      .maxRetries(0);                       // ← Infinite retries
      .persistRetryDelay(                   // ← delay between DDBB scans.
         PERSISTENTRETRYDELAY);
  queue.startQueue(config);                 // ← Start queue
  for (int i = 0; i < ROUNDS; i++)
    queue.queueItem(                        // ← Submit items
      new TestFileQueueItem(i)
    );
  queue.stopQueue();                        // ← stopQueue
  ```
* Consumer example implementation:
  ```
  static class TestConsumer implements Consumer<FileQueueItem> {
    public TestConsumer() { }

    @Override
    public Result consume(FileQueueItem item)
    throws InterruptedException {
                    try {
    TestFileQueueItem retryFileQueueItem =
        (TestFileQueueItem) item;
    if (retryFileQueueItem.getTryCount() == RETRIES )
        return Result.SUCCESS;
    return Result.FAIL_REQUEUE;
                    } catch (Exception e) {
    logger.error(e.getMessage(), e);
    return Result.FAIL_NOQUEUE;
                    }
    }
  }
  ```
* FileQueueItem implementation:
  ```
  import com.stimulussoft.filequeue.*;

  static class TestFileQueueItem extends FileQueueItem {
    Integer id;
    public TestFileQueueItem() { super(); };
    private TestFileQueueItem(Integer id) {
        this.id = id;
    }
    @Override
    public String toString() { return String.valueOf(id); }
    public Integer getId() { return id; }
    public void setId(Integer id) { this.id = id; }
  }
  ```
### File Caching:
If there is the need to cache a file to disk or perform resource
availability checks prior to items being placed on the queue,
implement availableSlot() on the QueueCallback interface. This method
is called as soon as a slot becomes available, just before the item
is place on the queue. It may be used to cache a file to disk, or
perform resource availability pre-checks (e.g. disk space check).

[[async/reactive.FileQueue}]]

[[{enterprise_patterns,qa.error_control,architecture.distributed,PM.WiP]]
## FailSafe 

@[https://github.com/failsafe-lib/failsafe]
* Fault tolerance and resilience patterns for the JVM
* lightweight, zero-dependency library for handling failures in Java 8+.
* It works by wrapping executable logic with one or more resilience policies,
  which can be combined and composed as needed.
* Current policies include:
  ```
  Retry
  CircuitBreaker
  RateLimiter
  Timeout
  Fallback
  ```
* Current features include:
  ```
  Async Execution
  Event Listeners
  Execution Context
  Execution Cancellation
  Standalone Execution
  Strong Typing
  Extension Points
  ```
[[enterprise_patterns}]]

## JDepend [[{qa.reporting,qa.versioning,security.auditing,DevOps.101]]
@[https://github.com/clarkware/jdepend]
@[https://htmlpreview.github.io/?https://github.com/clarkware/jdepend/blob/master/docs/JDepend.html]
* JDepend traverses Java class and source file directories and 
   generates design-quality-metrics for each Java package in terms of 
  its extensibility, reusability, and maintainability to effectively 
  manage and control package dependencies.
[[}]]

## JSON-P /JSR-374 [[{persistence.JSON,security.TLS/X509,security.aaa]]
* TODO: Patterns of JSON Matching
  Streaming based, binding based, expression based.
@[https://dzone.com/articles/patterns-of-reading-json?edition=598293]
* REF:JSON processing public review
  @[https://www.infoq.com/news/2017/03/json-processing-public-review]

* JSR 374, API for JSON Processing (JSON-P) version 1.1.
  * Java 8 streams and lambdas alternative to Gson and Jackson.
  * expected to be included in J2EE 8 .
  * compatible with JSON IETF standards.
  * It includes support for:
    * JSON Pointer
    * JSON Patch
    * JSON Merge Patch
    * Query and transformation operations
  * Designed to parse/generate/query standard JSON documents.
[[}]]

## JSON-B vs GSON vs ... [[{persistence.JSON,performance,io]]
* https://www.reddit.com/r/javahelp/comments/bx6ded/jsonb_vs_jackson/
   ... I've had issues with nested types (example: List<X>) when
  decoding Json with most libraries (see usage examples [here](https://www.baeldung.com/java-json),
  since they request a class as a parameter, which would be a raw type (in
  this example, List.class).<br/>
  Gson offers a helper class called TypeToken to circumvent this through
  the usage of type instead of class (ex: https://www.baeldung.com/gson-json-to-map).
  It's very complicated but at the same time very elegant.<br/>
  I don't know if other Json libraries offer something equivalent, I
  found Google's code to be concise and easy to understand so I didn't
  edit: I did a quick search about Json-B (never really read much about
  it before) and it doesn't seem to provide any way to handle the
  scenario described above. You would still need a library at least to
  obtain the type.
[[}]]

## JSON-B / JSR-367 [[{persistence.json,io,]]
  @[https://itsallbinary.com/jackson-vs-gson-vs-json-b-vs-json-p-vs-org-json-vs-jsonpath-java-json-libraries-features-comparison/]
- B stands for Ojbect binding
  - Standard binding layer for converting
    Java objects to/from JSON messages, defining a default mapping algorithm
    for converting existing Java classes to JSON, while enabling developers
    to customize it through annotations.

- Real World REST APIT Example:
  ```
  package com.mycomp.project1;

  import java.io.BufferedReader;
  import java.io.DataOutputStream;
  import java.io.IOException;
  import java.io.InputStreamReader;
  import java.net.HttpURLConnection;
  import java.net.URL;
  import java.security.KeyManagementException;
  import java.security.NoSuchAlgorithmException;

  import javax.net.ssl.HttpsURLConnection;
  import javax.net.ssl.SSLContext;
  import javax.net.ssl.SSLSocketFactory;
  import javax.net.ssl.HostnameVerifier;
  import javax.net.ssl.SSLSession;
  import javax.net.ssl.TrustManager;
  import javax.net.ssl.X509TrustManager;                    [security.aaa]
  import java.security.cert.X509Certificate;

  import org.json.JSONObject; ←··············· https://github.com/stleary/JSON-java/
                                               - reference implementation demonstrating:
                                                 - how to parse    JSON docs to Java objects
                                                 - how to generate JSON documents from the Java objects.
                                               - Project goals include:
                                                 - Adherence to the JSON spec.
                                                 - No external dependencies
                                                 - Fast execution and low memory footprint
                                               - It can also convert to/from:
                                                 JSON, XML, HTTP headers, Cookies, Comma Delimited Text
                                                 (org.json.CDT or CSV).
  import java.util.Date;
  import java.util.Scanner;

  public class TestAPI<JSONArray> {
      static String userpass = "operator1:ecllqy";
      private static SSLSocketFactory sslSocketFactory = null;

      private JSONObject sendPost(String url, String post_body, String token) throws Exception
      {
          URL obj = new URL(url);
          String basicAuth = "Basic " +
              javax.xml.bind.DatatypeConverter.printBase64Binary(userpass.getBytes());

          HttpsURLConnection con = (HttpsURLConnection) obj.openConnection();

          setAcceptAllVerifier((HttpsURLConnection)con); // TODO: WARN Add certificate validation.

          con.setRequestMethod("POST"); //add request header
          con.setRequestProperty("Content-Type", "application/json");
          con.setRequestProperty("Cache-Control", "no-cache");
          if (token.isEmpty()) { con.setRequestProperty("Authorization", basicAuth);
          } else               { con.setRequestProperty("Authorization", "Bearer "+token);
          }
          con.setDoOutput(true);
          DataOutputStream wr = new DataOutputStream(con.getOutputStream());
          wr.writeBytes(post_body);
          wr.flush();
          wr.close();
          int responseCode = con.getResponseCode();

          BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
          StringBuffer response = new StringBuffer();
          String inputLine; while ((inputLine = in.readLine()) != null) { response.append(inputLine); }
          in.close();
          return new JSONObject(response.toString()); //String myJSONStr
      }


      /*                                                                              */
      /**
       * Overrides the SSL TrustManager and HostnameVerifier to allow
       * all certs and hostnames.
       * WARNING: This should only be used for testing, or in a "safe" (i.e. firewalled)
       * environment.
       *
       * @throws NoSuchAlgorithmException
       * @throws KeyManagementException
       */
      protected static void setAcceptAllVerifier(HttpsURLConnection connection) throws NoSuchAlgorithmException, KeyManagementException {
          // Create the socket factory.
          // Reusing the same socket factory allows sockets to be
          // reused, supporting persistent connections.
          if( null == sslSocketFactory) {
              SSLContext sc = SSLContext.getInstance("SSL");
              sc.init(null, ALL_TRUSTING_TRUST_MANAGER, new java.security.SecureRandom());
              sslSocketFactory = sc.getSocketFactory();
          }

          connection.setSSLSocketFactory(sslSocketFactory);

          // Since we may be using a cert with a different name, we need to ignore
          // the hostname as well.
          connection.setHostnameVerifier(ALL_TRUSTING_HOSTNAME_VERIFIER);
      }

      private static final TrustManager[] ALL_TRUSTING_TRUST_MANAGER = new TrustManager[] {
          new X509TrustManager() {
              public X509Certificate[] getAcceptedIssuers() {
                  return null;
              }
              public void checkClientTrusted(X509Certificate[] certs, String authType) {}
              public void checkServerTrusted(X509Certificate[] certs, String authType) {}
          }
      };

      private static final HostnameVerifier ALL_TRUSTING_HOSTNAME_VERIFIER  = new HostnameVerifier() {
          public boolean verify(String hostname, SSLSession session) {
              return true;
          }
      };
  }
  ```
[[persistence.json}]]

## JPMS Modules (1.9+) [[{java_lang.modules,java_lang.101]]
* JPMS == Java Platform Module System
* Related:
  - JEP 200: THE MODULAR JDK
  - JEP 201: MODULAR SOURCE CODE
  - JEP 220: MODULAR RUN-TIME IMAGES
  - JEP 260: ENCAPSULATE MOST INTERNAL APIS
  - JEP 261: MODULE SYSTEM
  - JEP 275: MODULAR JAVA APPLICATION PACKAGING
  - JEP 282: JLINK: THE JAVA LINKER
  - JSR 376: JAVA PLATFORM MODULE SYSTEM
  - JSR 379: JAVA SE 9
@[https://www.oracle.com/corporate/features/understanding-java-9-modules.html]
By Paul Deitel

### module
A module is:
* a higher level of aggregation above packages.
* a uniquely named, reusable group of related packages and resources.

* module descriptor: (compiled version of module-info.java )
  ```
  /module-info.class  ( @ module root's folder)
  -------------------
  · name
  · dependencies (modules)
  · packages explicitly marked as available to other modules
    (by default  implicitly unavailable / strong encapsulation)
  · services offered
  · services consumed
  · module list allowed reflection
  ```

* Rules:
  * Each module must explicitly state its dependencies.
  * provides explicit mechanism to declare dependencies between
    modules in a manner that’s recognized both at   compile time
    and   execution time .

* The java platform is now modularized into ~ 95 modules
  ```
  $ java --list-modules  ←  List modules in SE, JDK, Oracle, ...
  ( custom runtimes can be created )
  ```

* Module Declarations
  ```
  $ cat module-info.java
  module java.desktop { ← body can be empty
     requires modulename; ← 'static' flag: required just at compile time.
     requires transitive java.xml; ← if a java.desktop method returns a type
                                     from the java.xml module, code using
                                     (reading) java.desktop become dependent
                                     on java.xml. Without 'transitive' compilation
                                     will fail.
     exports ...    ← declares module’s packages whose public types
                      (and their nested public and protected types)
                      are accessible to code in all other modules.
     exports to ... ← fine grained export
     uses           ← specifies a service used by this module
                      (making our module a service consumer).
                      → modules implements/extends the interface/abstract class

     provides...with ← specifies that a module provides a service implementation

     open 'package'  ← Specifies object introspection scope
     opens
     opens ... to

     transitive
  }
  ```

* Troubleshooting:
 ```
 --release $version compiler flag:                                            [[{DEVOPS.101.troubleshooting]]
  @[https://stackoverflow.com/questions/43102787/what-is-the-release-flag-in-the-java-9-compiler/]
   """ ... --release X is more than just a shortcut to -source X
       -target X because -source and -target are not sufficient to safely
       compile to an older release. You also need to set a -bootclasspath
       flag which must correspond to the older release (and this flag is
       often forgotten). So, in Java 9 they made a single --release flag
       which is a replacement for three flags: -source, -target and
       -bootclasspath.                                                        [[}]]
 ```
[[java_lang.modules}]]

## Resilience4j [[{architecture.distributed,java_lang.functional,qa.error_control,PM.TODO]]
* higher-order functions (decorators) to enhance any functional interface,
  lambda expression or method reference with a Circuit Breaker, Rate
  Limiter, Retry or Bulkhead.
* more than one decorator on any functional interface, lambda or method ref
  can be stacked.
[[}]]

## SummaryStatistics(1.8+) [[{java_lang.functional,PM.TODO]]
- Three of the new classes introduced in JDK 8 are 
  DoubleSummaryStatistics, IntSummaryStatistics, 
  andLongSummaryStatistics of the java.util package. These classes make 
  quick and easy work of calculating total number of elements, minimum 
  value of elements, maximum value of elements, average value of 
  elements, and the sum of elements in a collection of doubles, 
  integers, or longs. Each class's class-level Javadoc documentation 
  begins with the same single sentence that succinctly articulates 
  this, describing each as "A state object for collecting statistics 
  such as count, min, max, sum, and average."
[[}]]

## BascomTask Lib: 'CompletableFutures' Complement  [[{async/reactive.101,PM.TODO.NOW]]
* [REF](https://dzone.com/articles/lightweight-parallel-tasks-in-java-microservices)
* lightweight task-orchestration library providing frictionless thread-level
  parallelization.
* by design, use is very close to pure Java code, including use of
  CompletableFutures (where they can be used effectively), but without
  having to rely solely on them for task-level organization of a
  codebase.
* BascomTask aims to compliment rather than replace 'CompletableFutures'
  and freely integrates with them.
[[}]]

## Amdahl's law [[{concurrency.101,PM.TODO]]
- Amdahl's law provides a formula to compute the theoretical maximum
  speed up by providing multiple processors to an application like:
  ```
  S(n) = 1 / (B + (1-B)/n)  : n == number-of-processors
                              B == fraction of non-paralellizable code

  S(n) ~  1 / B when big 'n' <·· maximum speedup is reciprocal to
                                 fraction of non-paralellizable code
  ```
[[}]]

## Sub-processes [[{concurrency.101,PM.TODO,PM.low_cost]]
* Concurrency can be greately simplified by using OS processes
  (vs in-process threads).  This automatically avoid many/most of the problems
  with thread concurrency.
* When context switching among OS process is not an issue multi-process
  architecture is a safer, easier and faster (fewer locks) approach.
  Note for example how all modern web browser have switched to a
  multi-process (one per-tab) approach to gain estability and safety.
* Invoquing sub-processes from java, curl example:
  https://www.baeldung.com/java-curl
[[}]]

## jLine [[{qa.UX,java_lang.repl,java_lang.101,PM.low_code,PM.TODO]]
* [Github](https://github.com/jline/jline3)
* jLine: GNU/readline alike library for JAVA.
- Builtin support for console variables, scripts, custom pipes, widgets and object printing.
- Autosuggestions
- Language REPL Support
[[}]]

## PicoCli UX Cli apps [[{qa.UX,]]
@[https://github.com/remkop/picocli]
@[https://picocli.info/]
  Picocli is a one-file framework for creating Java user-friendly
 (autocompletion, subcommands, ...)  command line  , GraalVM-enabled
  applications with almost zero code. It supports a variety of command
  line syntax styles including POSIX, GNU, MS-DOS and more. It
  generates highly customizable usage help messages that use ANSI
  colors and styles to contrast important elements and reduce the
  cognitive load on the user.
  - 1 source file so apps can include as source and avoid adding a dependency!!!
[[}]]

### Clig.Dev: Command Line Interface Guidelines [[{qa.UX]]
@[https://clig.dev/]
* An open-source guide to help you write better command-line
  programs, taking traditional UNIX principles and updating them for
  the modern day.
  · ...
  · Use a command-line argument parsing library where you can. Either
     your language’s built-in one, or a good third-party one. They will
     normally handle arguments, flag parsing, help text, and even spelling
     suggestions in a sensible way.

* Note: Alternatives in other languajes:
  · Go: Cobra, cli
  · Node: oclif
  · Python: Click, Typer
  · Ruby: TTY
  · Rust: clap, structopt
  · PHP: console
[[}]]
[[}]]

## Snippets Collection [[{]]
https://github.com/topics/snippets-collection
[[}]]

[[PM.TODO}]]