Literal object methods VS prototyped methods? What’s about memory?
=================================================================

I’ve often read on some web articles related to JavaScript objects that literal instantiable objects (that we would have called class in some others objects languages) where heavier than prototyped objects because methods defined directly into literal objects were duplicated for each instance instead of being instantiated from the prototype.

I have been misled for ages by this assertion because I was thinking it would means that methods where really, fully duplicated - code included. Thus, for a long time, I reluctantly used literal instantiable objects, but the fact is – memory overconsumption may be fairly limited, because the part of duplicated stuff for methods defined into literal instantiated object is so!

I discovered that, a few years ago, by reading a little comment from Sasha Chedygov : http://stackoverflow.com/questions/9641155/object-literal-notation-vs-prototype-speed-and-memory#comment12424338_9641155, I realized then that browser’s memory wasn’t so damn sucked by literal instantiable objects than many would imply it in web articles and discussions. But how calculate aftermath of choosing literal instantiable objects form instead of prototyped one?

A thing that I learned from our great ninja Master John Reisig by reading his masterpiece book “Sectets of the Javascript Ninja” is that testing by ourselves JavaScript behaviors is a good way to have answers we want :). So let’s do it.

Test time
---------

I’m using chrome Version 33.0.1750.154 m for my test to write this document. For testing, we’ll use two simple equivalent scripts that perform the same work in two ways: 

_Literal object:_

<img src="/img/im4.png" width="50%" height="50%">
 
_Prototyped object:_

<img src="/img/im4.png" width="50%" height="50%">

Let’s set a breakpoint on the Alert statement for each script, and run them. When execution beaks let’s profile the heap and have a look at it:

<img src="/img/im1.png" width="50%" height="50%">

First interesting matter of fact, by opening some LiteralTest instances (in our test using literal method) and expanding getMemConsumer function property inside, we can see that code is referring to a same object ID: function code actually not duplicated!
Let’s have a step back and look at some interesting points

<img src="/img/im2.png" width="50%" height="50%">

Thus in the prototyped test:

<img src="/img/im3.png" width="50%" height="50%">

Literal Object test:

- Global Memory : 3.1mb
- Test object(LiteralTest) instances:
    - 68b / instance
    - 10000 instances
    - 68 * 10000 = 680000 b (or about cause we have some extra bytes in the LiteralTest total amount)
Prototype Object test:
- Global Memory : 2.5mb
- Test object (PrototypedTest) instances:
    - 12b / instance
    - 10000 instances
    - 12 * 10000 = 120000b

The real difference is 680000b – 120000b = 560000b (around 0.6mb)

That seems to match to rounded global heap size gap 2.5mb – 3.1mb = 0.6mb

So can we assume that the difference between prototyped function and literal function is 56 o per methods? In this case if we had an object with 10 methods instead of 1 the gap would be 56 x 10 x 10000 = 5600000 o (5,6mb)

Let’s test it:

With 10 methods for each case we now have:

- With literal object: 8.0mb
- With prototyped object: 2.5mb

2.5mb - 8.0mb = 5.5 we seem to get it!

Conclusion:
-----------

So the gap is 56 o x objects instance x number of methods for webkit (Chrome)! It’s certainly a bit different for Gecko based browsers and IE but not that different (hope so!). Feel free to test it yourself and report results here.

So 56b per method per instance, it’s important but not that much. If I take for example an object that would be instantiated 100 times using five methods, the difference is 56 x 5 x 100 = 28000 (28kb). Is it an expensive cost to pay to be able to grant access to wonderful closure capabilities offered by literal objects such as privacy?

However this amount of extra memory can be relevant in certain case, when object have many methods or are instantiated many times.

So we (as often in development) have to make choices, but at least I can now calculate their aftermath 