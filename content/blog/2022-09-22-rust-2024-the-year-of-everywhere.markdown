---
layout: post
title: Rust 2024...the year of everywhere?
date: 2022-09-22T15:51:00-0400
---

I’ve been thinking about what “Rust 2024” will look like lately. I don’t really mean the edition itself — but more like, what will Rust feel like after we’ve finished up the next few years of work? I think the answer is that Rust 2024 is going to be the year of “everywhere”. Let me explain what I mean. Up until now, Rust has had a lot of nice features, but they only work *sometimes*. By the time 2024 rolls around, they’re going to work *everywhere* that you want to use them, and I think that’s going to make a big difference in how Rust feels.

## Async *everywhere*

Let’s start with async. Right now, you can write async functions, but not in traits. You can’t write async closures. You can’t use async drop. This creates a real hurdle. You have to learn the workarounds (e.g., the [`async-trait`] crate), and in some cases, there are no proper workarounds (e.g., for async-drop).

[`async-trait`]: https://crates.io/crates/async-trait

Thanks to a recent PR by [Michael Goulet], static async functions in traits *almost* work on nightly today! I’m confident we can work out the remaining kinks soon and start advancing the static subset (i.e., no support for dyn trait) towards stabilization. 

The plans for dyn, meanwhile, are advancing rapidly. At this point I think we have two good options on the table and I’m hopeful we can get that nailed down and start planning what’s needed to make the implementation work.

