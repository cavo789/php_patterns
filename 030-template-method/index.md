# 3. Template method

> [https://laracasts.com/series/design-patterns-in-php/episodes/3?](https://laracasts.com/series/design-patterns-in-php/episodes/3?)

Avoid code duplication.

Imagine the need of creating two different type of cars. A lot of things will be the same (motors, tires, ...) while a few will be different (the interior, the logo, the color, ...).

By creating an abstract class `makeCar` and by using the class in two sub classes `YellowCar` and `RedCar`, we can put reusable methods in the parent `makeCar` class while `YellowCar` and `RedCar` will only implements differences (here, the `setColor` method).

The given example is about making sandwiches: we need bread, we need lettuce, *something* and sauces. Extending the *somehting* part by *turkey* or *veggies* or ... we then have a reusable class with only one thing that is different.

The solution is thus using abstract class and abstract method.
