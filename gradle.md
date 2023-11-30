## Apropos:
  Content is versioned in git.  commits, issues and pull-requests welcome!
@[https://www.github.com/earizon/gradle.md]

[[{ $devops.gradle]]

# Gradle Summary [[{devops.gradle.101,devops.gradle.troubleshooting]]
* TLDR
  ```
  # SETUP TASKS {{{ ───────────────────────────────────────────────────────
  $ gradle init        \   <- Initializes a new a new Gradle build
    --dsl  kotlin      \   <·· kotlin | groovy 
    --package ...      \   <·· eg:  my.comp.package
    --project-name ... \   <·· eg: projectA
    --type  ...            <·· := basic | pom 
                               |XXX-YYY  
                                XXX := java|kotlin|scala|cpp|swift|...
                                YYY := application|library|gradle-plugin
  --test-framework ... \   <·· := junit|junit-jupiter|kotlintest|...
                              Further details:
                              $ gradle -q help --task init



  (Output layout will be similar to ..)
  ├─ gradle/               <·· Gradle Wrapper: Gradle versioned "snapshot" JAR for project replacing
  │  │                         "random" OS-wide gradle install.
  │  └─ wrapper/               (For existing projects with no wrapper it can be generated with `$ gradle wrapper`)
  │     ├─ ..gradle-wrapper.jar      <·· minimal code to download a given gradle version
  │     └─ gradle-wrapper.properties <·· download repositories, ...
  │
  ├─ .gradle               <·· Project-specific cache directory
  │  ├─ 8.4/                   <·· given distribution downloaded by gradle wrapper
  │  ├─ ...
  │ 
  ├─ gradlew               <·· macOS/Linux script to launch  Gradle Wrapper.
  │                            [[{KEYPOINT]]  once in place, replace OS system-wide gradle
  │                            (with random version) with `$ ./gradlew build` to ensure 
  │                            **reproducible builds**  [[}]]
  │ 
  │                                           Project's settings file (subprojects list,...)
  │                                             (See gradle core model in next section)
  ├─  ┌─ settings.gradle.kts ────────┐ <·· │settings.gradle│ 1 <···>  1+│Project│1 <··> 1│ProjectDescriptor│
  │   │ rootProject.name = "demo"    │
  │   │ include (                    │
  │   │  "sharedlib1",               │
  │   │  "app1",                     │
  │   │  "services:service01" )      │
  │   └──────────────────────────────┘
  │  
  ├─ sharedlib1/           <·· Project 1.Source code and build configuration for the Java lib.
  │  ...
  ├─ app1/                 <·· Project 2.Source code and build configuration for the Java app.
  │  │
  │  ├─ ┌─ build.gradle.kts ────  <·· Real "Makefile" like with build dependency tree
  │  ·  │ 
  │  ·  │ plugins {
  │  ·  │     id 'java'        // <- Apply java plugin: build Java    support
  │  ·  │     id 'application' // <- Apply app. plugin: build cli app support
  │  ·  │ }
  │  ·  │ 
  │  ·  │ repositories {
  │  ·  │     jcenter()        // <·· Default repository. 
  │  ·  │     ...                     
  │  ·  │                             
  │  ·  │ }
  │  ·  │                                                                                   
  │  ·  │ application {
  │  ·  │     mainClassName = 'tmp.App'
  │  ·  │ }
  │  ·  │                                                                                   
  │  ·  │ task customTask01 {  // <·· run ··> $ gradle -q customTask01
  │  ·  │     doLast {
  │  ·  │         println 'Hello Baeldung!'
  │  ·  │     }
  │  ·  │ }
  │  ·  │                                                                                   
  │  ·  │ dependencies {
  │  ·  │     implementation          'com.google.guava:guava:28.0-jre'
  │  ·  │                            ┌─·············   · special form of execution dependency causing
  │  ·  │                         ┌──┴──┐                the other project to be built first and adding
  │  ·  │     implementation      project(':sharedlib1') the jar to the classpath.
  │  ·  │                                                $ gradle :api:compile # build "sharedlib" then "app"
  │  ·  │     testImplementation 'junit:junit:4.12'
  │  ·  │     └──────┬─────────┘
  │  ·  │ }    compileOnly    : used only at compile time (eg. transpiler).
  │  ·  │      implementation : dep. used for compilation and runtime
  │  ·  │                       (most dependencies fall in this category).
  │  ·  │                       (supersedes deprecated 'compile' that failed to distinguish
  │  ·  │                        among deps. impacting the public API of a Java lib)
  │  ·  │      runtimeOnly    : (supersedes runtime), used only at runtime (vs compilation)
  │  ·  │                       (e.g.: JDBC drivers needed only at runtime)
  │  ·  │      api, compileOnlyApi : dependencies required for compiling both
  │  ·  │                       the module and transitive module dependencies
  │  ·  │      (plus testCompileOnly, testImplementation, testRuntimeOnly)
  │  ·  └──────────────────────────────────────────────────────────────
  │  └─ src/
  │     ├ main/java/...        (or main/kotlin , main/scala/)
  │     │ └ resources/
  │     └ test/java/...
  │       └ resources/...
  │  ...                  <·· Git related files (.gitignore,...) 
  ├─ services/
  │  └─ service01/                 
  │     ├─ build.gradle                               
  │     .. ^^^^^^^^^^^^
  │      + dependencies {                           
  │      +   implementation project(':sharedlib1')   <·· has dependency on sharedlib1
  │      +   implementation project(':app1')         <·· has dependency on app1
  │      + }  
  │  More about (multimodule) Subproject Dependencies at 
  │ [https://docs.gradle.org/current/userguide/declaring_dependencies_between_subprojects.html]
  │
  ├─ `gradle.properties` <·· Modify/customize plugins 
  │                          NOTE: It is NOT initialized by default.
  │                          See related section for details.
     [[}]]

  $ git add \                           <···· NEXT) Commit gradle artifacts to git.
    build.gradle gradle* settings.gradle
  $ git commit -m "Adding graddle support"
                                              NEXT) Test pre-setup
  $ ./gradlew \                         <···· Run App::main @App.java,
     -Dskip.tests run \                       <·· Skip tests (optional)
     --args="arg1 arg2"                       <·· CLI arguments for running app
  $ ./gradlew build                     <···· Test build (bundling jar)
  $ ./gradlew build --scan              <···· Publish a build-scan
  ...
  Publishing build scan...
  https://gradle.com/s/5u4w3gxeurtd2    <···· Browse link to explore tasks executed, dependencies
  ^                                           downloaded,...
  Click the link and explore which tasks where executed,
  which dependencies where downloaded and many more details!
  }}} 
  BUILD TASKS {{{ ───────────────────────────────────────────────────────
  $ gradle build           <- Compile package
                              -x {{test}} : Exclude test task:
                              --offline   : offline mode (prevent network access/updates)
  $ gradle clean           <- Clear build directory

  $ gradle assembleRelease <- Build Android Package (APK) in release mode

                         ● HELP TASKS
  $ gradle tasks           <- List main tasks
                              $ gradle tasks --all  <- List all tasks
  $ buildEnvironment       <- show all buildscript dependencies declared in root project 'JAVA'.
  $ components             <- show components produced by root project 'JAVA'.
  $ dependencies           <- show all deps. declared in root project 'JAVA'.
  $ dependencyInsight      <- show insight into a specific dep. in root project 'JAVA'.
  $ dependentComponents    <- show dependent components of components in root project 'JAVA'. [incubating]
  $ help                   <- show a help message.
                              $ gradle help --task $task for detailed info
  $ model                  <- show configuration model of root project 'JAVA'. [incubating]
  $ projects               <- show sub-projects of root project     'JAVA'.
  $ properties             <- show properties of root project       'JAVA'.
  $ tasks                  <- show tasks runnable from root project 'JAVA'.

  }}}
  ```

# GRADLE CORE MODEL  [[{gradle.101.core_model,02_doc_has.diagram.UML.ER]]

 ```
  GRADLE FIXED BUILD PHASES
  1) INITIALIZATION: setup projects included in build
  2) CONFIGURATION : Configures, on every new build, the Directed Acyclic Graph (DAG)
                     of Tasks (Units of work). That is, the set tasks to be executed
                     and the order in which they must be executed.
  3) EXECUTION     : Runs tasks until completion or error.   


   │Java/Android/Kotlin/... Plugin│ 1<······················································
   "feed" gradle with Task-types                                                           ·
   for projects of different nature.                                                       ·
                                                                                           ·
                                                                                           v
    │root-project   │                                                                      N
    │(Settings)     │1<········>1+│Project│1<··>1│ProjectDescriptor│1<··>1+│Build│1<··>0+│Task│1<··>1│Plugin│
         ·                                        *NOTE 1                     ·            ·              ·                  
         ·                                                                    ·            ·              ·                  
  ┌──────┴───────────────────────────────┐    ┌───────────────────────────────┴──────┐     ·              ·
  * always present but `settings.gradle`      * "==" build.gradle                          ·              ·
    is optional for single project builds     * A new Build instance executes build.gradle ·              ·
  * Children projects added in hierarchy        to configure and wire build-tasks and,     ·              ·
    of build deps. with include*(...)           based on their dependencies, create the    ·              ·
  * Place to register build life-cycle          directed acyclic build graph (DAGs)        ·              ·
    (script) hooks                              used to determine tasks run-order.         ·              ·
  * A new gradle build creates a single         - the task code can be defined through:    ·              ·
    "Settings" instance that executes             · plugins                                ·              ·
    this file to further execute itself.          · custom build scripts                   ·              ·
                                                    BEST PATTERN: Delegate complex logic   ·              ·
                                                                  to plugins               ·              ·
                                                                                           ·              ·
  * Task==Unit of work!                             ─┐                                     ·              ·
       Inputs : Optional (values,files/dirs)         │                                     ·              ·
      Actions : Optional (compile, cp, ... )         │                                     ·              ·
      Outputs : Optional (files/dirs)                │                                     ·              ·
    * Task action customization:                     ├─────────────────────────────────────┘              ·
      Task.doLast  {}                                │                                                    ·
      Task.doFirst {}                                │                                                    ·
      Notes: standard-lifecycle-tasks don't even     │                   packaging way to distribute  ···─┘
             have any actions. They simply aggregate │                   standard,community&custom tasks 
             multiple tasks together.               ─┘

  
  NOTE 1:
 │ProjectDescriptor│ instance:
 ┌─ READONLY PROPS ──────────────────────────────┐ 
 │ · gradle        instance reference            │ 
 │ · PluginManager instance reference            │ 
 │ · Settings      instance injected from:       │ 
 │                 1) ${PROJCT}/gradle.properties│ 
 │                 2) ~/.gradle/gradle.properties│ 
 │                                               │
 ├─ READ/WRITE PROPS  ────────────────────────   │
 │ · startParameter: StartParameter (param.set used to invoke this gradle instance)
 │ · buildCache    : BuildCacheConfiguration     │
 │ · extensions    : ExtensionContainer          │
 │ · plugins       : PluginContainer (applied to this object)
 │                    not deprecated but prefer equivalent methods
 │                    in ProjectDescriptor | PluginAware.getPluginManager()
 │ · rootProject   : ProjectDescriptor (of build)│
 │ · rootDir       : File (of project)           │
 │ · settingsDir   : File (of build)             │
 │                                               │
 ├─ Methods: ────────────────────────            │
 │                                               │
 │  Method	               Description           │
 │  ------------------    -----------            │
 │  apply(closure)        Applies 0+ plugins|scripts
 │  apply(options)        Applies plugin|script using options (injected as map)
 │  apply(action)         Applies 0+ plugins|scripts
 │  buildCache(action)    Configures build cache │
 │  findProject(prjDir)   Returns project with given 'prjDir'
 │  findProject(path)     Returns project with given 'path'
 │  include(projectPaths) Adds projects to build (Build Project Dependency Tree)
 │  includeBuild(rootPrj) Includes build at path │
 │  includeBuild(rootPrj, configuration)         │
 │  includeFlat(prjNames) Adds project list to build
 │  project(projectDir)   Returns project with given prj.dir.
 │  project(path)         Returns the project with given path.
 │                                               │
 │ (REF: https://docs.gradle.org/5.6/dsl/org.gradle.api.initialization.Settings.html)
 └───────────────────────────────────────────────┘              

 WARN: [[{02_doc_has.comparative]]
 Gradle’s build phases != Maven’s phases [[}]]

```

## CORE MODEL CUSTOMIZATION [[{]]
* Custom properties can be added to projects and tasks.

* Custom models to represent  concepts (other than tasks, files and dependencies)
  can be created adding the concept of "source-sets" to a build.
  [[{KEYPOINT]]: Appropriate modeling of a build process can greatly improve
  a build's ease of use and its efficiency. [[}]]

* Logic must be delegated to plugins and custom models  [[{qa.BEST_PATTERN]]
  (vs build scripts)                                    [[}]]

## Project Coordinates: 
* Help to univocally identify a project and its versions.
  ```
  'group   :module     :version' <- gradle terminology [[{02_doc_has.comparative]]
  'groupId :artifactId :version' <- maven  terminology [[}]]
  ```
[[}]]

[[gradle.101.core_model}]]


# `gradle.properties` Customizing gradle/project/build/plugin  [[{gradle.properties]]

* [REF](https://dev.to/jmfayard/configuring-gradle-with-gradle-properties-211k)
* `gradle.propeties` is NOT created by default during '$ gradle init ...'
* `$ gradle refreshVersions` can be used to generate an initial one
  with depedencies versions and available updates looking similar to:
  ```
  ┌─ gradle.properties ─────────────────
  │ version.org.jetbrains.kotlin=1.3.31
  │ ##              #  available=1.3.4
  │ ...
  └─────────────────────────────────────
  ```

* Standard java key=value properties file that different plugins and
  graddle components will interpret or ignore in different ways.
* It can be used to:
  ```
  - Customize build/project settings.
  - Customize build/project settings.
  - Customize Gradle components runtime behaviour.
  - Customize Kotlin/Android/Java/... builds.
  ```
* [[{KEY-POINT]]: You should **NOT** add a setting to `gradle.propeties` before
  having read the docs to understand what any (collateral/direct) effect. 
  [[}]]
* Properties can also be defined in build.gradle or as parameters on
  the command line but 'gradle.properties' helps keeping them separate.

## Example ussage:

* Android project build customization using properties:
  ```
  ┌─ gradle.properties ───────
  │ # Common Android settings
  │ android.compileSdkVersion=28
  │ android.applicationId=com.example
  │ android.targetSdkVersion=28
  │ android.minSdkVersion=21
  │ android.versionCode=2
  │ android.versionName=1.2
  └─^─────────────────────────
    └··· Reuse in build.gradle like ···┐
  ┌─ build.gradle ───────────          ·
  │ android {                          v
  │   compileSdkVersion  ┌─────────────┴─────────────────────────┐
  │        rootProject
  │                     .findProperty("android.compileSdkVersion") as Integer
  │   defaultConfig {
  │     applicationId    findProperty("android.applicationId"    )
  │     targetSdkVersion findProperty("android.targetSdkVersion" ) as Integer
  │     minSdkVersion    findProperty("android.minSdkVersion"    ) as Integer
  │     versionName      findProperty("android.versionName"      )
  │     versionCode      findProperty("android.minSdkVersion"    ) as Integer
  │   }
  │ }
  └───────────────────────────
  ```

## 'gradle.properties' to customize Gradle build-runtime settings:

  ```
   org.gradle.caching=true                    [[{gradle.performance]]
   org.gradle.parallel=true                   [[}]]
   org.gradle.caching.debug=false
   org.gradle.configureondemand=false
   org.gradle.daemon.idletimeout= 10800000
   org.gradle.console=auto
   # org.gradle.java.home=(path to JDK home)
   # org.gradle.warning.mode=(all,none,summary)
   # org.gradle.workers.max=(max # of worker processes)
   # org.gradle.priority=(low,normal)
   org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m \
                      -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
   https://docs.gradle.org/current/userguide/build_environment.html#sec:configuring_jvm_memory
  ```

* 'gradle.properties' to customize the Gradle Project object itself:
  ```
   org.gradle.project.rootProject=....
   org.gradle.project.settingsDir=....
   ....
  ```
  [[gradle.properties}]]


[[devops.gradle.101}]]

