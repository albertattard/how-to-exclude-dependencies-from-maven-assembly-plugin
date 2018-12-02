Any dependencies that are already available at the runtime environment can be given the scope of `provided` as highlighted below.

```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.8.0-beta2</version>
      <scope>provided</scope>
    </dependency>
```

The SLF4J ([Homepage](https://www.slf4j.org/)) will not be included by the `maven-assembly-plugin` plugin in the final JAR, as this dependency is marked as `provided`.  The complete XML is shown next for completeness.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

  <modelVersion>4.0.0</modelVersion>
  <groupId>com.javacreed.examples</groupId>
  <artifactId>how-to-exclude-dependencies-from-maven-assembly-plugin</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>
  <name>How To Exclude Dependencies from Maven Assembly Plugin</name>
  <url>https://github.com/javacreed/${project.artifactId}</url>
  <inceptionYear>2014</inceptionYear>

  <contributors>
    <contributor>
      <name>Albert Attard</name>
      <email>albert@javacreed.com</email>
    </contributor>
  </contributors>

  <licenses>
    <license>
      <name>The Apache Software License, Version 2.0</name>
      <url>http://www.apache.org/licenses/LICENSE-2.0.txt</url>
      <distribution>repo</distribution>
    </license>
  </licenses>

  <scm>
    <connection>scm:git:git://github.com/javacreed/${project.artifactId}.git</connection>
    <developerConnection>scm:git:git://github.com/javacreed/${project.artifactId}.git</developerConnection>
    <url>https://github.com/javacreed/${project.artifactId}</url>
  </scm>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>

    <surefire.version>2.20.1</surefire.version>
    <jacoco.version>0.7.9</jacoco.version>
    <reports.version>2.9</reports.version>
    <slf4j.version>1.8.0-beta2</slf4j.version>
    <maven.assembly.version>2.4</maven.assembly.version>
    <junit.version>4.13-beta-1</junit.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>${slf4j.version}</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>${junit.version}</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <defaultGoal>clean package site versions:display-dependency-updates</defaultGoal>

    <plugins>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <version>${maven.assembly.version}</version>
        <configuration>
          <finalName>Application</finalName>
          <appendAssemblyId>false</appendAssemblyId>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
          <archive>
            <manifest>
              <mainClass>com.javacreed.examples.maven.Main</mainClass>
            </manifest>
          </archive>
        </configuration>
        <executions>
          <execution>
            <id>make-my-jar-with-dependencies</id>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>${surefire.version}</version>
        <configuration>
          <!-- Sets the VM argument line used when unit tests are run. -->
          <argLine>${surefireArgLine}</argLine>
          <!-- Skips unit tests if the value of skip.unit.tests property
            is true -->
          <skipTests>${skip.unit.tests}</skipTests>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.jacoco</groupId>
        <artifactId>jacoco-maven-plugin</artifactId>
        <version>${jacoco.version}</version>
        <executions>
          <!-- Prepares the property pointing to the JaCoCo runtime agent
            which is passed as VM argument when Maven the Surefire plugin is executed. -->
          <execution>
            <id>pre-unit-test</id>
            <goals>
              <goal>prepare-agent</goal>
            </goals>
            <configuration>
              <!-- Sets the path to the file which contains the execution
                data. -->
              <destFile>${project.build.directory}/coverage-reports/jacoco-ut.exec</destFile>
              <!-- Sets the name of the property containing the settings
                for JaCoCo runtime agent. -->
              <propertyName>surefireArgLine</propertyName>
            </configuration>
          </execution>
          <!-- Ensures that the code coverage report for unit tests is created
            after unit tests have been run. -->
          <execution>
            <id>post-unit-test</id>
            <phase>test</phase>
            <goals>
              <goal>report</goal>
            </goals>
            <configuration>
              <!-- Sets the path to the file which contains the execution
                data. -->
              <dataFile>${project.build.directory}/coverage-reports/jacoco-ut.exec</dataFile>
              <!-- Sets the output directory for the code coverage report. -->
              <outputDirectory>${project.reporting.outputDirectory}/jacoco-ut</outputDirectory>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>

  <reporting>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-project-info-reports-plugin</artifactId>
        <version>${reports.version}</version>
        <configuration>
          <dependencyLocationsEnabled>false</dependencyLocationsEnabled>
        </configuration>
      </plugin>

      <plugin>
        <groupId>org.jacoco</groupId>
        <artifactId>jacoco-maven-plugin</artifactId>
        <version>${jacoco.version}</version>
        <configuration>
          <!-- Sets the path to the file which contains the execution data. -->
          <dataFile>${project.build.directory}/coverage-reports/jacoco-ut.exec</dataFile>
        </configuration>
        <reportSets>
          <reportSet>
            <reports>
              <!-- select non-aggregate reports -->
              <report>report</report>
            </reports>
          </reportSet>
        </reportSets>
      </plugin>
    </plugins>
  </reporting>
</project>
```

The application can be built by Maven ([Homepage](https://maven.apache.org/)) into a single JAR (referred to as FAT JAR ([Plugin](http://maven.apache.org/plugins/maven-assembly-plugin/))).  This application makes use of SLF4J as shown in the following fragment.

```java
package com.javacreed.examples.maven;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Main {

    private static final Logger LOGGER = LoggerFactory.getLogger(Main.class);

    public static void main(final String[] args) {
        Main.LOGGER.debug("My Application uses SLF4J");
    }
}
```

This application can be ran using the following command

```
$ java -jar Application.jar
```

Unfortunately, this application will fail to run as it is missing the SLF4J library, which was excluded on purpose.

```
Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/LoggerFactory
    at com.javacreed.examples.maven.Main.<clinit>(Main.java:8)
Caused by: java.lang.ClassNotFoundException: org.slf4j.LoggerFactory
    at java.net.URLClassLoader.findClass(URLClassLoader.java:382)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
    at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:349)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
    ... 1 more
```

If you want to run this application, then you need to change the dependency scope.  The code listed above is available at [https://github.com/javacreed/how-to-exclude-dependencies-from-maven-assembly-plugin](https://github.com/javacreed/how-to-exclude-dependencies-from-maven-assembly-plugin).