Once async functions in traits work, the next steps for core Rust will be figuring out how to support async closures and async drop. Both of them add some additional challenges — particularly async drop, which has some complex interactions with other parts of the language, as Sabrina Jewson elaborated in a [great, if dense, blog post](https://sabrinajewson.org/blog/async-drop) — but we’ve started to develop a crack team of people in the async working group and I’m confident we can overcome them.

There is also library work, most notably settling on some interop traits, and defining ways to write code that is portable across allocators. I would like to see more exploration of structured concurrency[^moro], as well, or other alternatives to `select!` like the [stream merging pattern](https://blog.yoshuawuyts.com/futures-concurrency-3/#concurrent-stream-processing-with-stream-merge) Yosh has been advocating for.

[Michael Goulet]: https://github.com/compiler-errors

[^moro]: Oh, my beloved [moro]! I will return to thee!

[moro]: https://github.com/nikomatsakis/moro

Finally, for extra credit, I would love to see us integrate async/await keywords into other bits of the function body, permitting you to write common patterns more easily. Yoshua Wuyts has had a really interesting series of blog posts exploring these sorts of ideas. I think that being able to do `for await x in y` to iterate, or `(a, b).await` as a form of join, or `async let x = …` to create a future in a really lightweight way could be great.

## Impl trait *everywhere*

The `impl Trait` notation is one of Rust’s most powerful conveniences, allowing you to omit specific types and instead talk about the interface you need. Like async, however, impl Trait can only be used in inherent functions and methods, and can’t be used for return types in traits, nor can it be used in type aliases, let bindings, or any number of other places it might be useful.

Thanks to [Oli Scherer]’s hard work over the last year, we are nearing stabilization for impl Trait in type aliases. Oli’s work has also laid the groundwork to support impl trait in let bindings, meaning that you will be able to do something like

```rust
let iter: impl Iterator<Item = i32> = (0..10);
//        ^^^^^^^^^^^^^ Declare type of `iter` to be “some iterator”.
```

Finally, the same PR that added support for async fns in traits also added initial support for return-position impl trait in traits. Put it all together, and we are getting very close the letting you use impl trait everywhere you might want to.

There is still at least one place where `impl Trait` is not accepted that I think it should be, which is nested in other positions. I’d like you to be able to write `impl Fn(impl Debug)`, for example, to refer to “some closure that takes an argument of type `impl Debug`” (i.e., can be invoked multiple times with different debug types).

[Oli Scherer]: https://github.com/oli-obk

## Generics *everywhere*

Generic types are a big part of how Rust libraries are built, but Rust doesn’t allow people to write generic parameters in all the places they would be useful, and limitations in the compiler prevent us from making full use of the annotations we do have. 

Not being able to use generic types everywhere might seem abstract, particularly if you’re not super familiar with Rust. And indeed, for a lot of code, it’s not a big deal. But if you’re trying to write libraries, or to write one common function that will be used all over your code base, then it can quickly become a huge blocker. Moreover, given that Rust supports generic types in many places, the fact that we don’t support them in *some* places can be really confusing — people don’t realize that the reason their idea doesn’t work is not because the idea is wrong, it’s because the language (or, often, the compiler) is limited.

The biggest example of generics everywhere is *generic associated types*. Thanks to hard work by [Jack Huey], [Matthew Jasper], and a [number of others], this feature is very close to hitting stable Rust — in fact, it is in the current beta, and should be available in 1.65. One caveat, though: the upcoming support for GATs has a number of known limitations and shortcomings, and it gives some pretty confusing errors. It’s still really useful, and a lot of people are already using it on nightly, but it’s going to require more attention before it lives up to its full potential.

[Jack Huey]: https://github.com/jackh726/
[Matthew Jasper]: https://github.com/MatthewJasper/
[number of others]: https://blog.rust-lang.org/2021/08/03/GATs-stabilization-push.html#why-has-it-taken-so-long-to-implement-this

You may not wind up using GATs in your code, but it will definitely be used in some of the libraries you rely on. GATs directly enables common patterns like `Iterable` that have heretofore been inexpressible, but we’ve also seen a lot of examples where its used internally to help libraries present a more unified, simpler interface to their users.

Beyond GATs, there are a number of other places where we could support generics, but we don’t. In the previous section, for example, I talked about being able to have a function with a parameter like `impl Fn(impl Debug)` — this is actually an example of a “generic closure”. That is, a closure that itself has generic arguments. Rust doesn’t support this yet, but there’s no reason we can’t.

Oftentimes, though, the work to realize “generics everywhere” is not so much a matter of extending the language as it is a matter of improving the compiler’s implementation. Rust’s current traits implementation works pretty well, but as you start to push the bounds of it, you find that there are lots of places where it could be smarter. A lot of the ergonomic problems in GATs arise exactly out of these areas.

One of the developments I’m most excited about in Rust is not any particular feature, it’s the formation of the new [types team]. The goal of this team is to revamp the compiler’s trait system implementation into something efficient and extensible, as well as building up a core set of contributors. 

[types team]: https://github.com/rust-lang/types-team

## Making Rust feel simpler by making it more uniform

The topics in this post, of course, only scratch the surface of what’s going on in Rust right now. For example, I’m really excited about “everyday niceties” like let/else-syntax and if-let-pattern guards, or the scoped threads API that we got in 1.63. There are exciting conversations about ways to improve error messages. Cargo, the compiler, and rust-analyzer are all generally getting faster and more capable. And so on, and so on.

The pattern of having a feature that starts working *somewhere* and then extending it so that it works *everywhere* seems, though, to be a key part of how Rust development works. It’s inspiring also because it becomes a win-win for users. Newer users find Rust easier to use and more consistent; they don’t have to learn the “edges” of where one thing works and where it doesn’t. Experienced users gain new expressiveness and unlock patterns that were either awkward or impossible before.

One challenge with this iterative development style is that sometimes it takes a long time. Async functions, impl Trait, and generic reasoning are three areas where progress has been stalled for years, for a variety of reasons. That’s all started to shift this year, though. A big part of is the formation of new Rust teams at many companies, allowing a lot more people to have a lot more time. It’s also just the accumulation of the hard work of many people over a long time, slowly chipping away at hard problems (to get a sense for what I mean, read [Jack’s blog post on NLL removal][nllr], and take a look at the full list of contributors he cited there — just assembling the list was impressive work, not to mention the actual work itself).

[nllr]: https://jackh726.github.io/rust/2022/06/10/nll-stabilization.html#how-did-we-get-here

[“third way”]: https://smallcultfollowing.com/babysteps/blog/2022/09/19/what-i-meant-by-the-soul-of-rust/

It may have been a long time coming, but I’m really excited about where Rust is going right now, as well as the new crop of contributors that have started to push the compiler faster and faster than it’s ever moved before. If things continue like this, Rust in 2024 is going to be pretty damn great.