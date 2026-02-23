---
title: "What it means that Ubuntu is using Rust"
date: 2026-02-23T08:14:05-05:00
---

Righty-ho, I'm back from Rust Nation, and busily horrifying my teenage daughter with my (admittedly atrocious) attempts at doing an English accent[^accents]. It was a great trip with a lot of good conversations and some interesting observations. I am going to try to blog about some of them, starting with some thoughts spurred by Jon Seager's closing keynote, "Rust Adoption At Scale with Ubuntu".

[^accents]: I am famously bad at accents. My best attempt at posh British sounds more like Apu from the Simpsons. I really wish I could pull off a convincing Greek accent, but sadly no.

<!--more-->

## There are many chasms out there

For some time now I've been debating with myself, has Rust ["crossed the chasm"][ctc]? If you're not familiar with that term, it comes from a book that gives a kind of "pop-sci" introduction to the [Technology Adoption Life Cycle][talc]. 

[ctc]: https://en.wikipedia.org/wiki/Crossing_the_Chasm
[talc]: https://en.wikipedia.org/wiki/Technology_adoption_life_cycle

The answer, of course, is *it depends on who you ask*. Within Amazon, where I have the closest view, the answer is that we are "most of the way across": Rust is squarely established as the right way to build at-scale data planes or resource-aware agents and it is increasingly seen as the right choice for low-level code in devices and robotics as well -- but there remains a lingering perception that Rust is useful for "those fancy pants developers at S3" (or wherever) but a bit overkill for more average development[^AI].

