# 4. Strategy design

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
