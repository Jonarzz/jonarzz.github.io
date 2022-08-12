---
layout: post
title: "OOP - fewer definitions, more practice"
subtitle: "Designing external API"
excerpt: Based on the goal (user story) we create the external API.
         Based on the requirements (tasks) we create the internal API.
         Let's start with tests.
---
Since we have our [requirements](2019-12-26-OOP-getting-practical.md#requirements), we could proceed
and design the initial internal API of our project, but a more natural thing to do would be designing
the external API - what the user actually runs and sees.

### Initialize
So ["we know"](2019-12-26-OOP-getting-practical.md#requirements)
that our application will be run from the command line (it might change later, hence the quotes) -
let's create the main class of our project in `src/main/java`:
```java
package io.github.jonarzz;

public class I18nExample {

    public static void main(String[] args) {

    }

}
```
paired with the test in `src/test/java`:
```java
package io.github.jonarzz;

class I18nExampleTest {

    void successfullyCreateTranslationFiles() {
        
    }

}
```
For test orchestration we will use JUnit, so we add it to our `pom.xml`:
```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.5.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```
It is our project dependency, hence the `dependencies` tag - here we will place any external modules that we'd like to use.
Another new tag here is `scope` -
read more [here](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Dependency_Scope).

Now that we can use JUnit, let's add proper annotations to our first test:
```java
package io.github.jonarzz;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

@DisplayName("Internationalization tool acceptance tests")
class I18nExampleTest {

    @Test
    @DisplayName("Successfully create translation files")
    void successfullyCreateTranslationFiles() {

    }

}
```
where:
- `@Test` - marks the test method
- `@DisplayName` - used to make test reports easier to read and understand; could be a short sentence that summarizes the test class or it's methods

Furthermore, I encourage you to check [official JUnit user guide](https://junit.org/junit5/docs/current/user-guide/), it's excellent.

To run the tests during our build, we need to add a proper plugin to the
[pom.xml build section](2019-12-26-OOP-getting-practical.md#first-build):
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.0.0-M4</version>
</plugin>
```

Thanks to that we'll see such log when running the build:
```
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running io.github.jonarzz.I18nExampleTest
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.019 s - in io.github.jonarzz.I18nExampleTest
[INFO]
[INFO] Results:
[INFO]
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
```

We can optionally add the `configuration` section to the `plugin` to use `@DisplayName` values when running a Maven build in the console:
```xml
<configuration>
    <statelessTestsetInfoReporter implementation="org.apache.maven.plugin.surefire.extensions.junit5.JUnit5StatelessTestsetInfoReporter">
        <usePhrasedClassNameInRunning>true</usePhrasedClassNameInRunning>
        <usePhrasedClassNameInTestCaseSummary>true</usePhrasedClassNameInTestCaseSummary>
    </statelessTestsetInfoReporter>
</configuration>
```
Thanks to that, the log becomes:
```
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running Internationalization tool acceptance tests
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.019 s - in Internationalization tool acceptance tests
[INFO]
[INFO] Results:
[INFO]
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
```

### Assumptions
Most of the requirements are not fully confirmed, so we'll need to assume a few things for now:
1. `${this.is.our.placeholder.format}`
2. `this.is.our.placeholder.format = This is our dictionary format`
3. from `template/template1.html` filename, `eng.properties` and `pol.properties` dictionaries in `dictionary`
base directory we will create `output/template1-eng.html` and `output/template1-pol.html` translated files etc.

To successfully generate the translation files, we will need translation dictionaries in the format defined above
and template files with placeholders defined above.

Let's take such file structure:
- dictionary
  - eng.properties
  - pol.properties
- output
- template
  - template1.html
  - template2.html
  - template3.html
  
Our application will be run like so:
```
./i18n.sh [translation dictionaries path] [output path] [template files paths...]
```
where `i18n.sh` would be just `java -jar i18n-example-1.0.0.jar "$@"` - we run our jar file with the arguments passed to the script.
```
./i18n.sh dictionary output template/template1.html template/template2.html template/template3.html
```
The client might want to use just the `template` directory as the parameter for the recursive scan in the future,
so let's keep that in mind, especially that it seems natural, yet we will start like so for practice.

### Prepare for the future
We will write quite a few tests and they will all have a few things in common. If any of the requirements changes, we wouldn't like to
make modifications in multiple files, so the best thing to do is to extract what's common (within the bounds of reason of course).
**Don't repeat yourself**.

We will need files (templates, dictionaries and the results), their names and contents:
```java
public class TestConstants {

    public static final String ENGLISH_LANGUAGE = "eng";
    public static final List<String> ENGLISH_DICTIONARY_FILE_CONTENT_LINES = List.of(
            "fruit.apple = apple",
            "fruit.pear  = pear",
            "sentence.short = This is a short sentence",
            "sentence.long  = This is an example of a long sentence"
    );
    public static final String POLISH_LANGUAGE = "pol";
    public static final List<String> POLISH_DICTIONARY_FILE_CONTENT_LINES = List.of(
            "fruit.apple = jabłko",
            "fruit.pear  = gruszka",
            "sentence.short = To jest krótkie zdanie",
            "sentence.long  = To jest przykład długiego zdania"
    );
    public static final Collection<String> LANGUAGE_NAMES = Set.of(ENGLISH_LANGUAGE, POLISH_LANGUAGE);

    public static final String TEMPLATE_1_FILE_NAME = "template1";
    public static final List<String> TEMPLATE_1_FILE_CONTENT_LINES = List.of(
            "${fruit.apple} ${fruit.pear}",
            "${sentence.short}. ${sentence.long}."
    );
    public static final String TEMPLATE_2_FILE_NAME = "template2";
    public static final List<String> TEMPLATE_2_FILE_CONTENT_LINES = List.of(
            "${sentence.short}. ${sentence.long}.",
            "${fruit.apple} ${fruit.pear}"
    );
    public static final Collection<String> TEMPLATE_FILE_NAMES = Set.of(TEMPLATE_1_FILE_NAME, TEMPLATE_2_FILE_NAME);

    public static final Map<String, List<String>> TRANSLATED_FILENAME_TO_EXPECTED_CONTENT_LINES = Map.of(
            translatedFileName(TEMPLATE_1_FILE_NAME, ENGLISH_LANGUAGE),
            List.of(
                    "apple pear",
                    "This is a short sentence. This is an example of a long sentence."
            ),
            translatedFileName(TEMPLATE_1_FILE_NAME, POLISH_LANGUAGE),
            List.of(
                    "jabłko gruszka",
                    "To jest krótkie zdanie. To jest przykład długiego zdania."
            ),
            translatedFileName(TEMPLATE_2_FILE_NAME, ENGLISH_LANGUAGE),
            List.of(
                    "This is a short sentence. This is an example of a long sentence.",
                    "apple pear"
            ),
            translatedFileName(TEMPLATE_2_FILE_NAME, POLISH_LANGUAGE),
            List.of(
                    "To jest krótkie zdanie. To jest przykład długiego zdania.",
                    "jabłko gruszka"
            )
    );

    private TestConstants() {
    }

    public static String propertiesFileName(String language) {
        return language + ".properties";
    }

    public static String translatedFileName(String templateFileName, String language) {
        return templateFileName + "-" + language;
    }

}
```

We will also need a way to handle said files:
```java
public class TestResourceUtils {

    private TestResourceUtils() {
    }

    public static File createResourceDirectory(String subdirectoryName)
            throws URISyntaxException {
        URI resourcesUri = TestResourceUtils.class.getClassLoader()
                                                  .getResource(".")
                                                  .toURI();
        File resourceDirectory = new File(resourcesUri);
        File subdirectory = new File(resourceDirectory, subdirectoryName);
        if (!subdirectory.exists() && !subdirectory.mkdir()) {
            throw new RuntimeException("Could not create resource subdirectory " + subdirectoryName);
        }
        return subdirectory;
    }

    public static void createResource(File directory, String fileName, Iterable<String> fileContentLines)
            throws IOException {
        File file = new File(directory, fileName);
        if (!file.exists() && !file.createNewFile()) {
            throw new RuntimeException("Could not create file " + fileName);
        }
        Files.write(file.toPath(), fileContentLines);
    }

    public static URL getResource(String directoryPath, String resourcePath) {
        directoryPath = directoryPath.replaceAll("[/\\\\]$", "");
        return TestResourceUtils.class.getClassLoader()
                                      .getResource(directoryPath + File.separator + resourcePath);
    }

    // vavr's Either: https://www.javadoc.io/doc/io.vavr/vavr/latest/io/vavr/control/Either.html
    public static Either<AssertionFailedError, File> getResourceFileForAssertion(
            String directoryPath, String resourcePath) {
        URL resource = getResource(directoryPath, resourcePath);
        if (resource == null) {
            return Either.left(new AssertionFailedError(
                    resourcePath + " file does not exist in " + directoryPath + "/ resource directory"
            ));
        }
        try {
            return Either.right(new File(resource.toURI()));
        } catch (URISyntaxException e) {
            return Either.left(new AssertionFailedError(e.getMessage()));
        }
    }

}
```

Private constructors were used to show that those classes serve only as: a constants' holder (`TestConstants`)
and a utility static methods' provider (`TestResourceUtils`). It makes usage of the class more obvious as no instance can be created,
all calls should be static.

### [Acceptance test](http://softwaretestingfundamentals.com/acceptance-testing/)
When calling our application's `main` method with the arguments mentioned before, we expect translation files to be created,
having appropriate names and properly translated content. All of that boils down to the test shown below:
```java
// constants imported from TestConstants
// statically imported methods from TestResourceUtils:
// createResource, createResourceDirectory, getResource, getResourceFileForAssertion

@DisplayName("Internationalization tool acceptance tests")
class I18nExampleTest {

    private static final String RESOURCE_SUBDIRECTORY_NAME = "acceptance";
    private static final String DICTIONARY_SUBDIRECTORY_NAME = RESOURCE_SUBDIRECTORY_NAME + "/dictionary";
    private static final String OUTPUT_SUBDIRECTORY_NAME     = RESOURCE_SUBDIRECTORY_NAME + "/output";
    private static final String TEMPLATE_SUBDIRECTORY_NAME   = RESOURCE_SUBDIRECTORY_NAME + "/template";

    @BeforeAll
    static void setup() throws URISyntaxException, IOException {
        createResourceDirectory(RESOURCE_SUBDIRECTORY_NAME);

        File templateDirectory = createResourceDirectory(TEMPLATE_SUBDIRECTORY_NAME);
        createResource(templateDirectory, TEMPLATE_1_FILE_NAME, TEMPLATE_1_FILE_CONTENT_LINES);
        createResource(templateDirectory, TEMPLATE_2_FILE_NAME, TEMPLATE_2_FILE_CONTENT_LINES);

        File dictionaryDirectory = createResourceDirectory(DICTIONARY_SUBDIRECTORY_NAME);
        createResource(dictionaryDirectory, propertiesFileName(ENGLISH_LANGUAGE), ENGLISH_DICTIONARY_FILE_CONTENT_LINES);
        createResource(dictionaryDirectory, propertiesFileName(POLISH_LANGUAGE),  POLISH_DICTIONARY_FILE_CONTENT_LINES);

        createResourceDirectory(OUTPUT_SUBDIRECTORY_NAME);
    }

    @Test
    @DisplayName("Successfully create translation files")
    void successfullyCreateTranslationFiles() {
        String dictionaryDirectory = getResource(DICTIONARY_SUBDIRECTORY_NAME, "").getPath();
        String outputDirectory = getResource(OUTPUT_SUBDIRECTORY_NAME, "").getPath();
        Stream<String> templateFilePathsStream =
                TEMPLATE_FILE_NAMES.stream()
                                   .map(templateName -> getResource(TEMPLATE_SUBDIRECTORY_NAME,
                                                                    templateName))
                                   .map(URL::getPath);
        String[] arguments = Stream.concat(
                Stream.of(dictionaryDirectory, outputDirectory),
                templateFilePathsStream
        ).toArray(String[]::new);

        I18nExample.main(arguments);

        assertAll(
                getTranslatedFilesStreamForAssertion().map(either -> () -> {
                    File file = either.getOrElseThrow(either::getLeft);
                    assertTranslatedFile(file);
                })
        );
    }

    private Stream<Either<AssertionFailedError, File>> getTranslatedFilesStreamForAssertion() {
        Stream.Builder<Either<AssertionFailedError, File>> streamBuilder = Stream.builder();
        for (String templateFileName : TEMPLATE_FILE_NAMES) {
            for (String languageName : LANGUAGE_NAMES) {
                String translatedFileName = translatedFileName(templateFileName, languageName);
                streamBuilder.add(getResourceFileForAssertion(OUTPUT_SUBDIRECTORY_NAME, translatedFileName));
            }
        }
        return streamBuilder.build();
    }

    private void assertTranslatedFile(File file) throws IOException {
        assertTrue(file.exists(),
                   "Translated file " + file.getName() + " does not exist");

        String fileName = file.getName();
        assertTrue(TRANSLATED_FILENAME_TO_EXPECTED_CONTENT_LINES.containsKey(fileName),
                   () -> "Translated file name " + file.getName()
                         + " is not one of " + TRANSLATED_FILENAME_TO_EXPECTED_CONTENT_LINES.keySet());

        Path filePath = Paths.get(file.getPath());
        List<String> expectedLines = TRANSLATED_FILENAME_TO_EXPECTED_CONTENT_LINES.get(fileName);
        List<String> actualLines = Files.readAllLines(filePath);
        assertEquals(expectedLines.size(), actualLines.size(),
                     () -> "Number of lines in file " + file.getName() + " is different than expected");
        for (int i = 0; i < expectedLines.size(); i++) {
            assertEquals(actualLines.get(i), actualLines.get(i),
                         "Line " + (i + 1) + " in file " + file.getName() + " is different than expected");
        }
    }

}
```
It's worth using the `message` parameters in JUnit assertion methods as it makes test failure logs easier to understand and debug.
For example: `expected <true>, but got <false>` does not say much, but when there's `Translated file invalid-name.txt does not exist` next to it -
it all makes sense. Another way of handling it is
[Hamcrest](https://objectpartners.com/2013/09/18/the-benefits-of-using-assertthat-over-other-assert-methods-in-unit-tests/),
which often increases readability of assertions. We'll not focus on that in this series, but it's worth to know the possibilities.

### Summary
We ended up with a test that fails. [This is expected](2019-12-26-OOP-getting-practical.md#red---green---refactor).
```
[ERROR] Failures: 
[ERROR]   I18nExampleTest.successfullyCreateTranslationFiles:66 Multiple Failures (2 failures)
	org.opentest4j.AssertionFailedError: template1-eng file does not exist in acceptance/output/ resource directory
	org.opentest4j.AssertionFailedError: template1-pol file does not exist in acceptance/output/ resource directory
	org.opentest4j.AssertionFailedError: template2-eng file does not exist in acceptance/output/ resource directory
	org.opentest4j.AssertionFailedError: template2-pol file does not exist in acceptance/output/ resource directory
[INFO] 
[ERROR] Tests run: 1, Failures: 1, Errors: 0, Skipped: 0
```
Our application does not actually do anything, so the test does not pass. When we implement its features, the test should succeed.
Before that happens, we'll need to design the internal API and cover it with tests - that's what we'll do in the next article.
_Spoiler alert - we will not_.