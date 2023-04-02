---
title: ThinkPHP 5 隐藏index.php的方法
tags: 技术
date: 2017-06-02 08:57
comments: true
---

Apache：
```php
    <IfModule mod_rewrite.c>
        RewriteEngine on
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteRule ^(.*)$ index.php?s=/$1 [QSA,PT,L]
    </IfModule>
```
Nginx：
```php
    location / {
		if (!-e $request_filename){
			rewrite  ^(.*)$  /index.php?s=$1  last;   break;
		}
	}
```