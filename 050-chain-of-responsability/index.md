# 5. Chain of Responsibility

> [https://laracasts.com/series/design-patterns-in-php/episodes/5](https://laracasts.com/series/design-patterns-in-php/episodes/5)

Before leaving home, we need to check:

* is it alarm on?
* lights off?
* locks the doors?
* ...

The Chain of Responsibility pattern aims to implement such tasks.

Note: the concept of `middleware` of Laravel is exactly working the same way.

Consider the following example: we'll check doors. If doors aren't closed, an exception will be triggered but if well closed, we need
to chain to the next controls but which one?

```php
<?php

class HomeStatus {
    public $alarmOn = true;
    public $locked = true;
    public $lightsOff = true;
}

class Locks {
    public function check(HomeStatus $home) {
        if (!$home->locked) {
            throw new Exception('The doors are not locked!');
        }
        // Call the next check...
    }
}

class Lights {
}

class Alarm {
}

$locks = new Locks;

$status = new HomeStatus;
$locks->check($status);
```

Implement the chain; add a `HomeChecker` abstract class and a `successor` property.

The `check` method is defined as abstract since that method should be implemented in every classes (check lights, check doors, check alarm, ...) and we need to defined the `next` method: who'll be the next check?

```php
<?php

class HomeStatus {
    public $alarmOn = true;
    public $locked = true;
    public $lightsOff = true;
}

abstract class HomeChecker {

    protected $successor;

    public abstract function check(HomeStatus $home);

    public function succeedWith(HomeChecker $successor) {
        $this->successor = $successor;
    }

    public function next(HomeChecker $home) {
        // Make sure we've a successor
        if ($this->successor) {
            $this->successor->check($home);
        }
    }
}

class Locks extends HomeChecker {
    public function check(HomeStatus $home) {
        if (!$home->locked) {
            throw new Exception('The doors are not locked!');
        }

        // Chain with the next HomeChecker
        $this->next($home);
    }
}

$locks = new Locks;
$locks->check(new HomeStatus);
```

And we can create as many class as we want...

```php
class Lights extends HomeChecker {
    public function check(HomeStatus $home) {
        if (!$home->lightsOff) {
            throw new Exception('The lights are still on!');
        }
        $this->next($home);
    }
}

class Alarm extends HomeChecker {
    public function check(HomeStatus $home) {
        if (!$home->alarmOn) {
            throw new Exception('Alarm is not set!');
        }
        $this->next($home);
    }
}
```

Finally, we can implement our controls and define the successors chain:

```php

$locks = new Locks;
$lights = new Lights;
$alarm = new Alarm;

// Create the chain, define who is the successor i.e. the next check
$locks->succeedWith($lights);
$lights->succeedWith($alarm);

// Call the check() method on the first class to fire the full check
$locks->check(new HomeStatus);

// The code will never comes here if locks / lights / alarm checks
// throws an exception.
echo 'Well done! Everything is OK';
```
