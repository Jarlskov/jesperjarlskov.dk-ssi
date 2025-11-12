---
title: 'Front Controller design pattern'
date: 2016-03-09
---

A common design patterns used by PHP frameworks, is the front controller. The base idea is simply that your application has a single entry point, which distributes all requests to the proper parts of the system. In php the front controller pattern usually consists of an index.php file.

<h2>Front controller advantages</h2>
The front controller patterns provides a single place for handling an application's bootstrap process. This helps keep the bootstrap process similar no matter which part of the system a user requests.

The front controller's main job is to distribute the request further into the system, this can either be by dispatching a command to update the model directly, or by passing the request to a controller based on a defined route that matches the request.

<h2>Front controller alternatives</h2>
A lot of older php guides and books recommends having an entry point for each part of the application. For simple websites this could be a way to separate the concerns of each page into its own file. A basic portfolio page could consist of 3 files:
<ul>
<li>index.php - The frontpage</li>
<li>portfolio.php - The actual portfolio, with example projects etc.</li>
<li>contact.php - A page with contact info, and a contact form.</li>
</ul>

In this example all functionality relating to completed projects would be in portfolio.php. Similarly, all concerns regarding the contact form would be kept in the contact.php.
This approach has its strength in its simplicity and performance. Any libraries included to send email from the contact form will only be imported on the contact page. Thus it will not slow down the other pages.

The problem with this is that it doesn't scale well. If you're building a mildly complex application there will be a large part of redundancy in the bootstrapping process for each page. Eg. if the page has a user system, all parts of the user system will have to be included in every file to do access checks. At this point it makes sense to separate bootstrapping into a dedicated process, that is included in the pages requiring it. This in turn means that the previous benefits starts to become moot.

<h2>References and future reading</h2>
<ul>
<li><a href="http://martinfowler.com/eaaCatalog/frontController.html">Martin Fowler on the front controller pattern</a></li>
<li><a href="http://www.tutorialspoint.com/design_pattern/front_controller_pattern.htm">Tutorial with short descriptive example in Java</a></li>
</ul>