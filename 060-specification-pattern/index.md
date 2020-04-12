# 6. Specification pattern

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
