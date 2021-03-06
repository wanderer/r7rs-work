This is a frozen version of the Red Edition ballot, with hyperlinks.  It serves as a definition of the Red Edition, since a single document is not yet ready.

By unanimous consent, the libraries of R7RS-small are all required in implementations of the Red Edition.  The following additional libraries are required:

## Basic libraries

1. What list library should R7RS-large provide?

* No vote
* None
* [SRFI 1](http://srfi.schemers.org/srfi-1/srfi-1.html) (passed as `(scheme list)`)

2. What vector library should R7RS-large provide?

> SRFI 133 is a minor variant of SRFI 43 to make it compatible with R7RS-small by renaming a few procedures. It also includes a few additional procedures not present in SRFI 43.

* No vote
* None
* [SRFI 133](http://srfi.schemers.org/srfi-133/srfi-133.html) (passed as `(scheme vector)`)

3. What string library should R7RS-large provide?

> SRFI 13 is an index-based string library. SRFI 130 provides support for string cursors as well, but does not provide string mutation procedures. SRFI 135 is an immutable text library, but accepts strings as well; if you vote for it, you may want to vote "None" here.

* No vote
* None
* [SRFI 13](http://srfi.schemers.org/srfi-13/srfi-13.html)
* [SRFI 130](http://srfi.schemers.org/srfi-130/srfi-130.html)

No final result: this will be reballoted.

4. What sorting library should R7RS-large provide?

> SRFI 132 is an edited version of the withdrawn SRFI 32, with the algorithm-specific procedures removed and a few additional procedures; it provides sortedness testing, stable and unstable sorting, merging, duplicate removal, median, and selection. R6RS provides only stable sorting and merging.

* No vote
* None
* [R6RS](http://www.r6rs.org/final/html/r6rs-lib/r6rs-lib-Z-H-5.html#node_chap_4)
* [SRFI 132](http://srfi.schemers.org/srfi-132/srfi-132.html) (passed as `(scheme sort)`)

## Sets and maps

5. What general set and bag library should R7RS-large provide?

> SRFI 113 provides linear-update sets and bags, which can be mutable or immutable according to the implementer's preference. Since the interface of SRFI 113 depends on SRFI 114, which has been replaced by SRFI 128, voting for SRFI 113 will cause your vote on comparator libraries to be automatically changed to SRFI 128.

* No vote
* None
* [SRFI 113](http://srfi.schemers.org/srfi-113/srfi-113.html) (passed as `(scheme set)`)

6. What character set library should R7RS-large provide?

> SRFI 14 provides linear-update sets and bags, which can be mutable or immutable according to the implementer's preference. The contents are limited to characters.

* No vote
* None
* [SRFI 14](http://srfi.schemers.org/srfi-14/srfi-14.html) (passed as `(scheme charset)`)

7. What hash table library should R7RS-large provide?

> SRFI 69 is a basic hash table library. SRFI 125 is an upward compatible extension of it. Likewise, R6RS provides a basic hash table library, of which SRFI 126 is an upward compatible extension. Since the interface of SRFI 125 depends on SRFI 128, voting for SRFI 125 will cause your vote on comparator libraries to be automatically changed to SRFI 128.

* No vote
* None
* [SRFI 69](http://srfi.schemers.org/srfi-69/srfi-69.html)
* [SRFI 125](http://srfi.schemers.org/srfi-125/srfi-125.html) (passed as `(scheme hash-table)`)
* [R6RS](http://www.r6rs.org/final/html/r6rs-lib/r6rs-lib-Z-H-14.html#node_chap_13)
* [SRFI 126](http://srfi.schemers.org/srfi-126/srfi-126.html)

## Immutability

8. What immutable pair/list library should R7RS-large provide?

> SRFI 116 is a variant of SRFI 1 for immutable pairs, which are a separate data type from ordinary Scheme pairs. Only a few functional update procedures are provided. Procedure names are prefixed with "i".

* No vote
* None
* [SRFI 116](http://srfi.schemers.org/srfi-116/srfi-116.html) (passed as `(scheme ilist)`)

9. What random access pair/list library should R7RS-large provide?

> SRFI 101 is a library for purely functional pairs and lists with O(log n) access time. To avoid conflicts, procedure names will be modified for R7RS-large purposes as follows:

* `make-list` to become `make-rlist`
* `random-access-list->linear-access-list` to become `rlist->list`
* `linear-access-list->random-access-list` to become `list->rlist`
* all other identifiers to be prefixed with `r` (`rcons`, `rpair?`, `rcar`, `rmap`, etc.)


* No vote
* None
* [SRFI 101](http://srfi.schemers.org/srfi-101/srfi-101.html) (passed as `(scheme rlist)`)

10. What immutable deque library should R7RS-large provide?

> SRFI 134 is a library for immutable double-ended queues, with O(1) addition and removal time at both ends.

* No vote
* None
* [SRFI 134](http://srfi.schemers.org/srfi-134/srfi-134.html) (passed as `(scheme ideque)`)

11. What immutable text library should R7RS-large provide?

> SRFI 135 is a library for immutable character sequences with O(1) access time. Its procedures accept strings as well as texts, so that a separate string library may not be as useful.

* No vote
* None
* [SRFI 135](http://srfi.schemers.org/srfi-135/srfi-135.html) (passed as `(scheme text)`)
* [SRFI 140](http://srfi.schemers.org/srfi-140/srfi-140.html)

Laziness
12. What generator library should R7RS-large provide?

> SRFI 121 is a collection of routines for manipulating generators, which are nullary procedures that are invoked repeatedly to generate a sequence of values. They provide lightweight laziness.

* No vote
* None
* [SRFI 121](http://srfi.schemers.org/srfi-121/srfi-121.html) (passed as `(scheme generator)`)

13. What lazy sequence library should R7RS-large provide?

> SRFI 127 is a variant of SRFI 1 for lazy sequences, which are either proper lists or lists with a generator in the tail. They provide medium-weight laziness.

* No vote
* None
* [SRFI 127](http://srfi.schemers.org/srfi-127/srfi-127.html) (passed as `(scheme lseq)`)

14. What stream library should R7RS-large provide?

> SRFI 41 provides classical streams based on delay/force. They provide heavyweight laziness.  It specifies two libraries, but will be treated as just one for R7RS purposes.

* No vote
* None
* [SRFI 41](http://srfi.schemers.org/srfi-41/srfi-41.html) (passed as `(scheme stream)`)

## Miscellaneous

15. What box library should R7RS-large provide? *

> SRFI 111 is a trivial library for boxes (single-slot records).

* No vote
* None
* [SRFI 111](http://srfi.schemers.org/srfi-111/srfi-111.html) (passed as `(scheme box)`)

16. What list queue library should R7RS-large provide?

> SRFI 117 provides mutable half-deques with O(1) addition at both ends and O(1) deletion from the front. They are based directly on ordinary Scheme lists, and are known in other Lisps as "tconc structures".

* No vote
* None
* [SRFI 117](http://srfi.schemers.org/srfi-117/srfi-117.html) (passed as `(scheme list-queue)`)

17. What ephemeron library should R7RS-large provide?

> SRFI 124 provides ephemerons compatible with MIT Scheme except that they are immutable. A dummy implementation for systems without GC support is also provided.

* No vote
* None
* [SRFI 124](http://srfi.schemers.org/srfi-124/srfi-124.html) (passed as `(scheme ephemeron)`)

18. What titlecase library should R7RS-large provide?

> SRFI 129 is a modified version of R6RS titlecase support with full Unicode compatibility. It is less useful with SRFI 135, which provides titlecase support already.

* No vote
* None (passed, so no library)
* [SRFI 129](http://srfi.schemers.org/srfi-129/srfi-129.html)

19. What comparator library should R7RS-large provide?

> [SRFI 128](http://srfi.schemers.org/srfi-128/srfi-128.html) is a comparator library whose interface is used by SRFI 113 and SRFI 125.  It was not formally balloted but was voted in based on the votes for those libraries, as `(scheme comparator)`.

