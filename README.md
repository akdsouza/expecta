# Expecta

An Objective-C/Cocoa Matcher Library

## HOW IS IT BETTER?

The main advantage of using Expecta over other matcher frameworks is that you do not have to specify the data types. Also, the syntax of Expecta matchers is much more readable and does not suffer from parenthesitis. If you have used [Jasmine](http://pivotal.github.com/jasmine/) before, you will feel right at home!

OCHamcrest

```objective-c
assertThat(@"foo", is(equalTo(@"foo")));
assertThatUnsignedInteger(foo, isNot(equalToUnsignedInteger(1)));
assertThatBool([bar isBar], is(equalToBool(YES)));
assertThatDouble(baz, is(equalToDouble(3.14159)));
```

vs.

Expecta

```objective-c
expect(@"foo").toEqual(@"foo");
expect(foo).Not.toEqual(1);
expect([bar isBar]).toEqual(YES);
expect(baz).toEqual(3.14159);
```

## STATUS

Still under heavy development, but usable.

## BUILT-IN MATCHERS

>`expect(x).toEqual(y);` compares objects or primitives x and y and passes if they are identical (==) or equivalent (isEqual:).
>
>`expect(x).toBeNil();` passes if x is nil.
>
>`expect(x).toBeTruthy();` passes if x evaluates to true (non-zero).
>
>`expect(x).toBeFalsy();` passes if x evaluates to false (zero).
>
>`expect(x).toBeInstanceOf([Foo class]);` passes if x is an instance of a class Foo.
>
>`expect(x).toBeKindOf([Foo class]);` passes if x is an instance of a class Foo or if x is an instance of any class that inherits from the class Foo.
>
>`expect([Foo class]).toBeSubclassOf([Bar class]);` passes if the class Foo is a subclass of the class Bar or if it is identical to the class Bar.
>

*More matchers are coming soon!*

Every matcher's criteria can be inverted by prepending `.Not`: (It is `.Not` with a capital `N` because `not` is a keyword in C++.)

>`expect(x).Not.toEqual(y);` compares objects or primitives x and y and passes if they are *not* equivalent.

## WRITING NEW MATCHERS

Writing a new matcher is easy with special macros provided by Expecta. Take a look at how `.toBeKindOf()` matcher is defined:

`EXMatchers+toBeKindOf.h`

```objective-c
#import "Expecta.h"

EXMatcherInterface(toBeKindOf, (Class expected));
// 1st argument is the name of the matcher function
// 2nd argument is the list of arguments that may be passed in the function call.
// Multiple arguments are fine. (e.g. (int foo, float bar))

#define toBeAKindOf toBeKindOf
```

`EXMatchers+toBeKindOf.m`

```objective-c
#import "EXMatchers+toBeKindOf.h"

EXMatcherImplementationBegin(toBeKindOf, (Class expected)) {
  match(^BOOL{
    return [actual isKindOfClass:expected];
    // Return `YES` if the matcher should pass, `NO` if it should not.
    // The actual value/object is passed as `actual`.
    // Please note that primitive values will be wrapped in NSNumber/NSValue.
  });

  failureMessageForTo(^{
    return [NSString stringWithFormat:@"expected: a kind of %@, "
                                       "got: an instance of %@, which is not a kind of %@",
                                       [expected class], [actual class], [expected class]];
    // Return the message to be displayed when the match function returns `YES`.
  });

  failureMessageForNotTo(^{
    return [NSString stringWithFormat:@"expected: not a kind of %@, "
                                       "got: an instance of %@, which is a kind of %@",
                                       [expected class], [actual class], [expected class]];
    // Return the message to be displayed when the match function returns `NO`.
  });
}
EXMatcherImplementationEnd
```

## LICENSE

Copyright (c) 2011 Peter Jihoon Kim. This software is licensed under the MIT License.