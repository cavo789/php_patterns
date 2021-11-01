# PHP Patterns

Learning - Implementation of a few patterns in PHP

![Banner](./banner.svg)

> [https://laracasts.com/series/design-patterns-in-php](https://laracasts.com/series/design-patterns-in-php)

<!-- table-of-contents - start -->
* [1. The Decorator Pattern](#1-the-decorator-pattern)
* [2. Jiggy with adapters](#2-jiggy-with-adapters)
* [3. Template method](#3-template-method)
* [4. Strategy design](#4-strategy-design)
* [5. Chain of Responsibility](#5-chain-of-responsibility)
* [6. Specification pattern](#6-specification-pattern)
* [License](#license)
<!-- table-of-contents - end -->

## 1. The Decorator Pattern

>https://laracasts.com/series/design-patterns-in-php/episodes/1

Help for **SOLID** concept: Make classes open for extension but closed for modification (i.e. the `Open closed principle`).

Can be done by using an interface so each child should implements a few concepts like a `getCost()` function and, by using a constructor,
it'll be easy to put a class into a class into a class and thus extends the function.

The laracasts episodes give a nice example of a car service: just a review and/or with oil change and/or with tire rotation.

Really easy and, once written, a car service doesn't need to be modified (for adding a service), just write a new service class as illustrated in the video.

## 2. Jiggy with adapters

> [https://laracasts.com/series/design-patterns-in-php/episodes/2](https://laracasts.com/series/design-patterns-in-php/episodes/2)
>
> [https://github.com/laracasts/Getting-Jiggy-With-Adapters](https://github.com/laracasts/Getting-Jiggy-With-Adapters)

The given example is for a class `Person` reading a `Book`. A book has to be opened and then we read it by turning pages.

Then we need to implement eBooks like a Kindle. A Kindle doesn't need to be "opened" but "turned on", pages are not turned one by one but the user just need to click on the next button.

The key is thus to make our `Person` class opened, we'll use `openBook` and `turnPages` but if this is a book or a Kindle or ... it doesn't matters.

This can be done by using interfaces (a class needs to implement a `openBook` method and a `turnPages` method). Then, to be able to use a Kindle, we'll need to make an adapter: `openBook` is, for a Kindle, `turnOn` while `turnPages` is `clickNext`.

The KindleAdapter will thus translate expected methods (`openBook`) to intern methods implements by the class (here `turnOn`).

By writing opened adapters (like `eReaders`) we'll be opened for any readers or books without the need to change our `Person` class i.e. without the need to modify our existing classes.

## 3. Template method

> [https://laracasts.com/series/design-patterns-in-php/episodes/3?](https://laracasts.com/series/design-patterns-in-php/episodes/3?)

Avoid code duplication.

Imagine the need of creating two different type of cars. A lot of things will be the same (motors, tires, ...) while a few will be different (the interior, the logo, the color, ...).

By creating an abstract class `makeCar` and by using the class in two sub classes `YellowCar` and `RedCar`, we can put reusable methods in the parent `makeCar` class while `YellowCar` and `RedCar` will only implements differences (here, the `setColor` method).

The given example is about making sandwiches: we need bread, we need lettuce, *something* and sauces. Extending the *somehting* part by *turkey* or *veggies* or ... we then have a reusable class with only one thing that is different.

The solution is thus using abstract class and abstract method.

## 4. Strategy design

> [https://laracasts.com/series/design-patterns-in-php/episodes/4](https://laracasts.com/series/design-patterns-in-php/episodes/4)

Implement concepts of **SOLID** by making features interchangeable: the **need** of logging something is one thing and the **where** is something else. We'll log an information into a file or a database or in an external service or ... Too by making the class closed for changes but opened for extensions.

```php
<?php

interface Logger {
    public function log($data) {
    }
}

class LogToFile implements Logger {
    public function log($data) {
        var_dump('Log data to a file');
    }
}

class LogToDatabase implements Logger {
    public function log($data) {
        var_dump('Log data to a database');
    }
}

class LogToWebService implements Logger {
}
```

By creating a `LogToxxxx` class and implementer an interface, we're defining a contract. Each `LogToxxxx` classes have to implement the  `log()` methond on its own.

So, in our own code, we can just change our `$log = new LogToFile` instanciation to `$log = new LogToDatabase` and it's done. Just the constructor will change (database connection string instead of a  filename).

Note: the best way to to this is by not hard-coding the `LogToFile` class in the PHP code but by putting it into the method parameter.

To make a class fully reusable without to change it can be done by setting the logger as a parameter like below. From now, we just need to call `App->log()` by telling what to log and where to log. The `App` class is now closed for modification but opened for extensions.

```php
class App {
    public function log($data, Logger $logger = null) {
        $logger = $logger ?: new LogToFile;
        $logger->log($data);
    }
}

(new App)->log('Some info', new LogToDatabase);
```

## 5. Chain of Responsibility

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

## 6. Specification pattern

> [https://laracasts.com/series/design-patterns-in-php/episodes/6](https://laracasts.com/series/design-patterns-in-php/episodes/6)
>
> [https://laracasts.com/series/design-patterns-in-php/episodes/7](https://laracasts.com/series/design-patterns-in-php/episodes/7)
>
> [https://github.com/laracasts/the-specification-pattern-in-php](https://github.com/laracasts/the-specification-pattern-in-php)

Let's take the following example: how to check if a given customer is a gold one.

Easy way:

```php
class Customer {
    protected $subscription = '';
    public function isGold() {
        return $this->subscription == 'gold';
    }
}
```

or, better way:

```php
class CustomerIsGold {
    public function isSatisfiedBy(Customer $customer) {
        return $customer->subscription == 'gold';
    }
}
```

The second is better since we'll not "pollute" our generic `Customer` class with `isSilver`, `isBronze`, `isTemporary`, ... i.e. by  creating as many `isXXXX` than we need or ... doesn't need anymore (if no more needed we'll need to clean our class with the risks of creating a regression).

Keeping `Customer` class as small as possible is the key to keep maintainable code.

```php
abstract class CustomerSpecification {
    abstract function isSatisfiedBy(Customer $customer);
}

class CustomerIsGold implements CustomerSpecification {
    public function isSatisfiedBy(Customer $customer) {
        return $customer->subscription == 'gold';
    }
}

$spec = new CustomerIsGold();
if ($spec->isSatisfiedBy(new Customer()->find('John Doe'))) {
    // This is a gold customer
}
```

## License

[MIT](LICENSE)
