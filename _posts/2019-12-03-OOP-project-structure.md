---
layout: post
title: "OOP - less definitions, more practice"
subtitle: "2. How to start a project?"
---
I would like these posts to be knowledge dense, so let's cut to the chase without further ado.
When you know what project you would like to do - what tool to create, what application to make -
you would probably like to get your hands into the code as fast as possible. While it may be tempting,
it would be wise to do a few things first.

### Choose your weapon
When creating an enterprise scale application with complicated business logic and lots of technical demands,
Java seems like a good choice. But what if you wanted to create a simple or even more complex,
reactive page? Or a simple command line tool? You could actually do all these things in Java
(though I've seen only bad [Vaadin](https://vaadin.com/) usage, unfortunately),
but other languages and tools such as plain
[HTML](https://developer.mozilla.org/pl/docs/Web/HTML)+[CSS](https://developer.mozilla.org/pl/docs/Web/HTML),
[React](https://reactjs.org/) or [Python](https://www.python.org/) probably would be
more suitable for the mentioned purposes.

Similarly in just Java world **there is a ton of tools and libraries and choosing the right ones
for the project is an important thing to do** at the very start and during the development.
Lots of code has been written and there are so many open source and free to use projects
that there is no point in writing everything from scratch. Of course, it's worth knowing how they work
or even write some similar tools to learn, but in the end using appropriate tools and frameworks
can save us a lot of time and trouble. On the other hand, multiplicity of such projects often makes it
hard to come up with a good choice at the very beginning, but the Internet is rich in articles
and IT companies and teams structures make it easier for the knowledge to flow. You can find a short list
of Java frameworks [here](https://en.wikipedia.org/wiki/List_of_Java_frameworks), but usually the best idea
is to look up the Internet - blog posts, articles and StackOverflow pages - to find the best tool
for your purpose.

### Set up the world
Plain Java projects are hard to maintain and hard to build for others.
**Always use [Maven](https://maven.apache.org/) or [Gradle](https://gradle.org/).**
These tools allow you to set up a complete build with proper packaging,
run tests in the process and manage all the project's dependencies. Thanks to that, a properly
configured Maven/Gradle project can take up only a few seconds from downloading it to getting it
to be running or to being able to modify it easily.

An important thing when creating a tool that others might find useful is to choose the right Java version.
Big companies are often reluctant to change the JDK version every few months and also to choose
newest ones without them being properly tested and bugfixed. Current
[Java Support Roadmap](https://www.oracle.com/technetwork/java/java-se-support-roadmap.html) 
makes it more tempting to choose newer and newer Java versions as most of them provide some new features,
API enrichment and optimization. The other side of the coin is that already existing projects using
older Java version will not be able to use your library until they migrate to new version
and it may not happen as often as it could be expected. Right now, in my opinion, starting with current
LTS (long term support) Java would be the best option. All information about release dates and LTS-marked
versions can be found in the link above.

### Divide and conquer
The last thing I want to mention is maybe less important for a client or a tool user at first sight,
but cannot be overlooked by developers.
[Packaging](https://www.baeldung.com/java-packages).
[Modules](https://www.baeldung.com/java-9-modularity).
Code division overall.

A usual way to divide a project is by logical components. Let's say we have 3 logical layers and 
the data model in a web application:

- `com.example.dao` ([Data Access Object](https://www.baeldung.com/java-dao-pattern);
*repository* is sometimes used instead of *dao*)
- `com.example.service` (logic and data manipulation)
- `com.example.controller` (serving the actual web contents)
- `com.example.model` ([data holding classes](https://martinfowler.com/bliki/LocalDTO.html) -
could be simple objects only transferring data or [objects incorporating logic](https://martinfowler.com/eaaCatalog/domainModel.html))

*If you want to get into more details
[here's a nice place to start](https://stackoverflow.com/a/38549461/3305737)
(logical components as defined in [Spring Framework](https://spring.io/))*.

