---
title: 'Upgrading WordPress from the terminal'
date: 2018-02-26
---

<a href="https://codex.wordpress.org/Version_3.7">WordPress 3.7</a> introduces <a href="https://codex.wordpress.org/Configuring_Automatic_Background_Updates">automatic updates</a> which is a great way for most people to keep their WordPress sites updated, without having to remember to do it manually. For some people having live sites update automatically without testing or running backup scripts first might not be a good solution, though.

Luckily there is an alternative to the built-in automatic updates that don't require having to do all updates through the often sluggish web interface. <a href="https://wp-cli.org/">WP-CLI</a> allows handling a lot of WordPress maintenance tasks from the terminal including installing new themes and plugins or updating already installed versions.

The WP-CLI tool makes it easy to update your WordPress installation in 3 easy steps.

Update the WordPress core:
<pre><code>wp core update</code></pre>

Update installed plugins:
<pre><code>wp plugin update --all</code></pre>

Update installed themes:
<pre><code>wp theme update --all</code></pre>

This is easily combined to a one-line command
<pre><code>wp core update && wp plugin update --all && wp theme update --all</code></pre>

Adding the line
<pre><code>alias wpupdate='wp core update && wp theme update --all && wp plugin update --all'</code></pre>
to your <code>.bashrc</code> allows you to easily cd to the root of your WordPress project and update everything by just running the command <code>wpupdate</code>.

This would allow you to easily update WordPress in your test/staging environment, test the updates, and push the changes to your internal versioning system, before deploying a new tested version of your websites.

You could also include it in your normal maintenance routines together with your backup scripts and automated test cases if you want automatic updates which are still easy to roll back.