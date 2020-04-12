# 1. The Decorator Pattern

>https://laracasts.com/series/design-patterns-in-php/episodes/1

Help for **SOLID** concept: Make classes open for extension but closed for modification (i.e. the `Open closed principle`).

Can be done by using an interface so each child should implements a few concepts like a `getCost()` function and, by using a constructor,
it'll be easy to put a class into a class into a class and thus extends the function.

The laracasts episodes give a nice example of a car service: just a review and/or with oil change and/or with tire rotation.

Really easy and, once written, a car service doesn't need to be modified (for adding a service), just write a new service class as illustrated in the video.
