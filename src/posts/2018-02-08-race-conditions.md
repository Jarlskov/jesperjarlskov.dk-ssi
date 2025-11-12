---
title: 'Race conditions'
date: 2018-02-08
---

We just had an issue at work. A system that had been working fine suddenly started losing data. This had never been an issue before, and it seemed to happen at random which made it pretty hard to debug and reproduce.

The system consisted of a JavaScript-heavy web frontend, interacting with an API based on a PHP and MySQL backend.

After a lot of digging around in network requests and code on both the front- and backend, we discovered that the issue was caused by a race condition in the PHP code due to simultaneous AJAX requests from the frontend.
<h2>How it was designed to work</h2>
The subsystem we were having issues with consisted of a large form used to create drafts of items in the database. When the value of any form field was changed the frontend would fire an AJAX request to the backend with the property name and the new value. The PHP backend would then load the draft from the database, add or update the new property, and save it again. Simple stuff.

[caption id="attachment_1540" align="aligncenter" width="648"]<a href="https://jesperjarlskov.dk/wp-content/uploads/2018/02/Sequential-no-race-condition.png"><img class="size-full wp-image-1540" src="https://jesperjarlskov.dk/wp-content/uploads/2018/02/Sequential-no-race-condition.png" alt="Diagram: Everything running sequentially, no race condition" width="648" height="381" /></a> Everything running sequentially, no race condition[/caption]
<h2>How it actually worked</h2>
This design worked fine for a start, but as the subsystem grew and got slower stuff was starting to happen. In theory, saving each field on update worked fine. It meant that the user didn't have to click save buttons all the time (they are usually under heavy time constraints when using the system) and that nothing would be lost on page refreshes, or if the browser window was closed.

The problem with the design, though, was that it didn't take into account the asynchronous nature of AJAX calls (the first A in AJAX stands for Asynchronous). As the data handled by the system grew it was slowing down however slightly, which meant that requests were starting to get to the PHP backend at the same time causing several PHP processes to start adding data to the same draft.

[caption id="attachment_1541" align="aligncenter" width="756"]<a href="https://jesperjarlskov.dk/wp-content/uploads/2018/02/Asynchronous-race-condition.png"><img class="size-full wp-image-1541" src="https://jesperjarlskov.dk/wp-content/uploads/2018/02/Asynchronous-race-condition.png" alt="Request runs asynchronous, meaning we have a race condition" width="756" height="581" /></a> Request runs asynchronous, meaning we have a race condition[/caption]

As the diagrams (tries to) show two processes running side by side would request the same draft store from the database, they would then each add their own property to the draft bringing them out of sync. The first request would then save the original draft to the database including the new data property added in Request 1, and right after that the second request would save the same draft with the new data property from request 2, but excluding the property from Request 1, which did not exist when the draft was first read from the database.

This meant that when several requests hit the server at roughly the same time only the last one to save to the database would actually have its data saved.
<h2>The solution</h2>
The right solution here would be to make sure all data properties wasn't send one at a time, improving the design of both the frontend and the backend. But this would require a large refactoring of both subsystems including a lot of testing. Since the bug meant people were having a hard time doing their job, we did not have the luxury of this kind of time.

Instead, we implemented a queue in the frontend to make sure only one request would be sent at a time. This allowed us to implement a solution relatively quickly thus allowing sales to get back to work, so the business could continue running. First, make it work, then make it pretty.