# What's New [[{01_PM.WHATS_NEW]]

### gradle 8.0 GA release [[{DEVOPS.GRADLE]]
- enhancements to Kotlin DSL (alternative to Groovy DSL).
- upgrades to Kotlin 1.8 and JDK 11.
- improvements in buildSrc builds;
- configuration cache: incubating new feature
- improvements in Java toolchains.
- Shortly after the GA release, a patch release, Gradle 8.0.1,
  provides fixes for these issues:
[[}]]

### Gradle v6:
* @[https://www.infoq.com/news/2020/01/gradle-releases-version-6/]
[[}]]

# Building Java & JVM projects [[{gradle.java_projects]]

## build.gradle example  [[{]]
  ```
  ┌─ build.gradle ─────────────────────────────────────────────────────
  │ import com.github.jk1.license.render.ReportRenderer
  │ import com.github.jk1.license.render.InventoryHtmlReportRenderer
  │ import com.github.jk1.license.filter.DependencyFilter
  │ import com.github.jk1.license.filter.LicenseBundleNormalizer
  │
  │ plugins {
  │ 	id("java-library")                                             <·· Remember: Each plugin provides a set 
  │ 	id("org.springframework.boot")                 version "3.1.2"     of well-defined tasks (units of work)
  │ 	id("io.spring.dependency-management")          version "1.1.2"
  │   id("org.owasp.dependencycheck")                version "8.4.0"
  │   id("com.github.jk1.dependency-license-report") version "2.5"
  │   application
  │   `maven-publish`
  │ }
  │
  │
  │
  │ repositories {
  │   jcenter()        // <·· Default repository. 
  │   mavenLocal()     // <·· ${HOME}/.m2/repository. Neede to use artifact builds
  │                    //     not published in remote (public or private) repository.
  │   maven {
  │       url = uri("https://mycompany.com/repository/my_project/")
  │
  │       if (System.getenv("MAVEN_PASSWORD").isBlank()) {
  │          println("WARNING: Password is blank!")
  │       }
  │
  │       credentials {
  │           username = System.getenv("MAVEN_USERNAME")
  │           password = System.getenv("MAVEN_PASSWORD")
  │       }
  │   }
  │ }
  │
  │ group = "mycomp.myproject"
  │ version = '1.2.1'
  │
  │ java {
  │   sourceCompatibility = '17'
  │   withJavadocJar()
  │   withSourcesJar()
  │   toolchain {
  │     languageVersion.set(JavaLanguageVersion.of(17)) 
  │   }
  │ }
  │ 
  │ compileJava {                    <·· Changing compiler options
  │     options.incremental = true       <·· incremental compilation
  │     options.fork = true              <·· use new JVM process for compiler
  │     options.release = 7              <·· default value:  that of JVM running Gradle
  │     ...                              <·· compiler verbosity, enable/disable debug output 
  │ }                                        in byte code, **location for annotation processors
  │                                          used by compiler**, ...
  │
  │ application {
  │   mainClass.set("mycomp.myprj.MainCli")
  │ }
  │
  │ dependencies {
  │   // LOGGING
  │   implementation("org.slf4j:slf4j-api:2.0.7")
  │   implementation("org.slf4j:slf4j-simple:2.0.7")
  │
  │   // INTERNATL LIBS
  │   implementation(''my.lib:blabla:1.30') {
  │     exclude group: 'org.slf4j', module: 'slf4j-simple'
  │   }
  │
  │   // CLI-SNAPSHOT
  │   implementation("com.github.ajalt.clikt:clikt-jvm:4.2.0")
  │   implementation("com.github.ajalt.clikt:clikt:4.2.0")
  │
  │   // LOMBOK
  │   compileOnly 'org.projectlombok:lombok'
  │   runtimeOnly 'com.oracle.database.jdbc:ojdbc8'
  │   annotationProcessor 'org.projectlombok:lombok'
  │   annotationProcessor 'org.mapstruct:mapstruct-processor:1.5.5.Final'
  │
  │   // TESTING
  │   testImplementation 'org.springframework.boot:spring-boot-starter-test'
  │   testImplementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter-test:3.0.2'
  │ }
  │ 
  │ licenseReport {
  │     renderers = arrayOf<ReportRenderer>(InventoryHtmlReportRenderer("licence-report.html", "MyProject"))
  │     filters = arrayOf<DependencyFilter>(LicenseBundleNormalizer())
  │ }
  │
  │
  │
  │ tasks.register('uberJar', Jar) {                    // <·· Create FAT / Uber jar.
  │   archiveClassifier = 'uber'
  │   from sourceSets.main.output
  │   dependsOn configurations.runtimeClasspath
  │   from {
  │     configurations.runtimeClasspath.findAll {
  │       it.name.endsWith('jar') }
  │     .collect { zipTree(it) }
  │   }
  │ }
  │
  │ tasks.named<CreateStartScripts>("startScripts") {
  │   doLast {
  │     windowsScript.writeText(
  │       windowsScript.readText().replace(
  │         Regex("set CLASSPATH=.*"),
  │          "set CLASSPATH=%APP_HOME%\\\\lib\\\\*")
  │     )
  │   }
  │ }
  │
  │ val fatJar = task("fatJar", type = Jar::class) {
  │   group = "build"
  │   archiveBaseName.set("${project.name}-with-dependencies")
  │ 
  │   manifest {
  │       attributes["Implementation-Title"] = "Gradle Jar Bundling"
  │       attributes["Implementation-Version"] = archiveVersion.get()
  │       attributes["Main-Class"] = "mycomp.myprj.MainCliKt"
  │   }
  │ 
  │   from(configurations.runtimeClasspath.get().map { if (it.isDirectory) it else zipTree(it) })
  │   with(tasks.jar.get() as CopySpec)
  │ }
  │
  │
  │
  │ tasks.withType<Test> {
  │   useJUnitPlatform()
  │ }
  │
  │
  │ jar {                                               // <·· customize MANIFEST.MF in jar/war/ear
  │   manifest {
  │     attributes(
  │       "Implementation-Title": "Gradle",
  │       "Implementation-Version": archiveVersion)
  │   }
  │ }
  │
  │ publishing {
  │   publications {
  │     create<MavenPublication>("mavenJava") {
  │       pom {
  │         name.set("MyCompany MyProject")
  │         description.set(
  │           """
  │           Kotlin/Java library for ...
  │           """.trimIndent()
  │         )
  │         url.set("https://mycompany.com")
  │         licenses {
  │           license {
  │             name.set("Apache 2")
  │             url.set("https://raw.githubusercontent.com/mycomp/mylib/master/LICENSE")
  │           }
  │         }
  │       }
  │       from(components["java"])
  │     }
  │   }
  │ }

  NOTE 1: TASKS PROVIDED by java-library
  Remember a |java/kotlin/...-application/library plugin| provides a set of 
  unit-of-work tasks, and each task can have optional inputs, actions and/or outputs.
  
  TASK           | INPUT                 | ACTION                      | OUTPUT
  ---------------+-----------------------+-----------------------------+-----------
  compileJava    | src/main/java         |                             | build/???
                 | compilation Classpath |                             |
                 | ^^^^^^^^^^^^^^^^^^^^^ |                             |
                 | + sourceSetCompileOnly   (configuration)            |
                 | + sourceSetImplementation(configuration)            |
  ---------------+-----------------------+-----------------------------+-----------
  compileTestJava| src/test/java         |                             | build/???
  JUnit 3/4/5    | source set actually   |                             |
  ---------------+-----------------------+-----------------------------+-----------
  test           |                       | runs test in src/test/java  |
  ---------------+-----------------------+-----------------------------+-----------
  jar            | compiled classes      | package into                |
                 | src/main/resources    | <project>-<version>.jar     |
                 | source set actually   |                             |
  ---------------+-----------------------+-----------------------------+-----------
  javadoc        | src/main/java         |                             | Javadoc
  ---------------+-----------------------+-----------------------------+-----------
  ```

  The Java Library Plugin also integrates the above tasks into the
  standard Base Plugin lifecycle tasks:
  - `jar`  is attached to assemble.
  - `test` is attached to check.
