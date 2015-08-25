# assert with optional message

Adding an optional message to Dart's assert statement.

## Contact information

* Seth Ladd <sethladd@google.com>
* Original location: https://github.com/sethladd/dep_assert_with_optional_message

### Stakeholders

* Ian Hickson <ianh@google.com>

## Summary

An optional message for the assert statement, displayed when the assert fails.

For example:

`assert(thing != null, "You arrived here because the depleneration hasn't reordered. See http://goo.gl/fenf832f to learn more.");`

## Motivation

Assert statements are very useful for framework authors, and framework users.
Asserts keep the other framework authors on the right path, and they keep consumers of
the framework from getting into trouble. _Failing fast_ is a great developer experience.

Currently, Dart throws an exception when an assert fails in checked mode. The output of that exception
includes a line and column number, which points the user to the location of the assert.
This is necessary feedback to the user, but not sufficient to give the user a great experience.

For example, here is an exception output from Dart 1.12:

```
~/tmp $ dart --checked assert.dart 
Unhandled exception:
'file:///Users/sethladd/tmp/assert.dart': Failed assertion: line 3 pos 8: 'thing != null' is not true.
#0      _AssertionError._throwNew (dart:core-patch/errors_patch.dart:27)
#1      main (file:///Users/sethladd/tmp/assert.dart:3:8)
#2      _startIsolate.<anonymous closure> (dart:isolate-patch/isolate_patch.dart:261)
#3      _RawReceivePortImpl._handleMessage (dart:isolate-patch/isolate_patch.dart:148)
```

Because failed asserts can often trigger from the framework, and not a user's code,
the user is left wondering "Why did this happen? What can I do about it? Did I mess
up, or did my framework fail?".

Unforunately, there isn't a standard or clear way for framework authors to include
a helpful message with an assert. Ideally, the user would see something like this:

```
~/tmp $ dart --checked assert.dart 
Unhandled exception:
'file:///Users/sethladd/tmp/assert.dart': Failed assertion: line 3 pos 8: 'thing != null' is not true.
Message: You arrived here because the depleneration hasn't reordered. See http://goo.gl/fenf832f to learn more.
#0      _AssertionError._throwNew (dart:core-patch/errors_patch.dart:27)
#1      main (file:///Users/sethladd/tmp/assert.dart:3:8)
#2      _startIsolate.<anonymous closure> (dart:isolate-patch/isolate_patch.dart:261)
#3      _RawReceivePortImpl._handleMessage (dart:isolate-patch/isolate_patch.dart:148)
```

Now the user has context for the failed assertion, and ideally has a link to learn more,
the user has a better chance of addressing the issue.

## Examples

From Sky's [flex.dart](https://github.com/domokit/sky_engine/blob/8b88916443a29d0fa4bdb8264ee6db583b534504/sky/packages/sky/lib/rendering/flex.dart):

Before:

```
// ...
      if (flex > 0) {
        // Flexible children can only be used when the RenderFlex box's container has a finite size.
        // When the container is infinite, for example if you are in a scrollable viewport, then 
        // it wouldn't make any sense to have a flexible child.
        assert(canFlex && 'See https://github.com/domokit/sky_engine/blob/master/sky/packages/sky/lib/widgets/flex.md' is String);
        totalFlex += child.parentData.flex;
      } else {
        BoxConstraints innerConstraints;
// ...
```

After:

```
// ...
      if (flex > 0) {
        // Flexible children can only be used when the RenderFlex box's container has a finite size.
        // When the container is infinite, for example if you are in a scrollable viewport, then 
        // it wouldn't make any sense to have a flexible child.
        assert(canFlex, 'See https://github.com/domokit/sky_engine/blob/master/sky/packages/sky/lib/widgets/flex.md');
        totalFlex += child.parentData.flex;
      } else {
        BoxConstraints innerConstraints;
// ...
```

## Proposal

(disclaimer: I am not a language designer. I expect this part to get changed by experts.)

### Language change

```
assertStatement:
  assert ‘(’ conditionalExpression ', '? message? ‘)’ ‘;’
;

message:
  stringLiteral
;
```

If the message is provided, the message _m_ is evaluated to an object o.
It is a dynamic type error if _o_ is not of type String.

If the assertion failed, an AssertionError is thrown. If
_o_ is not null, the AssertionError is populated with the message.

(Runtimes are encouraged to display the message with the output of
the failed assertion.)

It is a static type warning if the type of _o_ may not be assigned to String.

### Library change

The class `AssertionError` is modified to add an optional message to its constructor,
and a new getter.

```
library dart.core;

class AssertionError {
  /// A contextual message related to why
  /// the assertion failed, and potentially
  /// what to do to resolve the failed assertion.
  /// May be null.
  String get message;
  
  /// Represents a failed assertion.
  ///
  /// The optional `message` should contain useful
  /// information for the developer, often to help
  /// the developer fix the failed assertion.
  AssertionError([String message]) { /* ... */ }
}
```

## Alternatives

Workarounds include:

```
assert(canFlex && 'See https://github.com/domokit/sky_engine/blob/master/sky/packages/sky/lib/widgets/flex.md' is String);
```

And:

```
assert(() => canFlex || throw "See https://github.com/domokit/sky_engine/blob/master/sky/packages/sky/lib/widgets/flex.md");
```

Of course, both do enable a message to be displayed to the user if the assert fails, but neither option is obvious.

The advantage of proper support for an optional assert message is that it is likely to be used. We want to encourage framework authors to keep their users on the golden path, and thus we want to provide simple and obvious ways to provide instructions/feedback to their users.

## Implications and limitations

This proposal appears to be localized to the `assert()` statement.

Some users may expect the optional message to allow for an expression that
returns a String.

## Deliverables

### Language specification changes

TBD

### A working implementation

TBD

### Tests

TBD

## Patents rights

TC52, the Ecma technical committee working on evolving the open [Dart standard][], operates under a royalty-free patent policy, [RFPP][] (PDF). This means if the proposal graduates to being sent to TC52, you will have to sign the Ecma TC52 [external contributer form][] and submit it to Ecma.

[tex]: http://www.latex-project.org/
[language spec]: https://www.dartlang.org/docs/spec/
[dart standard]: http://www.ecma-international.org/publications/standards/Ecma-408.htm
[rfpp]: http://www.ecma-international.org/memento/TC52%20policy/Ecma%20Experimental%20TC52%20Royalty-Free%20Patent%20Policy.pdf
[external contributer form]: http://www.ecma-international.org/memento/TC52%20policy/Contribution%20form%20to%20TC52%20Royalty%20Free%20Task%20Group%20as%20a%20non-member.pdf
