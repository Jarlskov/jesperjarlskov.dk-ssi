---
title: 'Laravel file logging based on severity'
date: 2017-02-23
---

Per default anything logged in a Laravel application will be logged to the file: <code>storage/logs/laravel.log</code>, this is fine for getting started, but as your application grows you might want something that's a bit easier to work with.

Laravel comes with a couple of different loggers:
<div>
<dl>
<dt><b>single</b></dt><dd>Everything it logged to the same file.</dd>
<dt><b>daily</b></dt><dd>Everything is logged to the same file, but the file is rotated on a daily basis so you have a fresh file every day</dd>
<dt><b>syslog</b></dt><dd>Log to syslog, to allow the OS to handle the logging</dd>
<dt><b>errorlog</b></dt><dd>Pass error handling to the web server</dd>
</dl>
</div>

Having different handlers included provides some flexibility, but sometimes you need something more specific to your situation. Luckily Laravel has outsourced it's logging needs to <a href="https://github.com/Seldaek/monolog">Monolog</a>, which provides all the flexibility you could want.

In our case, logging to files was enough, but having all of our log entries in one file made it impossible to find anything. Monolog implements the <a href="https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md">Psr-3 specification</a> which defines 8 severity levels, so we decided to split our logging into one dedicated file per severity level.

To add your own Monolog configuration, you just call <a href="https://laravel.com/api/5.1/Illuminate/Foundation/Application.html#method_configureMonologUsing"><code>Illuminate\Foundation\Application::configureMonologUsing()</code></a> from your <code>bootstrap/app.php</code>.

To add a file destination for each severity level, we simply loop through all available severity levels and assign a <a href="https://github.com/Seldaek/monolog/blob/master/src/Monolog/Handler/StreamHandler.php">StreamHandler</a> to each level.

<pre>
// Monolog writes to the first handler which accepts the given severity, so we flip the levels array, to register the highest severities first.
$severities = array_flip(\Monolog\Logger::getLevels());
foreach ($severities as $severity) {
    $lowercase = strtolower($severity);
    $file = "/logs/laravel_$lowercase.log";

    // Create a stream handler for each severity. Set a line formatter to keep the pretty output that we've come to know and love.
    $handler = new Monolog\Handler\StreamHandler(storage_path($file), constant("Monolog\Logger::$severity"), false);
    $handler->setFormatter(new \Monolog\Formatter\LineFormatter(null, null, true, true));

    $monolog->pushHandler($handler);
}
</pre>

Simple as that.

Later we decided to also send all errors should also be sent to a Slack channel, so we could keep an eye on it, and react quickly if required. Luckily Monolog ships with a <a href="https://github.com/Seldaek/monolog/blob/master/src/Monolog/Handler/SlackHandler.php">SlackHandler</a>, so this is easy as well.

<pre>
$slackhandler = new \Monolog\Handler\SlackHandler(env('SLACK_API_TOKEN'), '#errors');
$slackhandler->setLevel(\Monolog\Logger::ERROR);

// The slackhandler should allow the log event to bubble, so we don't prevent the log file logger to work.
$slackhandler->setBubble(true);

$monolog->pushHandler($slackhandler);
</pre>

So with just a few registered handlers, we've tailored our error logging to a level that we feel suits our needs at the current time.

<pre>
$app->configureMonologUsing(function($monolog) {
    // Monolog writes to the first handler which accepts the given severity, so we flip the levels array, to register the highest severities first.
    $severities = array_flip(\Monolog\Logger::getLevels());
    foreach ($severities as $severity) {
        $lowercase = strtolower($severity);
        $file = "/logs/laravel_$lowercase.log";

        // Create a stream handler for each severity. Set a line formatter to keep the pretty output that we've come to know and love.
        $handler = new Monolog\Handler\StreamHandler(storage_path($file), constant("Monolog\Logger::$severity"), false);
        $handler->setFormatter(new \Monolog\Formatter\LineFormatter(null, null, true, true));

        $monolog->pushHandler($handler);
    }

    if (!config('app.debug')) {
        $slackhandler = new \Monolog\Handler\SlackHandler(env('SLACK_API_TOKEN'), '#errors');
        $slackhandler->setLevel(\Monolog\Logger::ERROR);
        // The slackhandler should allow the log event to bubble, so we don't prevent the log file logger to work.
        $slackhandler->setBubble(true);
        $monolog->pushHandler($slackhandler);
    }
}
</pre>

Notice how we only log to Slack when we're not in debug mode, to filter out any errors thrown while developing.