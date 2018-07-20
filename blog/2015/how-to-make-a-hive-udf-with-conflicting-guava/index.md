<!--
.. title: How to make a Hive UDF with conflicting Guava
.. slug: how-to-make-a-hive-udf-with-conflicting-guava
.. date: 2015-05-29 00:31:00+02:00
.. tags: Hive,Big Data,Maven,Java
.. category: Hive
.. link: 
.. description: 
.. type: text
-->

If you're using Hive sooner or later you'll need to create user defined functions (UDFs). Chances are such a function would use a code that depends on the Guava library. And it is not that unlikely that the required Guava version would be newer than Hive's. Then you're running into a trouble. Hopefully this article save you much of the pain I had to suffer to make it working. Let's make a simple UDF and fix it using both maven and gradle.

<!-- TEASER_END -->

## Example UDF - top private domain

As a concrete example take a UDF that given a internet host name computes the top private domain. Eg. for host name `www.google.co.uk`, `uk` and `co.uk` are public domains, `google.co.uk` and `www.google.co.uk` are private domains and `google.co.uk` is the highest (top) private domain in this case.

Guava's `InternetDomainName.topPrivateDomain()` already does this job. We need lastest Guava in order to have an up-to-date trie representing public domains. However, there's a difference between ancient Guava 11 and latest Guava 18:

```java
InternetDomainName.from("www.google.co.uk").topPrivateDomain().toString()
// returns "google.co.uk" in Guava 18
// returns "InternetDomainName{name=google.co.uk}" in Guava 11
//   "google.co.uk" is returned by name() instead of toString()
```

The whole UDF class looks like this:

```java
package com.example.hive.udf;

import com.google.common.net.InternetDomainName;
import org.apache.hadoop.hive.ql.exec.UDF;
import org.apache.hadoop.io.Text;

/**
 * Extracts top private domain from a host name.
 */
public class TopPrivateDomain extends UDF {

    public Text evaluate(Text host) {
        if (host == null) {
            return null;
        }
        try {
            return new Text(InternetDomainName.from(host.toString())
                .topPrivateDomain()
                .toString());
        } catch (IllegalArgumentException ex) {
            return null;
        }
    }
}
```

In this case the incompatibility is just in the return value but in other cases there might be runtime errors such as added/removed/changed classes, methods, etc.

## Conflicts in library versions

So what's the problem with Guava and Hive? Hadoop and Hive are in general packaged with ancient or outdated version of Guava. Eg. Hive 0.14 available in CDH5 has Guava 11 and current Hive 1.2 has Guava 15, while the most recent Guava version is 18. The bigger the version difference the higher chance of incompatibilities. And the problem is that more than one version of the library cannot be easily loaded within JVM (at least in Hive).

However, there's a trick. If there a conflict between classes in the same package(s) (in this case `com.google.common` and `com.google.thirdparty`), why not to rename the package(s)?

There's one more catch with Hive UDFs. For some strange reason when registering the UDF Hive sees only classes from the JAR than contains the UDF class and not from other specified JARs. This along with renaming the packages leads to the need for a fat JAR, ie. a JAR containing all the necessary dependencies for the UDF class (or at least those shaded or not seen by Hive). Blindly packaging all transitive dependencies might result in a big bloated JAR, so we'd also like to minimize it just to classes that are actually used by our UDF.

The overall plan is thus to:

- package all the dependencies of the UDF into a fat JAR
    - to make Hive see the other classes
- rename (shade) the Guava packages
    - both the packages of the Guava classes and their imports in our code
    - to prevent conflict with Hive's own Guava
- (optional) minimize the resuling fat JAR
    - remove unnecessary dependencies or even unused classes

We'll explore how to do it using maven's `maven-shade-plugin` and gradle's `shadow` plugin.

## Maven - maven-shade-plugin

If your're using maven for building fortunately there's a plugin called [maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/) which can do both creating a fat JAR and renaming packages.

Within Maven's `pom.xml` in the plugin's configuration just specify using the `<relocation>` tags which package(s) should be renamed to what. The old package name is in the `<pattern>` and the new one in `<shadedPattern>`.

```xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-shade-plugin</artifactId>
      <version>2.3</version>
      <executions>
        <execution>
          <phase>package</phase>
          <goals>
            <goal>shade</goal>
          </goals>
          <configuration>
            <relocations>
              <relocation>
                <pattern>com.google.common</pattern>
                <shadedPattern>com.example.shaded.com.google.common</shadedPattern>
              </relocation>
              <relocation>
                <pattern>com.google.thirdparty</pattern>
                <shadedPattern>com.example.shaded.com.google.thirdparty</shadedPattern>
              </relocation>
            </relocations>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```

In order to minimize the JAR, enable it via the following. Beware that this might remove some classes used only by reflection. In such a case explicitly include the class via `configuration.filters.filter.include`. Please find the details in the [docs](https://maven.apache.org/plugins/maven-shade-plugin/examples/includes-excludes.html).

```xml
...
<configuration>
  <minimizeJar>true</minimizeJar>
</configuration>
...
```

By default the plugin produces the shaded JAR with the same name as the original and add a prefix `original-` to the latter. If we want the opposite behavior (mark the shaded JAR, eg. with `-jar-with-dependencies` suffix), we can to this via:

```xml
...
<configuration>
  <shadedArtifactAttached>true</shadedArtifactAttached>
  <shadedClassifierName>jar-with-dependencies</shadedClassifierName>
</configuration>
...
```

Then just build the JAR with `$ maven package` and copy it to the machine(s) running Hive.

## Gradle - shadow

Gradle [Shadow](https://github.com/johnrengelman/shadow) plugin is inspired by `maven-shade-plugin`. The basic configuration might look like this:

```groovy
plugins {
  id 'java' // or 'groovy' Must be explicitly applied
  id 'com.github.johnrengelman.shadow' version '1.2.1'
}

apply plugin: 'java' // or 'groovy'. Must be explicitly applied
apply plugin: 'com.github.johnrengelman.shadow'

shadowJar {
  relocate('org.google.common', 'com.exaple.shaded.org.google.common')
}
```

```bash
$ gradle shadowJar
```

## Registering the UDF

Within Hive register the UDF like this:

```sql
ADD JAR /path/to/the/example-hive-udf.1.0-jar-with-dependencies.jar;
CREATE TEMPORARY FUNCTION topPrivateDomain AS 'com.example.hive.udf.TopPrivateDomain';
```

The first command tells Hive to copy the JAR into the distributed cache and put it on the claspath, so that it is visible from all MapReduce tasks. The second one registers the UDF class with the given function name.

## Tips

In case you just need to modify the classpath and give your JAR more priority (without renaming packages) try to fiddle with these options:

```bash
export HADOOP_USER_CLASSPATH_FIRST=true
```

```sql
SET mapreduce.job.user.classpath.first=true;
```

```bash
hive --hiveconf hive.aux.jars.path='some-jar-1.0.jar'
```

More info on Hive UDFs:

- https://cwiki.apache.org/confluence/display/Hive/HivePlugins
- http://blog.matthewrathbone.com/2013/08/10/guide-to-writing-hive-udfs.html