[[}]]


## `SOURCE SETS`:

Source sets tie together:

* source file type/roles (app code, unit test, ...) with location in FS.
* compilation classpath, including any required dependencies
  (via Gradle configurations).
* where the compiled class files are placed.

* The Java Library Plugin creates a compilation task for every source set defined.
* Most language plugins, Java included, automatically create a source
   set called main, which is used for the project's production code.

### Customizing default source-sets

**WARN**: Not recomended except for compatibility with old projects, when
unique classpaths must be used, on generated classes are handled
differently from the main/test ones, ...

* At `build.gradle` added something like:
  ```
  sourceSets {
      main { java { srcDirs = ['src'] } } <··· Alt 1: Complete override defaults
      main { java { srcDir 'src'      } } <··· Alt 2: Add to defaults.
      test { java { srcDirs = ['test'] } }     common convention in Gradle:
  }                                            - setting a property   replaces values.
                                               - corresponding method appends values.
  ```

## Java properties files and reproducible builds.

### `WriteProperties` Task:

* fixes a well-known problem: `PropertiesInstance.store(...)` in standard Java API [[{PM.TODO]]
  produces a unique file every time breaking usefulness of incremental builds.     [[}]]

### Reusing shared Manifests

* (merging manifests into a single new one is also possible)
  ```
  ext.sharedManifest = manifest {              <···· Create standalone (shared) Manifest
      attributes("Implementation-Title": "Gradle",
                 "Implementation-Version": version)
  }
  tasks.register('fooJar', Jar) {
      manifest = project.manifest {
          from sharedManifest                 <······ Reuse in jar build
      }
  }
  ```


