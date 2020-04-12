# 2. Jiggy with adapters

> [https://laracasts.com/series/design-patterns-in-php/episodes/2](https://laracasts.com/series/design-patterns-in-php/episodes/2)
>
> [https://github.com/laracasts/Getting-Jiggy-With-Adapters](https://github.com/laracasts/Getting-Jiggy-With-Adapters)

The given example is for a class `Person` reading a `Book`. A book has to be opened and then we read it by turning pages.

Then we need to implement eBooks like a Kindle. A Kindle doesn't need to be "opened" but "turned on", pages are not turned one by one but the user just need to click on the next button.

The key is thus to make our `Person` class opened, we'll use `openBook` and `turnPages` but if this is a book or a Kindle or ... it doesn't matters.

This can be done by using interfaces (a class needs to implement a `openBook` method and a `turnPages` method). Then, to be able to use a Kindle, we'll need to make an adapter: `openBook` is, for a Kindle, `turnOn` while `turnPages` is `clickNext`.

The KindleAdapter will thus translate expected methods (`openBook`) to intern methods implements by the class (here `turnOn`).

By writing opened adapters (like `eReaders`) we'll be opened for any readers or books without the need to change our `Person` class i.e. without the need to modify our existing classes.
