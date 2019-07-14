# The Wordpress Security Handbook 2019 - Part 2: Protect access and sessions

## 8. Always use HTTPS

The HTTPS protocol creates a secure connection between the users and the server, eliminating possible _Man-in-the-Middle_ attacks (MITM). These attacks occur when an intermediate service alters or acquires information exchanged between two parties. That's because HTTPS encryption is used for all sensitive data.

> If you're still not sure about using HTTPS, [read this](https://letsencrypt.org/).

In order to use this protocol on your website, you must install an SSL certificate on your web server and change the URL in the admin panel. There are plenty of services and even free certificates provided with many hosing providers. One for all: [Let's encrypt SSL Certificate](https://letsencrypt.org/)

There are also several WordPress plugins that can force an HTTPS connection on all the resources of your website to avoid warnings or errors during the display of HTTP and HTTPS content on the same page. Finally, you will have to force every new session in the website admin panel to use SSL protocol.

To do so, just enter the following code in the wp-config.php file:

```php
define('FORCE_SSL_LOGIN', true);
define('FORCE_SSL_ADMIN', true);
```

> **Note**: _remember that you need to have an active SSL in the installation such as, for example, the one provided by [Let's Encryption](<(https://letsencrypt.org/)>)_

## 9. Disable session suggestions

As mentioned above, but it's worth repeating, a good security policy is always to "give" as little information as possible to the aggressors. This tip will help you to minimize the possible access to your website by disabling suggestions from the login page, which appear by default if the username or password are not correct:

```php
function no_wordpress_login_errors() {
    return "This website is protected.";
}
add_filter( 'login_errors', 'no_wordpress_login_errors' );
```

## 10. Change login access URL

Let's face it: many attacks on websites do often target the login page. This is because bots are programmed to recognize a WordPress installation, add the path _/wp-admin_ and access the administration area. Changing this default URL trunk will add an additional level of difficulty for the aggressors.

In the [WordPress repository](https://it.wordpress.org/plugins/) there are several plugins that allow you to change the location of your login page, for example _mydomain.com/mynewadminurl_.

The [WPS plugin Hide Login](https://it.wordpress.org/plugins/wps-hide-login/) is one of the most common used systems to perform this task. However, there are other plugins for this purpose and even many security systems also include this feature.

## 11. Limit login attempts

You can configure your website to block access to login page for a few minutes, a few hours or forever in the event a user entered incorrect login credentials a given number of times. In this way, bots will struggle to gain access through brute force attacks

Generally, security plugins like [Wordfence](https://www.wordfence.com/) include this functionality, as well as the following ones:

- [Limit Login Attempts (miniorange)](https://it.wordpress.org/plugins/miniorange-limit-login-attempts/)
- [Limit Login Attempts Reloaded](https://it.wordpress.org/plugins/limit-login-attempts-reloaded/)
- [Loginizer](https://it.wordpress.org/plugins/loginizer/)

> **Some firewall plugins also include this feature, such as [All In One WP Security & Firewall](https://it.wordpress.org/plugins/all-in-one-wp-security-and-firewall/).**

## 12. Use a firewall plugin

A firewall is software that provides an additional layer of security and is able to protect web connections or WordPress installations by detecting and analyzing connections inbound. Firewall plugins are very effective and easy to manage, since the configuration takes place from a single area. They generally include a _WAF_ (Web Application Firewall), a tool that analyzes and blocks attacks in real time.

There are three main firewall plugins at the moment:

- [All In One WP Security & Firewall](https://it.wordpress.org/plugins/all-in-one-wp-security-and-firewall/)
- [Wordfence](https://www.wordfence.com/) (_Please pay attention to the Live Traffic feature, which can cause a server overload and leave you without service_)
- [Better WP Security](https://it.wordpress.org/plugins/better-wp-security/)

Security features can vary depending on the installed plugin, but generally the following features are always available:

- Scan files to look for changes, errors and viruses
- WAF firewall that detects and blocks malicious visits
- Real-time traffic display
- A tool to block access to the website via IP
- Captcha for the WordPress login page and a feature that limits login attempts
- Password verification
- Two-factor verification to access the WordPress administration panel
- Possibility to block specific countries
- A tool to verify file and folder permissions

## 13. Use security headers

Improve your website security by implementing a series of HTTP headers integrated into the web server and sent to the browser.

Start with the **X-Frame-Options** heading, which prevents pages from opening in an external frame or iframe, thus preventing clickjacking attacks (literally "click theft") on your website, a technique that leads users to disclose confidential information on a seemingly normal website.

By adding the following line of code to your _.htaccess_ file, your website will tell the browser that frames can only be opened from the same domain or from the same domain origin:

```
Header set X-Frame-Options SAMEORIGIN
```

If your website integrates third-party services, you can specify that you deny access to the remaining ones. For example:

```
Header set X-Frame-Options "ALLOW-FROM https://example.com/"
```

Increase the protection of your website from XSS (cross-site scripting) attacks on older browsers by adding the following line of code to your _.htaccess_:

```
Header set X-XSS-Protection "1; mode=block"
```

In order to reduce the risk of XSS attacks, use the content-security-policy of the browser, which specifies what content on the website or third parties can dynamically load. For example, if you want your website to only accept content from same domain, add the following line of code to your _.htaccess_ file:

```
Header set Content-Security-Policy "default-src 'self';"
```

In this way you will block the loading of scripts from external sources. For example, to change the variables for your specific project, use the following line of code to allow third-party scripts like Google Analytics:

```
header set Content-Security-Policy "script-src 'self' www.google-analytics.com;"
```

You should be careful when implementing this header because it is easy to block needed resources. I recommend to perform various tests with this header in a separate browser tab to check for any errors on the terminal.

> **Note**: _if you previously included the x-content-security-policy header in your server and has become obsolete, you will have to delete it because if you use both headers simultaneously you may have problems._

The fourth header that you can use to increase security is _X-content-type-options_, which protects your website from loading unwanted styles and scripts when the expected MIME types do not match those declared on the page. To add this protection, use the following line of code in your _.htaccess_ file:

```
Header set X-Content-Type-Options "nosniff"
```

## 14. Prevent XML-RPC attacks

The **xmlrpc.php** file is used by some applications and software to communicate with WordPress; among these, the WordPress app or email clients like Outlook and Thunderbird that allow the "Publish articles via email" feature. Also plugins like Jetpack or JSON API use the XMLRPC file for some of their functionality.

You can completely deny access to the xmlrpc.php file by modifying its rules in the _.htaccess_ file or eliminating it directly if you are sure you don't need it. To deny access via ._htaccess_, add the following lines of code to the file:

```
# Deny access to XMLRPC.
<Files xmlrpc.php>
Order Deny,Allow
Deny from all
</Files>
```

It is also possible to deny access to XMLRPC using plugins such as [Disable XML-RPC](https://it.wordpress.org/plugins/disable-xml-rpc/) or [iThemes Security](https://it.wordpress.org/plugins/better-wp-security/), mentioned in point 14.

For those who absolutely need this API feature, the best solution is to enable it only from the IP where you need to access and deny others. In this specific case, add the following lines of code to the file _.htaccess_, replacing the IP with the one from which you want to gain access:

```
<Files xmlrpc.php>
order deny, allow
deny from all
allow from X.X.X.X
</Files>
```

## 15. Disable JSON Rest API

If none of your plugins use the REST API, you can easily disable it. Just add the following lines of code to the _functions.php_ file of your active theme or use a plugin: since WordPress version 4.4, REST APIs are included in the central core, allowing any developer to interact with the website. This allowed WordPress to reach a greater number of developers who do not have familiar with the platform, but at the same time left an open door to possible attacks, in particular DDoS attacks.

The following code blocks scripts from loading from external sources.

```php
add_filter('json_enabled', '__return_false');
```

If you prefer not to tinker with the code, you can use the iThemes Security plugin, mentioned in point 12, or firewall plugins, which will keep the REST APIs active but will allow access only to users with exclusive permissions.
