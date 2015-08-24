# assert with optional message

Adding an optional message to Dart's assert statement.

## Contact information

* Seth Ladd <sethladd@google.com>
 
### Original repo location

* https://github.com/sethladd/dep_assert_with_optional_message

### Stakeholders

* Ian Hickson <ianh@google.com>

## Summary

An optional message for the assert statement, displayed when the assert fails.

For example:

`assert(thing != null, "You arrived here because the depleneration hasn't reordered. See http://goo.gl/fenf832f to learn more.");`

## Motivation

Assert statements are very useful for framework authors, and framework users.
Asserts keep the other framework
authors on the right path, and they keep consumers of the framework from getting into
trouble. _Failing fast_ is a great developer experience.

Currently, Dart throws an exception when an assert fails in checked mode. The output of that exception
includes a line and column number, which points the user to the location of the assert.
This is necessary feedback to the user, but not sufficient to give the user a great experience.

Here is an example exception output from Dart 1.12:

```
~/tmp $ dart --checked assert.dart 
Unhandled exception:
'file:///Users/sethladd/tmp/assert.dart': Failed assertion: line 3 pos 8: 'thing != null' is not true.
#0      _AssertionError._throwNew (dart:core-patch/errors_patch.dart:27)
#1      main (file:///Users/sethladd/tmp/assert.dart:3:8)
#2      _startIsolate.<anonymous closure> (dart:isolate-patch/isolate_patch.dart:261)
#3      _RawReceivePortImpl._handleMessage (dart:isolate-patch/isolate_patch.dart:148)
```

Because failed asserts can trigger from the framework, and not a user's code,
the user is left wondering "Why did this happen? What can I do about it? Did I mess
up, or did my framework fail?".

Unforunately, there isn't a standard or clear way for framework authors to include
a helpful message with an assert. Ideally, the user would see something like this:

```
~/tmp $ dart --checked assert.dart 
Unhandled exception:
'file:///Users/sethladd/tmp/assert.dart': Failed assertion: line 3 pos 8: 'thing != null' is not true. Message: You arrived here because the depleneration hasn't reordered. See http://goo.gl/fenf832f to learn more.
#0      _AssertionError._throwNew (dart:core-patch/errors_patch.dart:27)
#1      main (file:///Users/sethladd/tmp/assert.dart:3:8)
#2      _startIsolate.<anonymous closure> (dart:isolate-patch/isolate_patch.dart:261)
#3      _RawReceivePortImpl._handleMessage (dart:isolate-patch/isolate_patch.dart:148)
```

Now the user has context for the failed assertion, and ideally has a link to learn more, address the
issue, and move on.

## Examples

Now that you've hopefully sold us caring about the problem, show us your solution. We'll get into the general behavior soon, but first, walk us through a few realistic examples of how the proposal would be used in practice.

Humans tend to absorb information better if you start with examples and then show the general principle, so this is the place for your proposal's highlight reel. It gives us readers a chance to develop and intuition for what your proposal does and get a feel for its user experience.

Try to use real-world examples. Keep them small, but with realistic context. "Foo" and "bar" make it hard to evaluate how the proposal would look in real code.

## Proposal

At this point, we should care about your problem and think your solution looks beautiful. Now it's time to dig in and explain how it works in detail. Cover the syntax and semantics precisely and clearly.

Describe its behavior in common usage, but also how it acts in bizarre corner cases. You don't need to be as precise as actual language spec verbiage, but this is the place where you start heading in that direction.

This is probably the least fun, but ultimately the most important section.

## Alternatives

While you may think of your solution as a perfect snowflake, there are other pretty snowflakes out there. Describe alternate solutions that cover the same space as this proposal. Compare them to the proposal and each other and explain why they were rejected.

What advantages do these alternatives have that the chosen one lacks? What does the selected proposal offer to outweigh them? Being rigorous here shows that you've done your due diligence and fully explored both the problem and the potential solution space. We want to be confident that we have not just *a* solution to the problem, but the *best* one.

## Implications and limitations

A good language is a cohesive whole. Even a small, well-defined proposal still has to hang together with the rest of the Dart platform.

- How does this feature interact with the other language features&mdash;in both good and bad ways&mdash;?
- If we adopt this feature what will it prevent us from doing now? What do we sacrifice for this?
- How does it affect the future evolution of the language?

## Deliverables

These don't have to be done *initially* but before the proposal can be given the final stamp of approval before it's shipped off to TC 52, three key artifacts are required.

These will live in the same repo as your proposal.

### Language specification changes

These the actual, precise diffs for how the [language spec][] will be modified.
Time to brush up on your [TeX][]. Being able to do this well is a rare gift, so
you are not expected to be a spec wizard. Fortunately, we have a couple on
staff. Do your best and as your proposal matures, we will help.

### A working implementation

Nothing flushes out the problems with a feature quite like writing it in cold, hard executable code. Before a proposal can be approved, some kind of working implementation must be provided.

Again, you don't have to have this on day one. However, you should make as much progress on this as early as you can. You *are* expected to be able to write working code, so there's nothing really holding you back. Doing this before you show your proposal to the world is a good sanity check to ensure you don't propose something that turns out to be impossible to implement. (It happens to the best of us.)

It's not necessary to actually implement your feature in the native Dart VM or dart2js compiler (though you can and will certainly get street cred for doing so). Instead, you may:

* Write some sort of standalone prototype implementation.
* Write a [transpiler][] that takes Dart code using your feature and translates it to "vanilla" Dart code.

What matters is that you demonstrate that the feature can feasibly be implemented in a reasonable amount of code with good performance.

### Tests

Write tests that could be run under an implementation to determine if it implements your proposal correctly. Make sure to test both success and failure cases. Be thorough. Programming languages have combinatorial input, so defining a comprehensive test suite is difficult.

Imagine an adversary is going to write a malicious implementation of your proposal that passes its tests but otherwise does things so horrible it will cause the language team to run screaming from your proposal. Don't let the adversary win.

## Patents rights

TC52, the Ecma technical committee working on evolving the open [Dart standard][], operates under a royalty-free patent policy, [RFPP][] (PDF). This means if the proposal graduates to being sent to TC52, you will have to sign the Ecma TC52 [external contributer form][] and submit it to Ecma.

[tex]: http://www.latex-project.org/
[language spec]: https://www.dartlang.org/docs/spec/
[dart standard]: http://www.ecma-international.org/publications/standards/Ecma-408.htm
[rfpp]: http://www.ecma-international.org/memento/TC52%20policy/Ecma%20Experimental%20TC52%20Royalty-Free%20Patent%20Policy.pdf
[external contributer form]: http://www.ecma-international.org/memento/TC52%20policy/Contribution%20form%20to%20TC52%20Royalty%20Free%20Task%20Group%20as%20a%20non-member.pdf
