---
title: 'Code reviews'
date: 2015-10-27
---

<h2>Background</h2>
I've recently taken part in introducing code reviews at my current workplace, so I'd like to spend some time here to tell a bit about why I believe code reviews is a good thing, what I believe code reviews can do for the code and for the development team, how we're currently doing code reviews and what a I believe a good code reviewer is looking for.

The aim of this post is not to be a checklist of how or what to do at code reviews, but rather to provide a broader perspective on some of the benefits of code reviews, and some broader points about what to look for.

<h2>Purpose of the code reviews</h2>
At first some people might get the feeling that they are being supervised, or that they aren't trusted. The point of code reviews isn't to point out how bad a developer you are, or to point out your flaws, it actually has very little to do with you as a person at all. A code review has two main purposes:
<ol>
<li>To improve the overall quality of the software</li>
<li>To improve the overall quality of the developers</li>
</ol>
The first point should be obvious. Better code means less bugs, which means less maintenance. This translates directly into better value for the customer, and more time for us as developers to build new exciting things, instead of maintaining old boring things.

The second point has two sides to it. First, there is a lot to learn from reading other people's code. They might do stuff in a way that you never thought about, using libraries you didn't know about. Even on the off chance that you have to do reviews for somebody you could never learn anything from at all, there is still a lot to be learned from condensing your current knowledge into useful actionable feedback.

<h2>Before a code review</h2>
It is important to prioritise code reviews, as having code reviews lying around waiting for reviews will both be a showstopper for the developer, and hold back the project. At the same time a code review will require you to disturb one of your colleagues, so please respect your colleagues' time; this means that you should prepare properly as to take as little of your colleague's time as possible away from his own project.

Be sure to read through your pull request again and make sure it is actually ready for review. Make sure everything is documented as expected, and that any debugging info is removed, so your colleague doesn't have to waste time rejecting your pull request because of something obvious like that.

Small pull requests are easier to grasp and review, so be sure to only include things that actually needs review in your pull request.

<h3>What needs to be reviewed</h3>
What needs to be reviewed is up to your organisation and project; the rule at our office is that if you wrote it, it needs to be reviewed. This might seem strict, but it saves everybody the trouble of thinking "is this actually a large enough change to require a code review?" since the answer is always yes. This saves us the trouble of having small bugs go into production because "it was just a minor change".

<h4>Exception: hotfixes</h4>
If a critical bug has been found which requires an immediate hotfix, we obviously won't be able to wait for a code review. In this case the hotfix will be made and deployed to solve the issue, the hotfix will then be code reviewed afterwards and fixed to live up to normal coding standards. This can be done in a more calm and considered fashion since the immediate problem has been fixed.

<h3>What doesn't need to be reviewed</h3>
Since the rule is "if you wrote it, it needs a review" it goes that if you didn't write it, it doesn't need a review.
We do a lot of sites in Drupal which makes it possible to deploy settings and configurations using a module called features. Features creates a PHP version of your configuration, since this is auto-generated we do not bother code reviewing this.

The same goes for community contributed code, like <a href="http://jesperjarlskov.dk/composer/">Composer</a> packages <a href="https://www.drupal.org/project/project_module">Drupal modules</a> since these are expected to be tested and vetted elsewhere and it is expected that the developer uses his critical sense before including these in a project.

<h2>The review</h2>
A code review is very similar to any other kind of text review, and should happen on 4 different levels using a top-down approach starting with the architectural thoughts and ending with the actual lines of code.

<ul>
<li><b>Architectural level:</b> A code review should start at the architectural level, which is the bird's eye view of the code. Does the structure make sense? Is the code modular? Testable? Performant? Maintainable? Does the chosen design patterns make sense for the problem at hand? Could the code be made more modular or maintainable by using more abstractions? Fewer abstractions? Are the concerns properly separated?</li>
<li><b>Functional level: </b> Jumping down a level we look at the individual functions. Does every function serve one, and only one, clear purpose? Is it obvious what that purpose is from the function name? Is the documentation descriptive? Do functions duplicate functionality from each other, or from built-in functions? Is the function itself readable and understandable?</li>
<li><b>Line level:</b> Does every line have a clear purpose? Are the lines compressed enough to allow you to keep an overview of the methods? Are they compressed to the point of unreadability? Are inline comments included? Are they necessary, or should they be replaced by properly named methods/functions/variables?</li>
<li><b>Word level:</b> At the lowest level we're looking at the words. Naming is known to be one of the hardest disciplines in computer science, so make sure the names makes sense now, and in 6 months when you come back to maintain the code. Does method names clearly describe what a method does, what it needs and what it's output should be? Does variables and properties tell you what might be in them?</li>
</ul>

<h2>Post code reviews</h2>
After the code review, the pull request is sent back to the developer to fix any issues and it will likely go back and forth a few times with the developer asking questions, fixing issues and questioning feedback.

It's important to note that the reviewer's word isn't set in stone, but should be considered feedback and suggestions. Everything is up for discussion, especially because there is no one right way to structure code. Again, the entire point of code reviews is to improve the quality and maintainability of the software, and learning from each others. Different developers have different strengths and skills, and bringing these together will both improve the software, the people developing it and the team.

<h2>Further reading</h2>
<ul>
<li>Kevin London <a href="http://kevinlondon.com/2015/05/05/code-review-best-practices.html">Code review best practices</a> - A more in-depth view of the actual code review, and suggestions on what to look for.</li>
<li>Smartbear <a href="http://www.ibm.com/developerworks/rational/library/11-proven-practices-for-peer-review/">11 best practices for peer code review</a> - Real life experiences condensed into a set of best practices. Many of them can be directly put into use, like the max size of a code review and how to articulate feedback.
</li>
</ul>