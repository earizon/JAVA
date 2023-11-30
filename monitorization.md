# Monitorization [[{monitoring]]
#[micrometer_summary]
## Micrometer Metrics [[{monitoring.micrometer,architecture.distributed,monitoring.distributed,PM.low_code,PM.WiP]]
- simple facade to instrumentation clients for
- instrument JVM-based apps without vendor lock-in.
  (Think SLF4J, but for application metrics! supporting AppOptics,
   Atlas, Datadog, Dynatrace, Elastic, Ganglia, Graphite,
   Influx,Instana, JMX (hierarchical mapping), KairosDB, New Relic,
   Prometheus, SignalFx, Stackdriver, StatsD, Wavefront,)

- Recorded metrics are intended to be used to
  observe/alert/react to current/recent operational state.

  out-of-the-box instrumentation provided by Micrometer
  - JVM Metrics on classloaders, memory, garbage collection,
    threads, etc.
  - Spring Boot 2.0.0.M5+: Micrometer used as instrumentation library powering
    the delivery of application metrics from Spring.
  @[https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-metrics]
    2 simple steps setup: [low_code]
    - Declare maven dependency.
    - Add config. to application.yml

  @[https://micrometer.io/docs/ref/spring/1.5] (legacy support)
drop─down support for Spring Boot 1.5.x.
  - Cache instrumentation for most popular caching frameworks.
  - OkHttpClient Instrumentation

Guides: [TODO]
-@[https://micrometer.io/docs/guide/consoleReporter]
-@[https://micrometer.io/docs/guide/httpSenderResilience4jRetry]
-@[https://micrometer.io/docs/guide/customMeterRegistry]
[[}]]

## JVM Implementations [[{jvm,PM.WiP]]
@[https://blog.joda.org/2018/09/time-to-look-beyond-oracles-jdk.html]

In practical terms, there is only one set of source code for the JDK.

- Anyone can take that source code,build and publish it.
- The certification process ensures that the build is valid.
- Certification run by the Java Community Process, which provides a
  Technology Compatibility Kit (TCK, sometimes referred to as the JCK).
  If build passes the TCK then it is described as "Java SE compatible".
  Note: Built can NOT be referred to as "Java SE" without paying a commercial
        license from Oracle.
        Ex: AdoptOpenJDK passing TCK are "Java SE compatible" (vs "Java SE").
  -  WARN : certification is currently on a trust-basis: results are
            not submitted to the JCP/Oracle for checking, neither can
            be made public.

- Existing builds include:
  - Oracle Java
  - OpenJ9      (Eclipse "IBM")
    └ https://en.wikipedia.org/wiki/OpenJ9
    └ Pre-built binaries available at AdoptOpenJDK
    └ Compared to Oracle's HotSpot VM, i touts higher
      start-up performance and lower memory consumption
      at a similar overall throughput.
    └ JIT with all optimization levels.
  - OpenJDK
  - GraalVM
  - Bellsoft Liberica:
    - $free TCK verified OpenJDK distribution for x86, ARM32 and ARM64.
  - Azul Systems
  - Sap Machine
    JDK for Java 10 and later under the GPL+CE license.
    They also have a commercial closed-source JVM
  - Amazon Corretto:
    zero-cost build of OpenJDK with long-term support that passes the
    TCK. It is under the standard GPL+CE license of all OpenJDK builds.
    Amazon will be adding their own patches and running Corretto on AWS
[[}]]






## Jabba JDK Version Mng [[{]]
@[https://github.com/shyiko/jabba]
* pain-free JDK installing on Linux x86/x86_64/ARMv7+, macOS, Windows x86_64.
* Support for:
  · Oracle JDK (latest-version only)
  · Oracle Server JRE (latest-version only),
  · Adopt OpenJDK (jabba >=0.8.0 is required)
        Hotspot
        Eclipse OpenJ9
  · Zulu OpenJDK (jabba >=0.3.0 is required)
  · IBM SDK, Java Technology Edition (jabba >=0.6.0 is required)
  · GraalVM CE
  · OpenJDK
  · OpenJDK Reference Implementation
  · OpenJDK with Shenandoah GC (jabba >=0.10.0 is required)
  · Liberica JDK
  · Amazon Corretto

* INSTALL/UPGRADE:
$ $ curl -sL https://github.com/shyiko/jabba/raw/master/install.sh | bash
$ $. ~/.jabba/jabba.sh

   Use $ ... | bash --skip-rc   to avoid modifying common rc files.
   In that case add next lines to .bashrc / ...
 + export JABBA_VERSION=...
 + [ -s "$JABBA_HOME/jabba.sh" ] && source "$JABBA_HOME/jabba.sh"

* USAGE
$ $ jabba ls-remote                   ←  list available JDK's
$ $ jabba ls-remote zulu@~1.8.60      ← Narrow results
$ $ jabba ls-remote --latest=minor\   ← semver allowed
$        "*@>=1.6.45 <1.9"

$ $ jabba ls                          ← list all installed JDK's
$ $ jabba use adopt@1.8
$ $ jabba use zulu@~1.6.97
$ $ echo "1.8" > .jabbarc             ← switch to JDK in .jabbarc
                                        It must be a valid YAML file.
                                        'jdk: 1.8' or simply '1.8' are valid
$ $ jabba alias default 1.8           ← set def.java ver. on shell (since 0.2.0)
                                        automatically used on new terminals

$ $ jabba install 1.15.0                      ← install Oracle JDK
$ $ jabba install sjre@1.8                    ← install Oracle Server JRE
$ $ jabba install adopt@1.8-0                 ← install Adopt OpenJDK (Hotspot)
$ $ jabba install adopt-openj9@1.9-0          ← install Adopt OpenJDK (Eclipse OpenJ9)
$ $ jabba install zulu@1.8                    ← install Zulu OpenJDK
$ $ jabba install ibm@1.8                     ← install IBM SDK, Java Technology Edition
$ $ jabba install graalvm@1.0-0               ← install GraalVM CE
$ $ jabba install openjdk@1.10-0              ← install OpenJDK
$ $ jabba install openjdk-shenandoah@1.10-0   ← install OpenJDK with Shenandoah GC
          └──┬──┘
   everything is installed under ~/.jabba. Removing this directory clean install
$ $ jabba uninstall zulu@1.6.77               ← uninstall JDK
$ $ jabba link system@1.8.72 \                ← link system JDK
$   /usr/lib/jvm/jdk1.8.0_72.jdk

* To modify JDK system-wide:
$ $ sudo update-alternatives --install /usr/bin/java java ${JAVA_HOME%*/}/bin/java 20000
$ $ sudo update-alternatives --install /usr/bin/javac javac ${JAVA_HOME%*/}/bin/javac 20000
* To swith among GLOBAL JDK system-wide:
$ $ sudo update-alternatives --config java
[[}]]


[[monitoring}]]


