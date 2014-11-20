---
layout: post
title: On API Correctness
---

# On API Correctness

## Developing APIs is hard. 

You pour your blood sweat and tears into this interface that bares your company/product/services soul to the world.  It's an understatement, though, to say that often times the machinery under the hood is a lot less polished than the fancy paint job would lead the rest of the world to believe (if you'll pardon the automotive analogy.)  We have to be careful, though, to not inflict our own rough edges on the people you expect to be consuming your API because...

## Using APIs is hard.

As an app developer you're trying to take someone elses product and somehow integrate it into whatever vision that you've got in your head.  Whether it's simply getting a list of things from another service (such as embedding a reading list) or full on wrapping your entire product around another product (using Amazon S3 as your primary binary storage mechanism for example) you have a lot of things to reconsile.

You have your own programming language (or more likely languages) that you're using. The use case you have in mind.  The use cases the remote devs had in mind for the API. The programming language they used to create the API (and that they used to test it). And the encoding or representation of the data and its limitations.  Reconsiling all of the slight (or major) differences between these things is a real challenge some times -- despite years of attempts at best practices and industry standards things just don't always fit together like we pretend that they will.

As a developer providing an API it's important to remember three things.  It's actually of varying levels of importance to remember a lot of things but these three things are more universal than most.

### #1 You want people to use your API.

Unless you're developing a completely internal API you're hoping that the world latches onto your API as something amazing and that your functionality starts popping up in other magical places with zero further effort on your part.

### #2 You have no control over what tools those people are using.

Are you using a language that has little or no variable type enforcement? Some people aren't.  Some of those people still want to use your product.  Did you come up with your own way of doing things which requires custom code to do things that there are already widely adopted intustry standards (and thus widely deployed and battle tested libraries in many languages)? Did that cause you to release a client in your own language (how about clojure, how about erlang, how about c++, how about perl, how about [...]) (Who do you expect to do this work)?

### #3 Your API is a promise.

It's easy to forget (especially for those of us who spend our time in a forgiving language such as PHP or Python) that the API we provide is a promise to the rest of the world.  What it says is that "When you provide me with ${this} I will provide you with ${that}"

The super important (and insidiously non obvious) thing about this is that if you do not provide a written promise (documentation for your API) then the *behavior* of your API becomes the implicit promise.

But the most important thing is that when your documentation is wrong the promise of your actual behavior wins every single time.

## Keep your promises

When your promises don't match your actual results is when things get super super hairy.

Lets take a look at a completely hypothetical situation.  

1. You have an API that is documented to return a json object with a success member which should be a boolean value.
2. You have a case (maybe all cases) where success is actually rendered as an integer (0 for false, 1 for true)
3. John has an app written in a strongly typed language which works around this by defining success as an integer type instead of a boolean type.  Because john was busy and never got around to letting you know. Or maybe john never knew because he simply introspected your API and worked backwards from the responses that you gave. Now johns app has 100k users depending on this functionality
4. Mary is writing an app, and because Mary doesn't like to play fast and loose (and also Mary doesn't want her app to break later on) she submits an issue pointing out that you are returning the wrong type.

Now at this point you are trapped.  The existing user base (and by extension their userbase) is comitted to integers.  And you only have 4 options.

1. You can break 100k people all at once and cripple an existing and deployed application.
2. You can version your API -- an entire new version to correct what should be a boolean value!
3. You can work with John to roll out a new version of the app which can handle both (but maybe his app is in the iOS app store, and getting everyone to update is impossible or takes a long time, and also even making it possible requires a length (or maybe even costly in some cases) review process by yet another party)
4. You make a really sad face and change your promise... To reflect that you are going to forever do the, really, less actually correct thing.

Because you wrote an API whose promise was wrong, or whose promise was missing, you have painted yourself into a very undesirable corner.  You're now in a place where doing the right thing for the right reasons is the wrong move.

So do yourself. Really do everyone one of two favors depending on which position you find yourself in.

If you're producing an API. Take extra care to make sure that your results match your documentation (and you need to have documentation)

If you're consuming an API. Don't be like john.  Don't work backwards from the data -- work forwards from the docs. And if the docs are wrong you should submit a ticket and wait to get it fixed (or at the very very very least make sure your workaround deals with BOTH the documented expectation and the actual incorrect return value)

## In conclusion

Just like a child it takes a village to raise a good, decent, hard working API.
