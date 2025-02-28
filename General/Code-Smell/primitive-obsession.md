# Primitive Obsession

## The Smell

Primitive Obsession is using primitive data types to represent domain ideas. For example, we use a String to represent a message, an Integer to represent an amount of money, or a Struct/Dictionary/Hash to represent a specific object.

## The Fix

Typically, we introduce a `ValueObject` in place of the primitive data, then watch like magic as code from all over the system shows FeatureEnvySmell and wants to be on the new `ValueObject`. We move those methods, and everything becomes right with the world.
When I introduce a `ValueObject`, I expect it to become "attractive code", meaning literally a class/module that attracts behavior towards it as new methods/functions. For example, instead of scattering all the parsing/formatting behavior for dates stored as text, introduce a DateFormat class which attracts that behavior as methods called parse() and format(), almost like magic.

## References

- [Primitive Obsession](https://wiki.c2.com/?PrimitiveObsession)