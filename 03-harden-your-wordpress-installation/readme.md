# The Wordpress Security Handbook 2019 - Part 3: Harden your Wordpress installation

## Handbook Index

- [Part1: Protect files and database](https://github.com/lucagrandicelli/wordpress-security-handbook-2019/tree/master/01-protect-files-and-database)
- [Part2: Protect access and sessions](https://github.com/lucagrandicelli/wordpress-security-handbook-2019/tree/master/02-protect-access-and-sessions)
- [Part3: Part 3: Harden your Wordpress installation](https://github.com/lucagrandicelli/wordpress-security-handbook-2019/tree/master/03-harden-your-wordpress-installation)
---
## 16. Choose themes and plugins wisely

Plugins and themes are powerful external resources that can help you increase the functionality of your WordPress site. There are hundreds of thousands available in the official repository of WordPress and on other websites. Unfortunately not all of them are examined, thus constituting a serious security problem. Often, we do not perform in-depth checks on features and codes before installing a plugin, and an unverified one may cause security breaches and conflicts.

> Download only plugins and themes from the [WordPress repository](https://it.wordpress.org/plugins/) and from trusted sources.

So, before choosing your next plugin or theme, i'd like to give you some advice:

- take a look at the reviews, number of downloads and comments;
- check the date of the last update to find out if the development is still active;
- look for the author and check every other theme in the repository;
- check for compatibility problems with the software and your current installation.

Sometimes, it's just safe to perform a full backup before installing a bunch of plugins.

## 17. Remove any information about your WP installation

Another important thing, when it comes to WP security, is to hide as much informations as possible. For example, your current installed version is one of those info that can help an hacker to understand potential security holes, so by hiding this, attackers will not be able to exploit any known vulnerabilities associated with a specific version of WordPress.

You can delete information from the HTML header and static files by adding the following code to the _functions.php_ file of yours theme or within the utilities of your plugin:

```php
/*
Hide scripts and styles version
*/
function remove_wp_version_strings( $src ) {
    global $wp_version;
    parse_str( parse_url( $src, PHP_URL_QUERY ), $query );
    if ( !empty( $query['ver'] ) && $query['ver'] === $wp_version ) {
        $src = remove_query_arg( 'ver', $src );
    }
    return $src;
}
add_filter( 'script_loader_src', 'remove_wp_version_strings' );
add_filter( 'style_loader_src', 'remove_wp_version_strings' );

/*
Hide tag generator from headings
*/
function remove_wp_generator() {
    return '';
}
add_filter( 'the_generator', 'remove_wp_generator' );
```

You can also hide informations about the current version of WordPress by adding the following line of code to the **.htaccess** file inside the main WP folder:

```
# Block any WP info
<files readme.html>
Order allow,deny
Deny from all
</Files>
<files license.txt>
Order allow,deny
Deny from all
</files>
```

**Note**: although some WordPress security guides recommend to delete all of these files, my advice is to completely block access to them, since a new wordpress installation or update will generate new ones.

## 18. Hide PHP notices and warnings

Along with other strategies to limit the information provided to attackers, it is a good idea to hide error reports, which can provide valuable information to attackers, like the PHP and WordPress versions of your website, folders structure or server informations.

In development environments, error reports are useful for validating your job and find potential errors; however, on an active website, you should disable these logs to hide informations such as paths, names, versions and more.

To disable error reports in WordPress, simply add the following lines of code to the _wp-config.php_ file:

```php
error_reporting( 0 );
ini_set( 'display_errors', 0 );
```

## 19. Hide any PHP / Apache information

The last tip for hiding informations is to configure the headers sent by the server: they often contain information about the software installed and the running PHP version.

Depending on the installation, you must hide or limit the informations shared on the web server by adding the following line of code to the **.htaccess** file in the main folder:

```
ServerSignature Off
```

There are two ways to hide informations about the PHP version of your website that some servers send in the HTTP header. First, add the following code to the **.htaccess** file:

```
Header unset X-Powered-By
```

Or use the following directive in the _php.ini_ file:

```
expose_php = Off
```

**Note**: it is generally possible to add this line of code to the currently active _php.ini_ file using the server administration panel, but the procedure
may vary depending on your hosting service.

## 20. Keep your Wordpress up to date

In order to protect your website from known security vulnerabilities, you should always use the latest available version of WordPress, keep the installed plugins updated and update your themes.

Usually, keeping your website updated manually is preferrable, even if it requires more attention and time, because it allows you to check the included features in each update and their purpose. Regarding the order of updates, I always recommend updating the Wordpress core first, then update plugins and themes without worrying about the order.

If, however, you want the WordPress core to be updated automatically, just add the following line of code to your _wp-config.php_ file:

```php
define( 'WP_AUTO_UPDATE_CORE', true );
```

**Note**: automatic update will not be performed if you have disabled the WordPress **cron** service.

After each update you will receive an email at the email address used for the platform administrator account.

The WordPress core update is only a part of the equation: according to a [wpscan.org](wpscan.org) report, 52% of the vulnerabilities found in the installations of WordPress are due to plugins, 11% to themes and 37% to the core of WordPress.

If you want to update the plugins automatically, add the following line to
code to the _functions.php_ file of your active theme:

```php
add_filter( 'auto_update_plugin', '__return_true' );
```

Before adding this code, delete all unused plugins on your website. Simple deactivation is not enough to remove potentials vulnerabilities: eliminate them!

Adding the following line of code will automatically update the themes:

```php
add_filter( 'auto_update_theme', '__return_true' );
```

Finally, remember that it is fine to keep your WordPress site safe, but you should not forget that your computer should also be protected by viruses and malicious software. Make sure you use a reliable and secure antivirus, keeping your operating system up to date.

## 21. Choose a good hosting company

The last safety advice, although it should be the first in terms of importance, is to choose a secure server to host your web project.

Your hosting provider should offer you a secure platform and keep the security of your infrastructure active. Beware of hosting services that use obsolete software, don't protect access and employ technical support with little knowledge of WordPress.

**Choosing a good hosting provider is crucial for security, performance and interior peace :)**
