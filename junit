Mainly there are 3 types of tests:

1. Unit test
1. Integration test
1. Functional test

To install JUnit5, following 2 dependencies are required:

```xml
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>${junit.jupiter.version}</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-engine</artifactId>
      <version>${junit.jupiter.version}</version>
      <scope>test</scope>
    </dependency>
```

-----------------------------

Once the dependencies are installed, we can start writing the tests and run them using
the IDE. This wouldn't work in CI/CD. What we need is a way to tests from the CLI. We do this with the help of `maven-surefire-plugin`.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.22.2</version>
      </plugin>
    </plugins>
</build>
```

-----------------------------

To install maven wrapper:

```bash
mvn -N wrapper:wrapper
```

For the above command to execute successfully, `maven` dependency is required.

```bash
sudo apt install maven
```

-----------------------------

To run all tests:

```bash
./mvnw test
```

-----------------------------

To run all test tagged with name "unit".

```bash
 mvn test -Dgroups=unit
```