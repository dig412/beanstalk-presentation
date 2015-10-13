class: center, middle

background-image: url(images/crowd.jpg)

![Ents24 Logo](images/logo.png)

# Beanstalk & Supervisord

---
class: center, middle
background-image: url(images/beanstalk.jpg)

#Beanstalk

---
#Beanstalk

* Simple job queue

--

* Jobs are blobs
    * We use JSON

--

* Stored in tubes

    * No configuration needed

--

* Binary logging

* Memcache style distribution
    * Client handles connections to several servers

---
.fleft[
![](images/tube1.png)
]

.fright[
```php
$bean = new Pheanstalk('127.0.0.1');
```
]

---
.fleft[
![](images/tube2.png)
]

.fright[
```php
$bean = new Pheanstalk('127.0.0.1');
$bean->useTube("phpsw");
```
]

---
.fleft[
![](images/tube3.png)
]

.fright[
```php
$bean = new Pheanstalk('127.0.0.1');
$bean->useTube("phpsw");
$bean->put("count the elephpants")
```
]

---
.fleft[
![](images/tube4.png)
]

.fright[
```php
$bean = new Pheanstalk('127.0.0.1');
$bean->watch("phpsw");
$job = $bean->reserve();
```
]

---
.fleft[
![](images/tube5.png)
]

.fright[
```php
$bean = new Pheanstalk('127.0.0.1');
$bean->watch("phpsw");
$job = $bean->reserve();
//turns out I can't count elephpants
$bean->bury($job);
```
]

---
.fleft[
![](images/tube6.png)
]

.fright[
```php
$bean = new Pheanstalk('127.0.0.1');
$bean->watch("phpsw");
$bean->kick(1);
```
]

---
#What can we use it for?
--

###Expensive Tasks
* Image Processing
    * Scaling
    * Filtering
    * Uploading

--
###Distributed Tasks
* Updates system

--
###Scale
* Email sender

---
class: center, middle
background-image: url(images/supervisord.jpg)

#Supervisord
---
#Supervisord

* Long running processes
* Starts specified number
* Restarts on close
* Stops on repeated close
* Captures stdout
* controlled via supervisorctl

---

#Problems - MySQL

```
PDOException with message
'SQLSTATE[HY000]: General error: 2006 MySQL server has gone away'
```
--

* mysqli is good (`mysqli.reconnect`)
--

* PDO is bad
   * One catch block for the PDO object
   * One catch block for the PDOStatement object
   * *Still* triggers a warning

```
Warning: PDO::query() [pdo.query]: MySQL server has gone away
```

---

#Problems - Swiftmailer

```
Expected response code 250 but got code "421"
```

--

* Catchable as `Swift_TransportException`

--

```
$transport->stop()
```
* Swiftmailer will automatically reconnect

---
#Problems - Swiftmailer

```
fwrite(): send of 41 bytes failed with errno=32
Broken pipe in Swift/Transport/StreamBuffer.php on line 236
```

* PHP Notice - uncatchable
* Just reconnect each time!

---
class: center, middle

#Questions?