---
title: 'HTTPS and HTTP/2 with letsencrypt on Debian and nginx'
date: 2017-02-09
---

<ul>
<li><a href="#introduction">Introduction</a></li>
<li><a href="#letsencrypt">Letsencrypt</a></li>
<li><a href="#nginx">NGINX</a></li>
<li><a href="#security">Improving HTTPS security</a></li>
<li><a href="#benchmarking">Benchmarking your setup</a></li>
</ul>

<h2 id="introduction">Introduction</h2>
Most web servers today run HTTP/1.1, but HTTP2 support is growing. I've written more in-depth about the advantages of <a href="https://jesperjarlskov.dk/http2-what-and-why/">HTTP/2</a>. Most browsers which support HTTP2 only supports it over encrypted HTTPS connections. In this article, I'll go through setting up NGINX to serve pages over HTTPS and HTTP/2. I'll also talk a bit about tightening your HTTPS setup, to prevent common exploits.

<h2 id="letsencrypt">Letsencrypt</h2>
HTTPS security utilises public-key cryptography to provide end-to-end encryption and authentication to connections. The certificates are signed by a certificate authority, which can then verify that the certificate holder is who he claims to be.

<a href="https://letsencrypt.org/">Letsencrypt</a> is a free and automated certificate authority who provides free certificate signing, which has historically been a costly affair.

Signing and renewing certificates from Letsencrypt is done using their certbot tool, this tool is available in most package managers which mean it's easy to install. On Debian Jessie, just install the certbot like anything else from apt:

<pre class="lang:sh decode:true">aptitude install certbot -t jessie-backports</pre> 

Using certbot you can start generating new signed certificates for the domains of your hosted websites:

<pre class="lang:sh decode:true">certbot certonly -w <WEBROOT> -d <DOMAIN></pre>

For example

<pre class="lang:sh decode:true">certbot certonly -w /var/www/example.com -d example.com</pre>

Letsencrypt certificates are valid for 90 days, after which they must be renewed by running

<pre class="lang:sh decode:true">certbot renew</pre>

To automate this process, you can add this to your crontab, and make it run daily or weekly or whatever you prefer. In my setup it runs twice daily:

<pre>0 0,12 * * * certbot renew --quiet</pre>

