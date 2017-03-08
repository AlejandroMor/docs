==================
nethserver-webtop5
==================

WebTop 5 is a full-featured groupware written in Java.

It's composed by three parts:

* Java web application running on Tomcat 7
* PHP implementation of Active Sync protocol
* PostgreSQL database

Access to web application is forced in SSL mode.

Database
========

Configuration is saved in ``webtop`` key inside ``configuration`` database.

Available properties:

* ``ActiveSync``: if set to ``enabled``, it enables /Microsoft-Server-ActiveSync url.  Default is ``enabled``
* ``ActiveSyncLog``: log level of z-push implementation. As default z-push will log only relevant errors.
* ``DefaultLocale``: default locale for WebTop users. To list available locales execute: ``/etc/e-smith/events/actions/nethserver-webtop5-locale-tz``
* ``DefaultTimezone``: default timezone for WebTop users. To list available timezones: ``JAVA_HOME=/usr/share/webtop/ java ListTimeZones``
* ``MinMemory`` and ``MaxMemory``: minimun and maximum memory of Tomcat instance. Values are expressed in MB.
* ``PublicUrl``: public URL used to publish resources for the cloud. If not set, default is ``http://<FQDN>/webtop``

Example: ::

  webtop=configuration
      ActiveSync=enabled
      ActiveSyncLog=LOGLEVEL_ERROR
      DefaultLocale=en_US
      DefaultTimezone=Etc/UTC
      MaxMemory=1024
      MinMemory=512
      PublicUrl=


Configuration can be applied using the ``nethserver-webtop5-update`` event.

Troubleshooting
===============

Please note that nethserver-webtop5 is composed by many parts.
Each part has its own logs and troubleshooting best practices.

Tomcat
------

Tomcat instance is managed by systemd unit called ``tomcat@webtop``.
All logs are saved inside ``/var/lib/tomcats/webtop/logs/`` directory.

Tomcat output can also be inspected using the following command: ::

  journalctl -u tomcat@webtop

Active Sync
-----------

Active Sync is implemented using a PHP application called z-push.
All logs are inside ``/var/log/z-push/`` directory.

To inspect z-push status use: ::

    php /usr/share/webtop/z-push/z-push-admin.php

It is also possibile to enable z-push debug using these commands: ::

  config setprop webtop ActiveSyncLog LOGLEVEL_DEBUG
  signal-event nethserver-webtop5-update

Instead of ``LOGLEVEL_DEBUG`` you can use any constant supported by z-push implementation.
See ``/usr/share/webtop/z-push/config.php``.


Tomcat instance
===============

WebTop uses its own Tomcat instance running on port ``58080``.

The instance is launched with some special Java options,
see content of ``/etc/sysconfig/tomcat@webtop``.
