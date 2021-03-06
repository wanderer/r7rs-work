## Introduction

This proposal defines immutable data structures for sets and bags. A structure is immutable when all its operations leave the structure unchanged and still available to any procedure that holds a pointer to it. Note that none of the procedures specified here ends with a `!`.

Immutable structures are sometimes called *persistent* and are closely related to *pure-functional* (a.k.a. *pure*) structures. The availability of immutable data structures facilitates writing efficient programs in the pure-functional style.

## Rationale

<p>Sets are a standard part of the libraries of many high-level programming languages, including Smalltalk, <a href="http://docs.oracle.com/javase/6/docs/api/java/util/Set.html">Java</a>, and <a href="http://www.cplusplus.com/reference/set/set/">C++</a>.</p>

<p>Bags are useful for counting anything from a fixed set of possibilities, e.g. the number of each type of error in a log file or the number of uses of each word in a lexicon drawn from a body of documents.  Although other data structures can serve the same purpose, using bags clearly expresses the programmer's intent and allows for optimization.</p>

<p>Insofar as possible, the names in this SRFI are harmonized with the names used for <a href="http://srfi.schemers.org/srfi-113/srfi-113.html">SRFI 113</a>.  SRFI 113 sets and bags are linear-update: that is, a modification to the data structure returns a modified version and may or may not mutate the original version.  In this SRFI, the data structures are guaranteed not to be mutated.</p>

<p>It's possible to use the general sets of this SRFI to contain characters, but the use of the linear-update sets of <a href="http://srfi.schemers.org/srfi-14/srfi-14.html">SRFI 14</a> is recommended instead.</p>

<p>Immutable sets and bags do not have a lexical syntax representation.  It's possible to use <a href="http://srfi.schemers.org/srfi-108/srfi-108.html">SRFI 108</a> quasi-literal constructors to create them in code, but this SRFI does not standardize how that is done.</p>

<p>The interface to immutable sets and bags depends on <a href="http://srfi.schemers.org/srfi-114/srfi-128.html">SRFI 128</a> comparators. Comparators conveniently package the equality predicate of the set with the comparison procedure needed to implement the set efficiently.</p>

## Specification

Immutable sets (also known as isets) and immutable bags (also known as ibags) are disjoint from all other Scheme types with the possible exception of immutable maps.  They may or may not be disjoint from one another.

We specify required time efficiency upper bounds using big-O notation. We note when, in some cases, there is "slack" between the required bound and the theoretically optimal bound for an operation. Implementations may use data structures with amortized time bounds, but should document which bounds hold in only an amortized sense. The use of randomized data structures with expected time bounds is discouraged.