Note that Letsencrypt currently doesn't support wildcard certificates, so if you're serving your website from both <code>example.com</code> and <code>www.example.com</code> (you probably shouldn't), you need to generate a certificate for each.

<h2 id="nginx">NGINX</h2>
NGINX is a free open source asynchronous HTTP server and reverse proxy. It's pretty easy to get up and running with Letsencrypt and HTTP/2, and it will be the focus of this guide.

<h3>HTTPS</h3>
To have NGINX serve encrypted date using our previously created <a href="#letsencrypt">Letsencrypt certificate</a> we have to ask it to listen for HTTPS connections on port 443, and tell it where to find our certificates.

Open your site config, usually found in <code>/etc/nginx/sites-available/&lt;site&gt;</code>, here you'll probably see that NGINX is currently listening for HTTP-connections on port 80:

<pre>listen 80 example.com;</pre>

So to start listening on port 443 as well, we just add another line:

<pre>listen 443 ssl example.com;</pre>

The domain at the end would, of course, be the domain that your server is hosting.

Notice that we've added the <code>ssl</code> statement in there as well.

Next, we need to tell NGINX where to look for our new certificates, so it knows how to encrypt the data, we do this by adding

<pre>
ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
</pre>

With the default Letsencrypt settings this would translate into something like:

<pre>
ssl_certificate <CERT_DIR>/example.com.crt;
ssl_certificate_key <CERT_DIR>/example.com.key;
</pre>

And that's really all there is to it.

<h3>HTTP/2</h3>
Enabling HTTP/2 support in NGINX is even easier, just add an <code>http2</code> statement to each <code>listen</code> line in your site config. So:

<pre>listen 443 ssl example.com;</pre>

Turns into:

<pre>listen 443 ssl http2 example.com;</pre>

Now test out your new, slightly more secure, setup:

<pre>
nginx -t
</pre>

If all tests pass, restart NGINX to publish your changes.

<pre>
service nginx restart
</pre>

Now your website should also be available using the <code>https://</code> scheme... unless the port is blocked in your firewall (that could happen to anybody). If your browser supports HTTP2, your website should also be served over this new protocol.


<h2 id="security">Improving HTTPS security</h2>
With the current setup, we're running over HTTPS, which is a good start, but a lot of exploits have been discovered in various parts of the implementation, so there are a few more things we can do to harden the security. We do this by adding some additional settings to our NGINX site config.

Firstly, old versions of TLS is insecure, so we should force the server to not revert:

<pre>ssl_protocols TLSv1.2;</pre>

If you need to support older browsers like IE10, you need to turn on older versions of TLS;

<pre> ssl_protocols TLSv1 TLSv1.1 TLSv1.2;</pre>

This in effect only turns off SSL encryption, it's not optimal, but sometimes you need to strike a balance, and it's better than the NGINX default settings. You can see which browsers supports which TLS versions on <a href="http://caniuse.com/#search=tls">caniuse</a>.

When establishing a connection over HTTPS the server and the client negotiates which encryption cypher to use. This has been exploited in some cases, like in the <a href="https://blogs.msdn.microsoft.com/kaushal/2011/10/03/taming-the-beast-browser-exploit-against-ssltls/">BEAST exploit</a>. To lessen the risks, we disable certain old insecure ciphers:

<pre>
ssl_ciphers EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
ssl_prefer_server_ciphers On;
</pre>

Normally HTTPS certificates are verified by the client contacting the certificate authority. We can turn this up a bit by having the server download the authority's response, and supply it to the client together with the certificate. This saves the client the roundtrip to the certificate authority, speeding up the process. This is called <a href="https://en.wikipedia.org/wiki/OCSP_stapling">OCSP stapling</a> and is easily enabled in NGINX:

<pre>
ssl_trusted_certificate /etc/letsencrypt/live/<DOMAIN>/chain.pem;
ssl_stapling on;
ssl_stapling_verify on;
</pre>

Enabling HTTPS is all well and good, but if a man-in-the-middle (MitM) attack actually occurs, the perpetrator can decrypt the connection from the server, and relay it to the client over an unencrypted connection. This can't really be prevented, but it is possible to instruct the client that it should only accept encrypted connections from the domain; this will mitigate the problem anytime the clients visits the domain after the first time. This is called <a href="https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security">Strict Transport Security</a>:

<pre>
add_header Strict-Transport-Security "max-age=31557600; includeSubDomains";
</pre>

<blockquote>
<strong>BE CAREFUL!</strong> with adding this last setting, since it will prevent clients from connecting to your site for one full year if you decide to turn off HTTPS or have an error in your setup that causes HTTPS to fail.
</blockquote>

Again, we test our setup:
<pre>
nginx -t
</pre>

If all tests pass, restart NGINX to publish your changes (again, consider if you're actually ready to enable Strict Transport Security).

<pre>
service nginx restart
</pre>

Now, to test the setup. First we run an <a href="https://www.ssllabs.com/ssltest">SSL test</a>, to test the security of our protocol and cipher choices, the changes mentioned here improved this domain to an <a href="https://www.ssllabs.com/ssltest/analyze.html?d=jesperjarlskov.dk">A+ grade</a>.

We can also check our <a href="https://securityheaders.io">HTTPS header security</a>. Since I still havn't set up <a href="https://developers.google.com/web/fundamentals/security/csp/">Content Security Policies</a> and <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Public_Key_Pinning">HTTP Public Key Pinning</a> I'm sadly stuck down on a <a href="https://securityheaders.io/?q=jesperjarlskov.dk&followRedirects=on">B grade</a>, leaving room for improvement.

<h2>Further reading</h2>

<ul>
<li>More on the <a href="https://www.troyhunt.com/i-wanna-go-fast-https-massive-speed-advantage/">speed advantages of HTTP2</a></li>
<li>Many reject HTTPS because it's so much slower than unencrypted HTTP so we ask: <a href="https://istlsfastyet.com/">Is TLS fast yet?</a></li>
<li>Setting up Letsencrypt Certbot on <a href="https://certbot.eff.org/#debianjessie-nginx">Debian Jessie</a>.</li>
<li>Enabling HTTPS and HTTP/2 in NGINX <a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-with-http-2-support-on-ubuntu-16-04">NGINX setup</a>.</li>
<li><a href="https://letsecure.me/secure-web-deployment-with-lets-encrypt-and-nginx/">Additional SSL settings</a></li>
</ul>