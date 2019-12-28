---
layout: post
title: "OOP - less definitions, more practice"
subtitle: "Getting practical"
excerpt: Having a good idea for a project is not an easy thing.
         What's even more difficult is actually starting the project and finishing it.
---
### Easier thought than done
Having a good idea for a project is not an easy thing. What's even more
difficult is actually starting the project and finishing it. Many distractions
show up during the process of development, especially when programming after school/work,
during your free time. You may be tired, may have other things to do or maybe even 
a great idea for an application has suddenly come to your mind.
[Sounds familiar?](http://www.commitstrip.com/en/2014/11/25/west-side-project-story/)

When learning a framework, a tool or a new programming language, I would suggest starting with
something relatively small. I'd say that it would be best if such a project could be finished
in it's basic form in a week. After that time you could start something new or continue with
adding new features to this one, but I want to emphasize having something actually **done**.
While [agile development](https://agilemanifesto.org/principles.html) might not be all
sunshine and rainbows, when carried out right, it really helps both the developers and
[the client](2019-09-24-OOP-intro.md#and-its-changed). Why not be agile at home?

### Create the project
I assume that you have an IDE of your choice installed (it's IntelliJ IDEA for me),
since this series' target audience are developers with some experience. As said in the
[previous post](2019-12-03-OOP-project-structure.md#build-tools) - start with the build tool.
I'm using Maven in this project, but
Gradle [is analogous](https://docs.gradle.org/current/userguide/migrating_from_maven.html#migmvn:migrating_deps).

When creating a new Maven project, we need to set up the
[pom.xml](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html#What_is_a_POM) configuration file
(when using an IDE, this should be handled by a project wizard). Let's start with a
[minimal POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Minimal_POM).

**Group ID** is an identifier that all our projects should have in common - it's a way of defining
the author(s) of the project and grouping all the projects by the same authors together. For example:
[org.apache](https://mvnrepository.com/artifact/org.apache) groups the projects of
[the Apache foundation](https://www.apache.org/) and [org.springframework](https://mvnrepository.com/artifact/org.springframework) -
[the Spring Framework](https://mvnrepository.com/artifact/org.springframework) projects.
By convention it's usually the main page of your organization/personal page's URL reversed, so in my case
it's `io.github.jonarzz` - if you don't have your own domain, you could start with a similar one,
with the username replaced.

**Artifact ID** - the actual name of the project.

**Version** - current version of the project - already explained in
[the previous post](2019-12-03-OOP-project-structure.md#api).

```xml
<groupId>io.github.jonarzz</groupId>
<artifactId>i18n-example</artifactId>
<version>1.0.0</version>
```

### Define responsibilities
Let's think of the steps our application should perform to do what it's supposed to do
and meet the requirements. We'll prepare a [user story](https://www.atlassian.com/agile/project-management/user-stories)
and divide it into more technical tasks.
#### Main goal
As a static website owner I want to be able to create multiple files in different languages
based on a template, so that it's easy to create multilingual versions of my page and reach people
from around the world.
#### Requirements
 - run as an executable command line script 
    - *might change later*
 - load the dictionary file with translations
    - *format not yet known*
 - load one or more template files
    - *way of searching for template files not yet known*
 - replace the placeholders in templates with appropriate translations
    - *placeholder style not yet known*
 - save translated files
 - display a meaningful error message in case of any problems

### What is in it for a developer
*TODO*