A sorted set data structure stores a finite collection of unique elements with a defined [comparator](http://ccil.org/~cowan/temp/srfi-128.html).

The following conditions are errors:

* if the comparator does not provide an ordering procedure

* if a procedure defined in this SRFI is invoked on sets or bags with distinct comparators (in the sense of `eq?`)

* if an object is mutated while it is contained in a set or bag

* if an object is added to a set or bag which does not satisfy the type test predicate of the comparator

* if an object is added to or removed from  a set or a bag while iterating over it

These requirements can be satisfied by many flavors of *self-balancing binary trees.* Red-black trees, 1-2 brother trees, and labeled 2-3 finger trees are particularly convenient in an immutable context.

### Constructors

If two elements are inserted into a set that are equal in the sense of the set's comparator but are not `eqv?`, the first to be specified or generated prevails.

`(iset `*comparator* [[|*element* ...]]`)`

Returns a set containing *elements*, where *comparator* provides the criterion of identity and ordering.  Takes O(n log n) time.

`(iset-unfold `*stop? mapper successor seed*`)`

Invokes the predicate *stop?* on *seed*.  If it returns false, generate the next result by applying *mapper* to *seed*, generate the next seed by applying *successor* to *seed*, and repeat this algorithm with the new seed.  If *stop?* returns true, return a set containing the results.  Takes O(n log n) time.

### Predicates

`(iset? `*obj*`)`

Returns `#t` if *obj* is a set, and `#f` otherwise.  Takes O(1) time.

`(iset-empty? `*set*`)`

Returns `#t` if *set* contains zero elements, and `#f` otherwise.  Takes O(1) time.

`(iset-member? `*set obj*`)`

Returns `#t` if *set* contains *obj*, and `#f` otherwise.  Takes O(log n) time.

### Accessors

`(iset-min `*set*`)`

`(iset-max `*set*`)`

Returns the least/greatest element of *set*.  It is an error for *set* to be empty. Takes O(log n) time; O(1) is optimal.

`(iset-comparator `*set*`)`

Returns the comparator of *set*.  Takes O(1) time.

`(iset-predecessor `*set obj failure*`)`

`(iset-successor `*set obj failure*`)`

Returns the element that immediately precedes/succeeds *obj* in *set's* ordering. If no such element exists because *obj* is the minimum/maximum element, or because *set* is empty, returns the result of invoking the thunk *failure*. Takes O(log n) time.

### Functional update

`(iset-adjoin `*set obj*`)`

Returns a set which contains the elements of *set* and *obj* as well.  If there is already an element of *set* that is equal (in the sense of the comparator) to *obj*, the existing element of *set* prevails.  Takes O(log n) time.

`(iset-adjoin-all `*set list*`)`

Returns a set which contains the elements of *set* and the elements of *list* as well.  It is an error if the elements of *list* are not distinct and increasing in the sense of the comparator of *set*.  If there is already an element of *set* which is equal (in the sense of the comparator) to an element of *list*, the element of *set* prevails.  Takes O(k log n) time, where *k* is the length of *list*.

`(iset-replace `*set obj*`)`

Returns a set which contains the elements of *set* and *obj* as well.  If there is already an element of *set* that is equal (in the sense of the comparator) to *obj*, *obj* prevails.  Takes O(log n) time.

`(iset-delete `*set obj*`)`

Returns a set which contains the elements of *set* with the exception of *obj*, if present.  If there is already an element of *set* that is equal (in the sense of the comparator) to *obj*, the element of *set* prevails.  Takes O(log n) time.

`(iset-delete-keys `*set list*`)`

Returns a set which contains the elements of *set*, excluding the elements of *list*.  It is an error if the elements of *list* are not distinct and increasing in the sense of the comparator of *set*. Takes O(k log n) time, where *k* is the length of *list*.

### The whole set

`(iset-size `*set*`)`

Returns the size of *set* as an exact integer.  May take O(n) time, though O(1) is optimal.

`(iset-find `*set obj failure*`)`

Returns the element equal (in the sense of the comparator of *set*) to *obj* in *set*, or the result of invoking the thunk *failure* if no such element exists. Takes O(log n) time.

`(iset-count `*pred set*`)`

Returns the number of elements in *set* which satisfy *pred* as an exact integer.  Takes O(n) time.

`(iset-any `*pred set*`)`

`(iset-every `*pred set*`)`

Invokes *pred* on the elements of *set* until one of them returns a true/false value, which is then returned.  If there are no such elements, returns `#f`/`#t`.  Takes O(n) time.

### Filtering

`(iset-range= `*set obj*`)`

`(iset-range< `*set obj*`)`

`(iset-range> `*set obj*`)`

`(iset-range<= `*set obj*`)`

`(iset-range>= `*set obj*`)`

Returns a set containing only the elements of `set` that are equal to / less than / greater than / less than or equal to / greater than or equal to *obj*.  Takes O(log n) time, where n is the number of elements in the set.

Note that since set elements are unique, `iset-range=` returns a set of at most one element.

`(iset-filter `*pred set*`)`

`(iset-remove `*pred set*`)`

Returns a set containing only those elements on which *pred* returns true/false. Takes O(n log n) time; O(n) is optimal.

`(iset-partition`*pred set*`)`

Returns two values, `(iset-filter `*pred set*`)` and `(iset-remove `*pred set*`)` respectively, but may be more efficient.

### Folding and mapping

`(iset-fold `*proc nil set*`)`

The fundamental set iterator. Equivalent to, but may be more efficient than, `(fold `*proc base* ` (iset->increasing-list `*set*`))`.  Takes O(n) time.

`(iset-fold-right `*proc nil set*`)`

The fundamental set iterator. Equivalent to, but may be more efficient than, `(fold-right `*proc base* ` (iset->increasing-list `*set*`))`.  Takes O(n) time.

`(iset-map/monotone `*proc set* [[|*comparator* ]]`)`

Returns a set containing the result of invoking *proc* on every element in *set*.  It is an error unless *proc* is a *monotone* unary procedure that preserves the order of set elements. Observe that mapping a set of unique elements with a monotone function yields a set of unique elements, so element uniqueness follows from the monotonicity assumption. If *comparator* is given, it is the comparator of the result; otherwise the result uses the same comparator as *set*. Takes O(n) time.

`(iset-map`*proc set* [[|*comparator* []] ]( *merger*)`)`

Like `iset-map/monotone`, except that *proc* is not required to be monotone. The `merger` procedure is used to select among any duplicate elements (in the sense of the comparator of *set*) that might be created; it returns the value to be used; if absent, the element chosen is implementation-specific.  Takes O(n log n) time.

`(iset-for-each `*proc set*`)`

Invokes *proc* on every *element* in *set*.  The result is unspecified. Takes O(n) time.

### Subsets

Note: The following three predicates do not obey the trichotomy law and therefore do not constitute a total order on sets.

`(iset=? `*set1 set2* ...`)`

Returns `#t` if each *set* contains the same elements, and `#f` otherwise.

`(iset<? `*set1 set2* ...`)`

Returns `#t` if each *set* other than the last is a proper subset of the following set, and `#f` otherwise.

`(iset>? `*set1 set2* ...`)`

Returns `#t` if each *set* other than the last is a proper superset of the following set, and `#f` otherwise.

`(iset<=? `*set1 set2* ...`)`

Returns `#t` if each *set* other than the last is a subset of the following set, and `#f` otherwise.

`(iset>=? `*set1 set2* ...`)`

Returns `#t` if each *set* other than the last is a superset of the following set, and `#f` otherwise.

### Conversion

`(iset->list `*set*`)`

Returns a list containing the elements of `set` in increasing order. Takes O(n) time.

`(increasing-list->iset comparator list)`

Returns a set containing the elements of *list* and using *comparator*. It is an error for *list* to be anything other than a proper list of elements in increasing order. Takes O(n log n) time; O(n) is optimal.

`(list->iset `*comparator list [[|*merger* ]]`)`

Returns a set containing the elements of *list* and using *comparator*. It is an error if `list` is not a proper list, but it may contain duplicates and need not be in order.  The `merger` procedure is used to select among any duplicate elements (in the sense of the comparator of *set*) that might be created; it accepts the existing and new elements and returns the value to be used.  Takes O(n log n) time.

`(generator->iset `*generator*`)

`(iset->generator `*iset*`)`

Converts a [SRFI 121](http://srfi.schemers.org/srfi-121/srfi-121.html) generator to and from a set.

### Set-theoretic operations

`(iset-union `*set* ... `)`

`(iset-intersection `*set* ... `)`

`(iset-difference `*set* ... `)`

`(iset-xor `*set,,1,, set,,2,,*`)`

Returns a set containing the union/intersection/difference/symmetric difference of the arguments. All the arguments must be sets sharing an equivalent comparator. The set operator is applied in a left-associative order. If an element is found in more than one set, the first set in the argument list prevails.  May take O(kn log n) time, where k is the number of sets and n is the number of elements involved, though O(kn) time is optimal.

## Bags
Bags are like sets, but can contain the same object more than once.  However, if two elements that are the same in the sense of the comparator, but not in the sense of <code>eqv?</code>, are both included, it is not guaranteed that they will remain distinct when retrieved from the bag.  It is an error for a single procedure to be invoked on bags with different comparators.

</p><p>

The procedures for creating and manipulating bags are the same as those for sets, except that <code>iset</code> is replaced by <code>ibag</code> in their names, and that adjoining an element to a bag is effective even if the bag already contains the element.  If two elements in a bag are the same in the sense of the bag's comparator, the implementation may in fact store just one of them.
</p>

<p>The <code>ibag-union</code>, <code>ibag-intersection</code>, <code>ibag-difference</code>, and <code>ibag-xor</code> procedures behave as follows when both bags contain elements that are equal in the sense of the bags' comparator:</p>

<ul>
<li><p>For <code>bag-union</code>, the number of equal elements in the result is the largest number of equal elements in any of the original bags.</p></li>

<li><p>For <code>bag-intersection</code>, the number of equal elements in the result is the smallest number of equal elements in any of the original bags.</p></li>

<li><p>For <code>bag-difference</code>, the number of equal elements in the result is the number of equal elements in the first bag, minus the number of elements in the other bags (but not less than zero).</p></li>

<li><p>For <code>bag-xor</code>, the number of equal elements in the result is the absolute value of the difference between the number of equal elements in the first and second bags.</p></li></ul>

<h3 id="Additionalbagprocedures">Additional bag procedures</h3>

<p><code>(ibag-sum </code><em>set<sub>1</sub> set<sub>2</sub></em> ... <code>)</code></p>

<p>The <code>ibag-sum</code> procedure returns a bag containing all the unique elements in all the <em>bags</em>, such that the count of each unique element in the result is equal to the sum of the counts of that element in the arguments.  It differs from <code>ibag-union</code> by treating identical elements as potentially distinct rather than attempting to match them up.</p>

<p><code>(ibag-product </code><em>n bag</em><code>)</code></p>

The <code>bag-product</code> procedure returns a bag containing all the unique elements in <em>bag</em>, where the count of each unique element in the bag is equal to the count of that element in <code>bag</code> multiplied by <em>n</em>.

</p><p><code>(ibag-unique-size </code><em>bag</em><code>)</code></p><p>

Returns the number of unique elements of <em>bag</em> as an exact integer.

</p><p><code>(ibag-element-count </code><em>bag element</em><code>)</code></p><p>

Returns an exact integer representing the number of times that <em>element</em> appears in <em>bag</em>.

</p><p><code>(ibag-for-each-unique </code><em>proc bag</em><code>)</code></p><p>

Applies <em>proc</em> to each unique element of <em>bag</em> in arbitrary order, passing the element and the number of times it occurs in <em>bag</em>, and discarding the returned values.  Returns an unspecified result.

</p><p><code>(ibag-fold-unique </code><em>proc nil bag</em><code>)</code></p><p>

Invokes <em>proc</em> on each unique element of <em>bag</em> in arbitrary order, passing the number of occurrences as a second argument and the result of the previous invocation as a third argument.  For the first invocation, <em>nil</em> is used as the third argument.  Returns the result of the last invocation, or <em>nil</em> if there is none.

</p><p><code>(ibag-increment </code><em>bag<code> </code>element<code> </code>count</em><code>)</code></p><p><code>(ibag-decrement </code><em>bag<code> </code>element<code> </code>count</em><code>)</code></p><p>

Procedures that return a bag with the same elements as <em>bag</em>, but with the element count of <em>element</em> in <em>bag</em> increased or decreased by the exact integer <em>count</em> (but not less than zero).

</p><p><code>(ibag-&gt;iset </code><em>bag</em><code>)</code></p><p><code>(iset-&gt;ibag </code><em>set</em><code>)</code></p><p>

The <code>ibag-&gt;iset</code> procedure returns a set containing the unique elements (in the sense of the equality predicate) of <em>bag</em>.  The <code>iset-&gt;ibag</code> procedure returns a bag containing the elements of <em>set</em>. In all cases, the comparator of the result is the same as the comparator of the argument or arguments.

</p><p><code>(ibag-&gt;alist </code><em>bag</em><code>)</code></p><p><code>(alist-&gt;ibag </code><em>comparator alist</em><code>)</code></p><p>

The <code>ibag-&gt;alist</code> procedure returns a newly allocated alist whose keys are the unique elements of <em>bag</em> and whose values are the number of occurrences of each element.  The <code>alist-&gt;ibag</code> returning a bag based on <em>comparator</em>, where the keys of <em>alist</em> specify the elements and the corresponding values of <em>alist</em> specify how many times they occur.</p>

