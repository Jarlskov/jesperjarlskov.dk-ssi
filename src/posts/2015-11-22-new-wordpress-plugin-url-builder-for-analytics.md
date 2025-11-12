---
title: 'New WordPress plugin: URL Builder for Analytics'
date: 2015-11-22
---

<strong>tl;dr:</strong> I've built a WordPress plugin for creating <a href="https://wordpress.org/plugins/url-builder-for-analytics/">Google Analytics tracking URLs</a> for your posts, straight in the post editor.  Development of the plugin happens on <a href="https://github.com/Jarlskov/url-builder-for-analytics">the GitHub repository</a>

<h2>Website Analytics in short</h2>
When doing website analytics, it can be really helpful to know which channels site visitors come from. If you use <a href="http://analytics.google.com/">Google Analytics</a> this can be specified easily, as a couple of GET parameters in the site URL. The main issue here is that remembering the names of the parameters can be bothersome, and writing them manually is error prone. That's why Google supplies a <a href="https://support.google.com/analytics/answer/1033867?hl=en">URL builder tool</a>, where you just fill out the fields and it'll give you an URL with the tracking parameters.

<h2>URL builder for Analytics</h2>
Since I was doing a bunch of tracking URLs for WordPress posts, I decided it would be helpful to just to it all straight from the WordPress interface. I couldn't find a proper plugin for it, so I decided to build my own, the <a href="https://wordpress.org/plugins/url-builder-for-analytics/">URL builder for Analytics</a>.

The plugin is pretty simple. It adds a meta box to the edit page of all post types. The meta box has 2 tabs, social sharing, and custom sharing.

<h3>Social Sharing</h3>
[caption id="attachment_1152" align="aligncenter" width="700"]<a href="http://jesperjarlskov.dk/wp-content/uploads/2015/11/screenshot-1.png"><img src="http://jesperjarlskov.dk/wp-content/uploads/2015/11/screenshot-1-1024x478.png" alt="The Social Sharing tab" width="700" height="327" class="size-large wp-image-1152" /></a> The Social Sharing tab[/caption]

Most of my URL sharing happened on various social channels, so to streamline that I made the social sharing tab, heavily inspired by <a href="http://linktagger.azurewebsites.net/">this linktagger tool</a> (thanks to <a href="http://www.afdeling18.dk/">Søren Sprogøe</a> for showing me that). Using the social sharing you just fill out your campaign name, and the plugin will give you links for various social media sites (Facebook, Twitter, Google+ and LinkedIn). The created links will have it's source set to the social site's name, and the medium will be "social", this ensures consistency for all posts shared to social media.

<h3>Custom sharing</h3>
[caption id="attachment_1155" align="aligncenter" width="700"]<a href="http://jesperjarlskov.dk/wp-content/uploads/2015/11/screenshot-2.png"><img src="http://jesperjarlskov.dk/wp-content/uploads/2015/11/screenshot-2-1024x652.png" alt="The Custom Sharing tabs" width="700" height="446" class="size-large wp-image-1155" /></a> The Custom Sharing tabs[/caption]

The other tab allows you to specify your own tracking values for all five of the parameters Google Analytics allows for it's tracking URLs. The plugin will then give you a full tracking URL ready to cut and paste into your newsletter, or where you'd want to use it.

<h2>Development and Next step</h2>
The development of the plugin is done on a <a href="https://github.com/Jarlskov/url-builder-for-analytics">GitHub repository<a>, which also hosts the current <a href="https://github.com/Jarlskov/url-builder-for-analytics/issues">issues and feature requests queue</a>.

The next thing I'd like to add is <a href="http://bit.ly">bit.ly</a> support. This would include doing OAuth authentication with the bit.ly service, allowing the use of the bit.ly URL shortening service, to shorten the URLs and saving them to your bit.ly account.

The plugin can be downloaded from the <a href="https://wordpress.org/plugins/url-builder-for-analytics/">WordPress repository</a>, and bug reports and feature requests are very welcome both in the <a href="https://github.com/Jarlskov/url-builder-for-analytics/issues">issues queue</a>, or in the comments below.