## ex: use Asciidoc  in Javadoc comments.   [[{qa.documentation.asciidoc]]

NOTE: This configuration is optional (but elegant)
  ```
  configurations { asciidoclet }
  dependencies {
      asciidoclet 'org.asciidoctor:asciidoclet:1.+'
  }
  
  tasks.register('configureJavadoc') {
    doLast {                          <········· Customize Task.Action with doLast
      javadoc {
        options.doclet = 'org.asciidoctor.Asciidoclet'
        options.docletpath = configurations.asciidoclet.files.toList()
      }
    }
  }
  
  javadoc {
    dependsOn configureJavadoc
  }
  ```

  [[}]]

## Java Library Plugin 
* The Java Library Plugin (on top of Java Plugin) allows gradle to
  distinguish between two different types of dependencies: those
  requiring the library at compile and those that are also required to
  compile the consumer and have task to create the POM file.

## War Plugin 
(plugin "on top" of Java Plugin) adds an extra packaging step to:
  - Copies static resources from src/main/webapp into root of WAR
  - Copies the compiled production classes into a WEB-INF/classes
  - Copies lib.dependencies into WEB-INF/lib subdirectory

[[gradle.java_projects}]]

# TODO/Non-classified [[{01_PM.TODO]]

## CUSTOMIZING THE WRAPPER
The built-in wrapper task exposes numerous options to customize the runtime behavior.
  ```
  build.gradle.kts:
  build.tasks.wrapper {
    distributionType = Wrapper.DistributionType.ALL
  }
  ```

