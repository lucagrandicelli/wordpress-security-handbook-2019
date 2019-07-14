# The Wordpress Security Handbook 2019 - Part 1: Protect your files and database

**Disclaimer**: _This handbook is a translation of the guide written by Fernando Puente for the Siteground Hosting Provider_.

## A brief introduction

Wondering if WordPress is safe? **The answer is yes**.

WordPress uses the latest security technologies. Moreover, it is continuously updated with new versions, which often include several vulnerability patches. Finally, WordPress is monitored and run by a large community of developers to whom security is deeply at heart.

**So why a Security Handbook?**
Because the protection of your website should be a continuous activity, which implies, among other ones, creating barriers against aggressors, the failure prevention, monitoring of changes, opposition to malicious access and concealment of sensitive information.

[WordPress is used in more than 30% of websites](https://w3techs.com/technologies/history_overview/content_management/all/y): just because of this popularity it is a target of cyber criminals and also a platform in continuous development, open to changes and additions with third parts.

So let's start building our handbook and remember: _When it comes to security, paranoia is your best friend_.

---

**This article is not intended to be exhaustive of all situations and enviroments nor a definitive guide to your website security, thus is subject to change. Each situation should be carefully reviewed and analyzed with a security expert.**

---

## 1. Before installing Wordpress

When creating a new WordPress installation, you should always choose the [latest stable version](https://wordpress.org/download/). But before installing, please follow these two simple security steps:

1. **Change the DB table prefix**
2. **Use authentication keys**

**How to change DB table prefix**

By default all WordPress installations use the table prefix **wp\_** in their database. I suggest you to change this to a more complex string, preferably random such as, for example: **3Fd9\_**

> _Remember that this security step in meant to prevent possible attacks on your database._

To change the WordPress table prefix, edit the following line in the **wp-config.php** configuration file by replacing the default prefix with the one you chosen (i suggest you to use a generated random string. Use an online tool like [random.org](https://www.random.org/strings/)):

```php
// Original table prefix.
$table_prefix = 'wp_';

// change it to (es.):
$table_prefix = '3Fd9_'; // This is just an example. Use the prefix you like.
```

Moreover, thanks to this change, you will be able to run different WordPress installations in the same database, provided that you do not repeat the prefix.

**Note:**

> _If you have already installed your website and haven't changed the default prefix during the installation process, it is not too late. Use a plugin like [Change Table Prefix](https://it.wordpress.org/plugins/change-table-prefix/) to make the change. It is also possible to change the prefix manually, but DON'T DO IT if you are not familiar with the database._

**How to use authentication keys**

WordPress uses secret keys, called _Keys_ and _Salt_, stored in the file **wp-config.php**: they protect open sessions by encrypting session data in the browser cookie.

Although it is possible to manually generate these keys, i suggest you use the official WordPress service on the page [https://api.wordpress.org/secret-key/1.1/salt/](https://api.wordpress.org/secret-key/1.1/salt/) and replace the keys with those in your wp-config.php file.

**Note:**

> _Just like the database prefix, you can change the keys at any time, even on an existing website: it is better to run this regularly operation to invalidate active sessions and force all users to log in again._

**But before moving on, a little trick:**

In the unlikely event that you should deny any access to the admin panel, even through the login credentials, you can configure the invalidation of the keys every microsecond by inserting the following code in **wp-config.php**:

```php
define('AUTH_KEY', microtime());
define('SECURE_AUTH_KEY', microtime());
define('LOGGED_IN_KEY', microtime());
define('NONCE_KEY', microtime());
define('AUTH_SALT', microtime());
define('SECURE_AUTH_SALT', microtime());
define('LOGGED_IN_SALT', microtime());
define('NONCE_SALT', microtime());
```

Just remember to periodically update these keys as a preventive measure or to end active sessions.

## 2 - After installing Wordpress

Once the installation of your new WordPress site is complete, you should delete the administrator profile used during installation, create a new user with administrator permissions and all the user accounts you need.

Avoid weak usernames like "admin" or "administrator" (common in all WordPress installations) and remember to use a complex password. Disable pingback and trackback notifications on your admin panel (Settings > Comments), as they could constitute an access for Possible **DDoS** (**D**istributed **D**enial **o**f **S**ervice) attacks on your website.

Protect your files from attacks and intrusions by adding the following lines of code in the **.htaccess** file, ideally at the beginning of the file in the root directory of your website:

```
#Deny folder listing
Options -Indexes

#Block access to sensitive files
<files .htaccess>
Order allow,deny
Deny from all
</files>
<files wp-config.php>
Order allow,deny
Deny from all
</files>
```

You should also block access to all unnecessary files by **creating a new file .htaccess in the /wp-admin folder** and adding the following lines of code:

```
#Block installation file
<files install.php>
Order allow,deny
Deny from all
</files>
<files setup-config.php>
Order allow,deny
Deny from all
</files>
```

Finally, have also a look at the **robots.txt** file located in the root folder of yours website. Check that it shows no sensitive informations about your WordPress installation, for example your administration folder _wp-admin_.

## 3. Change files and folders permissions

Make sure the files and folders in your WordPress installation have the appropriate permissions to prevent attackers to take control of your website.
You can change the permissions via an FTP client or via the panel administration provided by your web hosting service.

The following are commonly accepted as secure values:

- All _folders_ should be set **755**.
- All _files_ should be set **644**.
- _wp-config.php_ should be set **600**
- _.htaccess_ should be set **604**

## 4. Block PHP execution in your folders

Although WordPress installations block PHP file uploads by default through the administration panel, you should also block the ability to run PHP code in folders. Also, you should limit the execution of unnecessary PHP code in other folders used by WordPress which should not be accessed directly.

Create a new .htaccess file in your **/wp-content/uploads** folders, **/wp-content/plugins** and **/wp-content/themes** and add the following lines of code to block PHP executions:

```
<Files *.php>
deny from all
</Files>
```

## 5. Disable file editing in Wordpress

This procedure aims to add a level of security to the administration panel, in order to prevent undesirable intrusions and limit errors made by authorized users. To disable the file editing option in the admin panel of WordPress, use the following line of code in the configuration file **wp-config.php**:

```php
define( 'DISALLOW_FILE_EDIT', true );
```

This code is equivalent to removing the "**edit_themes**" permissions,
"**edit_plugins**" and "**edit_files**" for any registered user on the website.

You can add an additional level of control for active websites if you don't want users to install themes and plugins on their own. Just enter the following code to the **wp-config.php** configuration file:

```php
define( 'DISALLOW_FILE_MODS', true );
```

Remember to disable the command by changing the directive to "false" if you have to perform WordPress installation tasks. All changes to the **wp-config.php** file must be inserted above the following line of code:

```php
/* That's all, stop editing! Happy publishing. */
```

## 6. Use a CDN as DNS

Although we already know the advantages of a **CDN** (**C**ontent **D**elivery service **N**etwork) to improve the performance of your website, the use of a CDN as DNS service (this before your web server) can improve security in the following three ways:

- Enables an active firewall that is continuously updated against malicious behavior such as massive connections or tracking ports;

- Prevents brute force attacks using the distributed server network of providers, which minimize the impact and apply blocking rules to detect this type of attacks, usually DoS or DDoS;

- It hides the real IP of your server, preventing direct attacks against yours website thanks to the masking of the real IP in which your website is hosted.

## 7. Backup. Backup. Backup

Although i hope you never get to use this suggestion, it's better to be safe than sorry: make a complete backup of your website.

You rarely need to restore a full backup, but if you have to do so, check if your hosting provider already has some tool that can help you through.

There a special rule called "**The 3-2-1 Backup Rule**" when it comes to backup of sensitive data:

- **Keep 3 backups;**
- **In 2 different formats (minimum);**
- **1 of the backups should be in a different physical location.**

In an emergency situation, it is useless to have all the backups in the same format or in the same position. Remember to always generate a new backup after having made major changes to your WordPress installation.
