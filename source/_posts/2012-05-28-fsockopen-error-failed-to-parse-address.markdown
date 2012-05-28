---
layout: post
title: "fsockopen error - failed to parse address"
date: 2012-05-28 14:24
comments: true
categories: [PHP, MailPress, WordPress]
---
Just met this error when using MailPress plugin in WordPress. 
on file 

`"wp-content/plugins/mailpress/mp-includes/Swiftmailer/classes/Swift/Transport/StreamBuffer.php" Line 233`

		if (!$this->_stream = fsockopen($host, $this->_params['port'], $errno, $errstr, $timeout))
		{
		  throw new Swift_TransportException(
		    'Connection could not be established with host ' . $this->_params['host'] .
		    ' [' . $errstr . ' #' . $errno . ']'
		    );
		}


Checked the php manual: 
[fsockopen](http://cn.php.net/manual/en/function.fsockopen.php)
It says 
```php
resource fsockopen ( string $hostname [, int $port = -1 [, int &$errno [, string &$errstr [, float $timeout = ini_get("default_socket_timeout") ]]]] )
```

After some research, I found that **we can't leave the port parameter empty, although it is stated optional**. 

This can be traced to C function "parse_ip_address_ex", it needs a colon to separate IP and port number in the textual address it is passed.

Refer from [this topic](http://forum.caravelgames.com/viewtopic.php?TopicID=16512)

So the solution is simple, either add a port behind the server's address, or specify a port as the second parameter.

{% codeblock Simple Solution lang:php %}
fsockopen('relay.xxx.com:25');
or
fsockopen('relay.xxx.com', 25);
{% endcodeblock %}

Of course we won't change the MailPress's source code, just change the MailPress settings on the dashboard :)