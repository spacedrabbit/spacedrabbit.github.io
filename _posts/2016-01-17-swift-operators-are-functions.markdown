---
layout: post
title:  "Swift Operators Are Functions"
date:   2016-01-17 21:58:49 -0500
categories: jekyll update
---

As [previously discussed][swift1], the turning point for me with Swift was watching a `func` converted into a succinct three character statement. Summarily, the code looks like: 
<script src="https://gist.github.com/spacedrabbit/c658075aa0b3c276334f.js"></script>

This is possible in part thanks to Swift’s emphasis on [types][swiftTypes]. In Swift, a function that expects another function as a parameter, is really checking for the function's *type* to match. In the case of the code above, the `performOperation` method is expecting a *type* of `(Double, Double) -> Double`. So as long as the function we pass matches that *type*, we can ensure that Swift will accept it as being valid. 

What's of particular interest here is that all [operators][swiftOperators] (unary, binary, and tertiary) are treated as functions in Swift. In the example above, we substitute the `multiply` function with the binary operator `*`. To really explain *why* this is possible, let's for a moment look at Swift's declaration of the [`*` operator][operatorReference]: 

{% highlight swift %}
infix operator * {
    associativity left
    precedence 150
}
{% endhighlight %}

As [Mattt of NSHipster][NSHipster] and [Tammo Freese (via Medium)][tf] explain, the declaration defines a few things:
  
  1. The symbol for the operator will be `*`
  2. It will be `infix`, meaning in-between two operands
  3. And additionally it details its `precedence` (order of operation) and `associativity` (rules for handling operators of equal precedence).

In order to actually use an operator, Swift requires that you define functions that describe its use-cases. There are many for each operator to allow for the multitude of different *types* the operator could be used with. For example, just three of the `*` operator's (many) functions are:

{% highlight swift %}
func *(lhs: Double, rhs: Double) -> Double
func *(lhs: Float, rhs: Float) -> Float
func *(lhs: Int8, rhs: Int8) -> Int8
...
{% endhighlight %}

These functions, and Swift's *type inference*, are what allow for implicitly handling any kind of multiplication without having to include the operand's *type* when using the `*` operator.  


##Bringing it together
If you recall, in the snippet from earlier we wrote

{% highlight swift %}
func performOperation(operation: (Double, Double) -> Double ) { ... } 
{% endhighlight %}

which was called in a `switch` statement 

{% highlight swift %}
case "*": performOperation(multiply)
{% endhighlight %}

where 

{% highlight swift %}
func multiply(opt1: Double, opt2: Double) -> Double { }
{% endhighlight %}

`performOperation` is just interested in a parameter of type `Double, Double -> Double` to accept. Which just so happens to be exactly one of `*`'s *function type*:

{% highlight swift %}
func *(lhs:Double, rhs:Double) -> Double
{% endhighlight %}

Because `*`'s *type* matches the expected type for `performOperation` we can really see why the substitution works! 

Isn't the esoteric interesting?

Oh, and P.S. We can simplify our expression even further to just be
{% highlight swift %} case "*": performOperation(*) {% endhighlight %}
This is because we don't change `*`'s *type* by omitting the two variables Swift implicitly adds for unnamed parameters. And really, they could just be added later within `performOperation`. 

[swift1]: http://catthoughts.ghost.io/begrudgingly-swift/
[swiftTypes]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Types.html#//apple_ref/doc/uid/TP40014097-CH31-ID445
[swiftOperators]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AdvancedOperators.html#//apple_ref/doc/uid/TP40014097-CH27-ID28
[operatorReference]: https://developer.apple.com/library/ios//documentation/Swift/Reference/Swift_StandardLibrary_Operators/index.html#//apple_ref/doc/uid/TP40016054
[tf]: https://medium.com/swift-programming/facets-of-swift-part-5-custom-operators-1080bc78ccc
[NSHipster]: http://nshipster.com/swift-operators/