[^AI]: Russel Cohen and Jess Izen gave a [great talk at last year's RustConf](https://www.youtube.com/watch?v=VthhIdqwdHc) about what our team is doing to help teams decide if Rust is viable for them. But since then another thing having a big impact is AI, which is bringing previously unthinkable projects, like rewriting older systems, within reach.

On the other hand, within the realm of Safety Critical Software, as Pete LeVasseur wrote in a [recent rust-lang blog post](https://blog.rust-lang.org/2026/01/14/what-does-it-take-to-ship-rust-in-safety-critical/), Rust is still scrabbling for a foothold. There are a number of successful products but most of the industry is in a "wait and see" mode, letting the early adopters pave the path.

## "Crossing the chasm" means finding "reference customers"

The big idea that I at least took away from reading [Crossing the Chasm][ctc] and other references on the [technology adoption life cycle][talc] is the need for "reference customers". When you first start out with something new, you are looking for pioneers and early adopters that are drawn to new things:

> What an early adopter is buying \[..\] is some kind of *change agent*. By being the first to implement this change in the industry, the early adopters expect to get a jump on the competition. -- from *Crossing the Chasm*

But as your technology matures, you have to convince people with a lower and lower tolerance for risk:

> The early majority want to buy a *productivity improvement* for existing operations. They are looking to minimize discontinuity with the old ways. They want evolution, not revolution. -- from *Crossing the Chasm*

So what is *most convincing* to people to try something new? The answer is seeing that others like them have succeeded.

You can see this at play in both the Amazon example and the Safety Critical Software example. Clearly seeing Rust used for network services doesn't mean it's ready to be used in your car's steering column[^interwebs]. And even within network services, seeing a group like S3 succeed with Rust may convince other groups building at-scale services to try Rust, but doesn't necessarily persuade a team to use Rust for their next CRUD service. And frankly, it shouldn't! They are likely to hit obstacles.

[^interwebs]: I have no idea if there is code in a car's steering column, for the record. I assume so by now? For power steering or some shit?

## Ubuntu is helping Rust "cross the (user-land linux) chasm"

All of this was on my mind as I watched the keynote by Jon Seager, the VP of Engineering at Canonical, which is the company behind Ubuntu. Similar to Lars Bergstrom's [epic keynote from year's past](https://www.youtube.com/watch?v=QrrH2lcl9ew) on Rust adoption within Google, Jon laid out a pitch for why Canonical is adopting Rust that was at once **visionary** and yet **deeply practical**.

"Visionary and yet deeply practical" is pretty much the textbook description of what we need to cross from *early adopters* to *early majority*. We need folks who care first and foremost about delivering the right results, but are open to new ideas that might help them do that better; folks who can stand on both sides of the chasm at once.

Jon described how Canonical focuses their own development on a small set of languages: Python, C/C++, and Go, and how they had recently brought in Rust and were using it as the language of choice for new [foundational efforts](https://smallcultfollowing.com/babysteps/blog/2025/03/10/rust-2025-intro/), replacing C, C++, and (somewhat surprisingly) Python. 

## Ubuntu is building the bridge across the chasm

Jon talked about how he sees it as part of Ubuntu's job to "pay it forward" by supporting the construction of memory-safe foundational utilities. Jon meant support both in terms of finances -- Canonical is sponsoring the [Trifecta Tech Foundation's](https://trifectatech.org/) to develop [sudo-rs](https://github.com/trifectatechfoundation/sudo-rs) and [ntpd-rs](https://github.com/pendulum-project/ntpd-rs) and sponsoring the [uutils org's](https://github.com/uutils/) work on [coreutils](https://uutils.github.io/coreutils/) -- and in terms of reputation. Ubuntu can take on the risk of doing something new, prove that it works, and then let others benefit.

Remember how the Crossing the Chasm book described early majority people? They are "looking to minimize discontinuity with the old ways". And what better way to do that than to have drop-in utilities that fit within their existing workflows.

## The challenge for Rust: listening to these new adopters

With new adoption comes new perspectives. On Thursday night I was at dinner[^tea] organized by Ernest Kissiedu[^love]. Jon Seager was there along with some other Rust adopters from various industries, as were a few others from the Rust Foundation and the open-source project. 

[^tea]: Or am I supposed to call it "tea"? Or maybe "supper"? I can't get a handle on British mealtimes.

[^love]: Ernest is such a joy to be around. He's quiet, but he's got a lot of insights if you can convince him to share them. If you get the chance to meet him, take it! If you live in London, go to the London Rust meetup! Find Ernest and introduce yourself. Tell him Niko sent you and that you are supposed to say how great he is and how you want to learn from the wisdom he's accrued over the years. Then watch him blush. What a doll.

Ernest asked them to give us their unvarnished takes on Rust. Jon made the provocative comment that we needed to revisit our policy around having a small standard library. He's not the first to say something like that, it's something we've been hearing for years and years -- and I think he's right! Though I don't think the answer is just to ship a big standard library. In fact, it's kind of a perfect lead-in to (what I hope will be) my next blog post, which is about a project I call "battery packs"[^bp].

[^bp]: If you can't wait, you can read some [Zulip discussion][bpp] here.

[bpp]: https://rust-lang.zulipchat.com/#narrow/channel/220302-wg-cli/topic/Hello.20everyone/near/570148087

## To grow, you have to change

The broader point though is that shifting from targeting "pioneers" and "early adopters" to targeting "early majority" sometimes involves some uncomfortable changes:

> Transition between any two adoption segments is normally excruciatingly awkward because you must adopt new strategies just at the time you have become most comfortable with the old ones. \[..\] The situation can be further complicated if the high-tech company, fresh from its marketing success with visionaries, neglects to change its sales pitch. \[..\] **The company may be saying "state-of-the-art" when the pragmatist wants to hear "industry standard".** -- Crossing the Chasm (emphasis mine)

Not everybody will remember it, but in 2016 there was a proposal called [the Rust Platform](https://internals.rust-lang.org/t/proposal-the-rust-platform/3745). The idea was to bring in some crates and bless them as a kind of "extended standard library". People *hated* it. After all, they said, why not just add dependencies to your `Cargo.toml`? It's easy enough. And to be honest, they were right -- at least at the time.

I think the Rust Platform is a good example of something that was a poor fit for early adopters, who want the newest thing and don't mind finding the best crates, but which could be a *great* fit for the Early Majority.[^bpbetter]

[^bpbetter]: The [Battery Packs proposal][bpp] I want to talk about is similar in some ways to the Rust Platform, but decentralized and generally better in my opinion-- but I get ahead of myself!

Anyway, I'm not here to argue for one thing or another in this post, but more for the concept that we have to be open to adapting our learned wisdom to new circumstances. In the past, we were trying to bootstrap Rust into the industry's consciousness -- and we have succeeded.

The task before us now is different: **we need to make Rust the best option not just in terms of "what it *could be*" but in terms of "what it *actually is*"** -- and sometimes those are in tension.

## Another challenge for Rust: turning adoption into investment

Later in the dinner, the talk turned, as it often does, to money. Growing Rust adoption also comes with growing needs placed on the Rust project and its ecosystem. How can we connect the dots? This has been a big item on my mind, and I realize in writing this paragraph how many blog posts I have yet to write on the topic, but let me lay out a few interesting points that came up over this dinner and at other recent points.

## Investment can mean contribution, particularly for open-source orgs

First, there are more ways to offer support than $$. For Canonical specifically, as they are an open-source organization through-and-through, what I would most want is to build stronger relationships between our organizations. With the Rust for Linux developers, early on Rust maintainers were prioritizing and fixing bugs on behalf of RfL devs, but more and more, RfL devs are fixing things themselves, with Rust maintainers serving as mentors. This is awesome!

## Money often comes *before* a company has adopted Rust, not after

Second, there's an interesting trend about $$ that I've seen crop up in a few places. We often think of companies investing in the open-source dependencies that they rely upon. But there's an entirely different source of funding, and one that might be even easier to tap, which is to look at companies that are **considering** Rust but haven't adopted it yet.

For those "would be" adopters, there are often *individuals* in the org who are trying to make the case for Rust adoption -- these individuals are early adopters, people with a vision for how things could be, but they are trying to sell to their early majority company. And to do that, they often have a list of "table stakes" features that need to be supported; what's more, they often have access to some budget to make these things happen.

This came up when I was talking to Alexandru Radovici, the Foundation's Silver Member Directory, who said that many safety critical companies have money they'd like to spend to close various gaps in Rust, but they don't know how to spend it. Jon's investments in Trifecta Tech and the uutils org have the same character: he is looking to close the gaps that block Ubuntu from using Rust more.

## Conclusions...?

Well, first of all, you should watch Jon's talk. "Brilliant", as the Brits have it.

But my other big thought is that this is a crucial time for Rust. We are clearly transitioning in a number of areas from visionaries and early adopters towards that pragmatic majority, and we need to be mindful that doing so may require us to change some of the way that we've always done things. I liked this paragraph from [Crossing the Chasm][ctc]:

> To market successfully to pragmatists, one does not have to be one -- just understand their values and work to serve them. To look more closely into these values, if the goal of visionaries is to take a quantum leap forward, the goal of pragmatists is to make a percentage improvement--incremental, measurable, predictable progress. \[..\] To market to pragmatists, you must be patient. You need to be conversant with the issues that dominate their particular business. You need to show up at the industry-specific conferences and trade shows they attend.

Re-reading [Crossing the Chasm][ctc] as part of writing this blog post has really helped me square where Rust is -- for the most part, I think we are still crossing the chasm, but we are well on our way. I think what we see is a consistent trend now where we have Rust *champions* who fit the "visionary" profile of early adopters successfully advocating for Rust within companies that fit the pragmatist, early majority profile.

### Open source can be a great enabler to cross the chasm...

It strikes me that open-source is just an amazing platform for doing this kind of marketing. Unlike a company, we don't have to do everything ourselves. We have to leverage the fact that *open source helps those who help themselves* -- find those visionary folks in industries that could really benefit from Rust, bring them into the Rust orbit, and then (most important!) **support and empower them** to adapt Rust to their needs.

### ...but only if we don't get too "middle school" about it

This last part may sound obvious, but it's harder than it sounds. When you're embedded in open source, it seems like a friendly place where everyone is welcome. But the reality is that it can be a place full of cliques and "oral traditions" that "everybody knows"[^nobody]. People coming with an idea can get shutdown for using the wrong word. They can readily mistake the, um, "impassioned" comments from a random contributor (or perhaps just a troll...) for the official word from project leadership. It only takes one rude response to turn somebody away.

[^nobody]: [Betteridge's Law of Headlines](https://en.wikipedia.org/wiki/Betteridge%27s_law_of_headlines) has it that "Any headline that ends in a question mark can be answered by the word *no*". Well, Niko's law of open-source[^adage] is that "nobody actually knows anything that 'everybody' knows".

[^adage]: Another of my pearls of wisdom is "there is nothing more permanent than temporary code". I used to say that back at the startup I worked at after college, but years of experience have only proven it more and more true.

### What Rust needs most is empathy

So what will ultimately help Rust the most to succeed? [Empathy in Open Source](https://smallcultfollowing.com/babysteps/blog/2023/09/27/empathy-in-open-source/). Let's get out there, find out where Rust can help people, and make it happen. Exciting times!

