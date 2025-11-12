---
title: 'PHP 7 has finally arrived'
date: 2015-12-04
---

After about 2 years of work, and a few postponements, <a href="http://php.net/archive/2015.php#id2015-12-03-1">PHP 7 has finally been released</a>. I've previously written at length about <a href="http://jesperjarlskov.dk/php-7-whats-up-and-whats-new/">PHP 7 new features and enhancements</a> but the short version is:
<ul>
<li>Improved performance: PHP 7 is up to twice as fast as PHP 5.6</li>
<li>Significantly reduced memory usage</li>
<li>Many fatal errors converted to Exceptions</li>
<li>Secure random number generator</li>
<li>Removed old and unsupported SAPIs and extensions</li>
<li>Return and Scalar Type Declarations</li>
</ul>

<h2>What about the major projects, are they ready?</h2>
<a href="https://www.drupal.org/8">Drupal 8</a> was recently released, and the core should have good enough <a href="https://www.drupal.org/node/2454439">PHP 7 support</a> for most people, so you can start playing around with that if you are so inclined.

<a href="https://make.wordpress.org/core/2015/09/10/wordpress-and-php7/">WordPress</a> has been trying to catch up as well, but I'm not too certain about their current status. I would expect the WordPress core to be running on PHP 7 very soon, if it doesn't already, but I could imagine a lot of plugins having issues.

All <a href="http://symfony.com/blog/symfony-achieves-100-php7-compatibility">maintained Symfony branches</a> has had their test suites passing for a few months, so if your a Symfony developer your main concern should be your own additions.

The Laravel Homestead Vagrant box has also <a href="http://laravel.com/docs/5.1/homestead#upgrading-to-php-7">supported PHP 7</a> for quite a while, so I would also expect Laravel core to be running fine on the new version, even though I havn't found anything official about it.

<h2>Trying out PHP 7</h2>
If you want try out PHP 7 before you run out and upgrade all of your live servers (this is a good idea&trade;) there's a few ways to do that.

You could of course just <a href="http://php.net/downloads.php">download it</a> and install it from source.

If you're running Debian, PHP 7 is already available on the <a href="https://www.dotdeb.org/2015/12/04/php-7-0-0-is-available-for-jessie/">Dotdeb repositories</a>.

As I mentioned it is also <a href="http://laravel.com/docs/5.1/homestead#upgrading-to-php-7">available on Homestead</a> if you're developing with Laravel using Vagrant. Alternatively Rasmus Lerdorfs <a href="https://github.com/rlerdorf/php7dev">php7dev Vagrant box</a> also supports a range of PHP versions, including PHP 7.

The <a href="https://puphpet.com/">puphpet Vagrant box builder</a> is sadly still using the PHP 7 nightlies, but I'm hoping that they will upgrade to the final release soon.