# proguard-example

### Overview

This project illustrates non-deterministic behavior by the Gradle (v1.12) `install` task when installing multiple `jar` and `proguard` artifacts.

0. Sometimes, executing `./gradlew install --info` installs the expected number of artifacts - 4 ('debug' classifier corresponds to `jar` artifacts; no classifier corresponds to `proguard` artifacts):

    ```
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/libs/gradle-example-lower-1.0.0-SNAPSHOT-debug.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT-debug.jar
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/libs/gradle-example-upper-1.0.0-SNAPSHOT-debug.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT-debug.jar
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-upper-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT.jar
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-lower-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT.jar
    ```

0. Other times, 5 artifacts are installed:
    0. This can be because one artifact is installed twice (innocuous):

    <pre>
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/libs/gradle-example-lower-1.0.0-SNAPSHOT-debug.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT-debug.jar
<b>[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-upper-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT.jar</b>
<b>[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-upper-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT.jar</b>
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/libs/gradle-example-upper-1.0.0-SNAPSHOT-debug.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT-debug.jar
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-lower-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT.jar
    </pre>

    0. Or because one artifact is **incorrectly** installed over the correct one:

    <pre>
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/libs/gradle-example-lower-1.0.0-SNAPSHOT-debug.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT-debug.jar
<b>[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-lower-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT.jar</b>
<b>[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-<i>lower</i>-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-<i>upper</i>-1.0.0-SNAPSHOT.jar</b>
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/libs/gradle-example-upper-1.0.0-SNAPSHOT-debug.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT-debug.jar
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-upper-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT.jar
    </pre>
  
0. Finally, I have also seen 6 artifacts installed - in this case, 2 duplicates:

    <pre>
<b>[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-upper-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT.jar</b>
<i>[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-upper-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT.jar</i>
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/libs/gradle-example-lower-1.0.0-SNAPSHOT-debug.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT-debug.jar
<i>[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-lower-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-lower/1.0.0-SNAPSHOT/gradle-example-lower-1.0.0-SNAPSHOT.jar</i>
<b>[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/proguard/gradle-example-lower-1.0.0-SNAPSHOT.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT.jar</b>
[INFO] Installing /Users/echen/data/vungle/code/gradle-example/build/libs/gradle-example-upper-1.0.0-SNAPSHOT-debug.jar to /Users/echen/.m2/repository/example/gradle-example-upper/1.0.0-SNAPSHOT/gradle-example-upper-1.0.0-SNAPSHOT-debug.jar
    </pre>

### Observations

The problem seems to lie with installing the Proguard artifacts rather than the `jar` task artifacts (notice that duplication or overwriting install never occurs with the `-debug` artifacts).

### Envrionment

* Gradle 1.12 (with Gradle wrapper)
* Java 1.6.0_65
* Mac OS X 10.9.4
