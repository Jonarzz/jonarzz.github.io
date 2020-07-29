---
layout: post
title: "OOP - fewer definitions, more practice"
subtitle: "How to start a project?"
excerpt: When you know what project you would like to do -
         you would probably like to get your hands into the code as fast as possible.
         While it may be tempting, it would be wise to do a few things first.
---
I would like these posts to be knowledge dense, so let's cut to the chase without further ado.
When you know what project you would like to do - what tool to create, what application to make -
you would probably like to get your hands into the code as fast as possible. While it may be tempting,
it would be wise to do a few things first.

### Choose your weapon
When creating an enterprise scale application with complicated business logic and lots of technical demands,
Java seems like a good choice. But what if you wanted to create a simple or even more complex,
reactive page? Or a simple command line tool? You could actually do all these things in Java
(though I've seen only bad [Vaadin](https://vaadin.com/) usage, unfortunately; also -
[welcome to the future](https://trends.google.pl/trends/explore?date=all&q=vaadin,gwt,react,angular)),
but other languages and tools such as plain
[HTML](https://developer.mozilla.org/pl/docs/Web/HTML)+[CSS](https://developer.mozilla.org/pl/docs/Web/HTML),
[React](https://reactjs.org/) or [Python](https://www.python.org/) probably would be
more suitable for the aforementioned purposes.

Similarly, in just Java world **there is a ton of tools and libraries and choosing the right ones
for the project is an important thing to do** at the very start and during the development.
Lots of code has been written and there are so many open source and free to use projects
that there is no point in writing everything from scratch. Of course, it's worth knowing how they work
or even write some similar tools to learn, but in the end using appropriate tools and frameworks
can save us a lot of time and trouble. On the other hand, multiplicity of such projects often makes it
hard to come up with a good choice at the very beginning. It all comes with experience,
so if you can use your IT colleagues' knowledge at the university or at work - do it!
You can find a vast list of Java frameworks [here](https://github.com/akullpp/awesome-java).
Usually the best idea is to just look up the Internet - blog posts and articles - to find the best tool
for your purpose - [here's](https://hackr.io/blog/java-frameworks) an example.
StackOverflow pages are also a great source with often thorough analysis and discussion.

### Set up the world
#### Build tools
Plain Java projects are hard to maintain and hard to build for others.
**Always use [Maven](https://maven.apache.org/) or [Gradle](https://gradle.org/).**
These tools allow you to set up a complete build with proper packaging,
manage all the project's dependencies and utilize plugins: run tests during build process,
generate JavaDoc and do [many more things](https://maven.apache.org/plugins/).
Thanks to that, a properly configured Maven/Gradle project can take up only a few seconds
from downloading it to getting it to run or to being able to modify it easily.

#### Java version
An important thing when creating a tool that others might find useful is to choose the right Java version.
Big companies are often reluctant to change the JDK version every few months and to choose
the newest ones without them being properly tested and bugfixed. Current
[Java Support Roadmap](https://www.oracle.com/technetwork/java/java-se-support-roadmap.html) 
makes it more tempting to choose newer and newer Java versions as most of them provide some new features,
API enrichment and optimization. The other side of the coin is that already existing projects using
older Java version will not be able to use your library until they migrate to new version,
and it may not happen as often as it could be expected. Right now, in my opinion, starting with current
LTS (long term support) Java would be the best option. All information about release dates and LTS-marked
versions can be found in the link above.

### Divide and conquer
The last thing I want to mention is maybe less important for a client or a tool user at first sight,
but cannot be overlooked by developers.
[Packaging](https://www.baeldung.com/java-packages).
[Modules](https://www.baeldung.com/java-9-modularity).
Code division overall.

#### Packaging
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

Now let's imagine we would like to create an e-commerce application (web shop). A few exemplary
data model entities that could be included: Product, Customer, Cart, Order, Payment, Delivery.
When using the approach described above, we could end up with a catalog structure like so:

- `com.example.dao`
    - ProductRepository
    - CustomerRepository
    - CartRepository
    - OrderRepository
    - PaymentRepository
    - DeliveryRepository
- `com.example.service`
    - ProductService
    - CustomerService
    - CartService
    - OrderService
    - PaymentService
    - DeliveryService
- `com.example.controller`
    - ProductController
    - CustomerController
    - CartController
    - OrderController
    - PaymentController
    - DeliveryController
- `com.example.model`
    - Product
    - Customer
    - Cart
    - Order
    - Payment
    - Delivery
    
...and that's without interfaces and their implementations. That's **package by layer**. Often used, has its advantages, but now that I worked in
environments with such approach more, I prefer **package by feature**.

Package-by-feature's main goal is to separate the files by their business and not technical context.
First let's see how would the structure above look in this approach:

- `com.example.product`
    - ProductRepository
    - ProductService
    - ProductController
    - Product
- `com.example.customer`
    - CustomerRepository
    - CustomerService
    - CustomerController
    - Customer
- `com.example.cart`
    - CartRepository
    - CartService
    - CartController
    - Cart
- `com.example.order`
    - OrderRepository
    - OrderService
    - OrderController
    - Order
- `com.example.payment`
    - PaymentRepository
    - PaymentService
    - PaymentController
    - Payment
- `com.example.delivery`
    - DeliveryRepository
    - DeliveryService
    - DeliveryController
    - Delivery

More usual situation is to have to "fix something related to order" than "fix all controllers",
so this structure makes it easier to find yourself in. Testing modules with concise
and strictly separated logic is easier and more consistent as well.
Another huge advantage is the possibility to actually use the **default
[access level modifier](https://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html)**,
which is not `public` on the contrary to popular belief, and it was made like this for a reason.

#### API
API (application programming **interface**) is a commonly used term. Maybe even used to the point that
the abbreviation loses its actual meaning, becoming anything the person saying it wanted to say.
Emphasis on the word *interface* is not coincidental. API is what you're actually exposing in your
application. If someone is to use your code, they will do it through the API. If you make everything
public, it may turn out that the tool or framework you've written is not used as you intended.
You create a new version of your library, and you want to change some names, modify method signatures -
if public classes are modified, the version will have to increase it's [major part](https://semver.org/).
That's why most libraries don't change this part so often -
such changes are ["breaking"](https://en.wiktionary.org/wiki/breaking_change), require changes in
client code or even migration. This is the reason not to start coding without analyzing the problem,
thinking it over, designing an API and encapsulating it.

*Note: "REST API" is commonly used altogether, and it also fits the definition above -
 this is an externally exposed web interface that has a strict contract,
 hence the existence of [versioning mechanism](https://restfulapi.net/versioning/)*.
 
So - having `public` modifier only on the classes we want to be used externally of the package
makes it easier to expose an API, view and analyze the code, test it and refactor. In the shop example
above we should probably make only `Service` classes public, and the rest could stay hidden.

#### Separation
The last advantage of the package by feature approach is its modularity. With strict boundaries
between the packages and [loose coupling](https://stackoverflow.com/questions/226977/what-is-loose-coupling-please-provide-examples)
it is pretty easy to extract some parts of the application to a separate one, maybe even deployed on
a separate server. Boom! [Microservices](https://microservices.io/).

In the [next part](2019-12-26-OOP-getting-practical.md) we will finally see some code -
based on everything said above.