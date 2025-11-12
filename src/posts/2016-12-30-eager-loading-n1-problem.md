---
title: 'Lazy loading, eager loading and the n+1 problem'
date: 2016-12-30
---

In this article I'll look into a few data loading strategies, namely:
<ul>
<li><a href="#lazyloading">Lazy loading</a></li>
<li><a href="#eagerloading">Eager loading</a></li>
<li><a href="#lazyeagerloading">Lazy-eager loading</a></li>
</ul>

I'll talk a bit about the pros and cons of each strategy, and common issues developers might run into. Especially the n+1 problem, and how to mitigate it.

The examples in the post are written using PHP and Laravel syntax, but the idea is the same for any language.

<h2 id="lazyloading">Lazy Loading</h2>
The first loading strategy is probably the most basic one. Lazy loading means postponing loading data until the time where you actually need it.
 
<pre class="lang:php decode:true " title="Lazy loading in action" >
$users = App\User::get();
foreach ($users as $user) {
    $user-&gt;load('posts');
    echo $users-&gt;posts()-&gt;count();
}
</pre> 

Lazy loading has the advantage that you will only load the data you actually need. 

<h3>The n+1 problem</h3>
The problem is with lazy loading is what is known as the n+1 problem. Because the data is loaded for each user independently, n+1 database queries is required, where n is the number of users. If you only have a few users this will likely not be a problem, but this means that performance will degrade quickly when the number of users grows.

<h2 id="eagerloading">Eager Loading</h2>
In the eager loading strategy, data loading is moved to an earlier part of the code.

<pre class="lang:php decode:true " title="Eager loading in action">
$users = App\User::with('posts')-&gt;get();
foreach ($users as $user) {
    echo $user-&gt;posts-&gt;count();
}
</pre>

This will solve the n+1 problem. Since all data is fetched using a single query, the number of queries is independent of the number of items fetched.

One problem with eager loading is that you might end up loading more data than you actually need.

Often data is fetched in a controller and used in a view, in this case, the two will become very tightly coupled, and every time the data requirements of the view changes, the controller needs to change as well requiring maintenance in several different places.

<h2 id="lazyeagerloading">Lazy-eager loading</h2>
The lazy-eager loading combines both of the strategies above; loading of data is postponed until it is required, but it is still being prepared beforehand. Let's see an example.

<pre class="lang:php decode:true">
$users = App\User::get();
$users-&gt;load('posts');
foreach ($users as $user) {
    echo $users-&gt;posts-&gt;count();
}
</pre> 

As usual, a simple example like this only shows part of the story, but the `$users` list would usually be loaded in a controller, away from the iterator.

In this case, we're keeping related code close together which means you'll likely have fewer places to go to handle maintenance, but since the data is often required in templates we might be introducing logic into our templates giving the template more responsibilities. This can both make maintenance and performance testing harder.

<h2>Which strategy to choose?</h2>
This article has introduced three different data loading strategies, namely; <a href="#lazyloading">lazy loading</a>, <a href="#eagerloading">eager loading</a> and <a href="#lazyeagerloading">lazy-eager loading</a>.

I've tried to outline som pros and cons of each strategy, as well as some of the issues related to each of them.

Which strategy to choose depends on the problem you're trying to solve, since each strategy might make sense in their own cases.

As with most other problems I'd usually start by implementing whichever strategy is simplest and fastest to implement, to create a PoC of a solution to my problem. Afterwards I'd go through a range of performance tests, to see where the bottlenecks in my solution appears, and then look into which alternate strategy will solve that bottleneck.