## HTTP Basic Authentication (  WARN : use only with TLS connections)

* alt 1: ENV.VARS:
  ```
  systemProp.gradle.wrapperUser=username
  systemProp.gradle.wrapperPassword=password
  ```
* alt 2: `gradle/wrapper/gradle-wrapper.properties`
  ```
  distributionUrl=https://username:password@somehost/path/to/gradle-distribution.zip
  ```

## Verifying download checksum
   ```
   @gradle/wrapper/gradle-wrapper.properties:
    distributionSha256Sum=371cb9fbebbe9880d147f59bab36d61eee122854ef8c9ee1ecf12b82368bcf10
   ```

## https://scans.gradle.com/ [[{troubleshooting]]

* Build Scan™: shareable record of a build providing insights into
   what happened and why. You can create a Build Scan at
   scans.gradle.com for the Gradle and Maven build tools for free.
    Publishing a Build Scan to scans.gradle.com transmits information
   about your Gradle and Maven builds and their environment to Gradle's
   servers. The information is only accessible via a randomly generated
   link, printed at the end of the build. You can delete the
   Build Scan when you are finished. [[}]]

## Kotlin DSL [[{PM.TODO.now]]
[[}]]

## dependency_management_in_gradle
https://docs.gradle.org/current/userguide/core_dependency_management.html#dependency_management_in_gradle

