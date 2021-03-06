#autoupdate

####Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Setup](#setup)
4. [Usage](#usage)
5. [To Do](#to-do)

##Overview

The autoupdate module allows you to configure automatic updates on all [RHEL variants](http://en.wikipedia.org/wiki/List_of_Linux_distributions#RHEL-based) including Fedora.

##Module description

The yum-cron service enables scheduled system updates on RHEL-based systems. This module allows you to install and configure this service without the need to fiddle manually with configuration files, which may vary from one major version to the other on most RHEL-based distributions.

##Setup

autoupdate will affect the following parts of your system:

* yum-cron package and service
* yum cron configuration file(s)
* yum-cron script itself to implement extra features provided by this module

Including the main class is enough to get started. It will enable automatic updates check via a cron.daily task and apply all available updates whenever possible. Summary emails are sent to the local root user.

```puppet
include ::autoupdate
```

####A couple of examples

Disable emails and just download updates, do not apply

```puppet
class { '::autoupdate':
  notify_email => false,
  action       => 'download'
}
```

Suppress debug output completely, but keep logging possible errors

```puppet
class { '::autoupdate':
  …
  debug_level => '-1',
  error_level => '3'
}
```

Send emails to a different receiver (local root account by default) and from a specific sender address 

```puppet
class { '::autoupdate':
  …
  email_to   => 'admin@example.com',
  email_from => 'sysupdates@example.com'
}
```

Let crond send emails instead of mailx

```puppet
class { '::autoupdate':
  …
  email_to => ''
}
```

Disable random delay

```puppet
class { '::autoupdate':
  …
  randomwait => '0'
}
```

Exclude specific packages

```puppet
class { '::autoupdate':
  …
  exclude => ['httpd','puppet*']
}
```

##Usage

####Class: `autoupdate`

Primary class and entry point of the module.

**Parameters within `autoupdate`:**

#####`action`

Mode in which yum-crom should perform. Valid values are 'check', 'download' and 'apply'. Defaults to 'apply'

#####`exclude`

Array of packages to exclude from automatic update. Defaults to '[]'

#####`service_enable`

Enable the service or not. Valid values are 'true' and 'false'. Defaults to 'true'

#####`service_ensure`

Whether the service should be running. Valid values are 'stopped' and 'running'. Defaults to 'running'

#####`notify_email`

Enable email notifications. Valid values are 'true' and 'false'. Defaults to 'true'  
It is recommended to also adjust debug/error levels accordingly (see below) 

#####`email_to`

Recipient email address for update notifications. Defaults to 'root' (local user)  
An empty string forces the output to stdio, so emails will be sent by crond

#####`email_from`

Sender email address for update notifications. No effect when `email_to` is empty. Defaults to 'root' (local user)

Note: not supported on CentOS 5

#####`debug_level`

YUM debug level. Valid values are numbers between -1 and 10. '-1' to disable. Default depends on the platform  
Enforced to `-1` when `notify_email` is `false`

Notes:

* `-1` is necessary to also suppress messages from deltarpm, since `0` doesn't
* Always outputs to stdio on modern platforms, can apparently not be changed

#####`error_level`

YUM error level. Valid values are numbers between 0 and 10. '0' to disable. Defaults to '0'

Note: always outputs to stdio on modern platforms, can apparently not be changed

#####`randomwait`

Maximum amount of time in minutes YUM randomly waits before running. Valid values are numbers between 0 and 1440. '0' to disable. Defaults to '60'

##To Do

* Add support for multiple schedules
* Add support for passing arbitrary parameters to YUM
* Make it Debian compatible

Features request and contributions are always welcome!