## java_testing
https://docs.gradle.org/current/userguide/java_testing.html#java_testing

## Kotlin Symbol Processing (KSP) API

* [REF](https://kotlinlang.org/docs/ksp-overview.html)
  Kotlin Symbol Processing (KSP) API allows to create
  lightweight compiler plugins.
  Compared to kapt, annotation processors run up to x2 times faster.

## JAVA PLUGIN Tasks

* Kotlin PLUGIN Tasks ???
  assemble, check, build, jar, javadoc, clean ,...
  Gradle command to list available plugins???
  Gradle command to list tasks in a plugin???

* Further reading:
  ```
  https://docs.gradle.org/current/userguide/building_java_projects.html
  https://docs.gradle.org/current/userguide/declaring_repositories.html#sub:flat_dir_resolver
  https://docs.gradle.org/current/userguide/dynamic_versions.html#sub:declaring_dependency_with_changing_version
  https://docs.gradle.org/current/userguide/dynamic_versions.html#sub:declaring_dependency_with_dynamic_version
  https://docs.gradle.org/current/userguide/declaring_dependencies.html#sub:project_dependencies
  https://docs.gradle.org/current/userguide/dependency_constraints.html#dependency-constraints
  https://docs.gradle.org/current/userguide/composite_builds.html#composite_builds
  https://docs.gradle.org/current/userguide/declaring_repositories.html#sub:maven_local
  ```

[[ $devops.gradle}]]