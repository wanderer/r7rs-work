Compound objects are a generalization of SRFI 35 and R6RS compound conditions,
and are suitable for use in creating and handling conditions among other purposes.
They encapsulate an immutable sequence of subobjects, which can be
any object except another compound object.  

## Rationale

Compound objects belong to a disjoint type.  Consequently, they can
be used to represent multiple otherwise unrelated aspects of a value
or situation.  Because they are sequences, they can be used to
represent priorities of interpretation from higher to lower.  Most
of the operations described
in this section treat a simple condition identically to a compound
condition with itself as its own sole component. 

## Specification

By convention, a subobject that is a list whose car is a symbol and whose
cdr is an alist whose keys are symbols is known as a type subobject.
It specifies the compound object's overall type and any associated key-value properties.
A compound object may contain more than one such subobject,
but only the first one is treated specially.
If there is no such subobject, the compound object has no particular type.

## Procedures

`(make-compound ` *list*`)`

Create a compound object containing the objects in *list* in the specified order.
If any object is itself a compound object, it is flattened into its subobjects,
which are then added to the result in sequence.

`(compound ` *obj* ...`)`

The same as `make-compound`,
except that it accepts multiple arguments instead of a list.

`(compound? `*obj*`)`

Returns `#t` if *obj* is a compound object, and `#f` otherwise.

`(compound-type `*obj*`)`

If *obj* is a compound object containing a
type subobject, returns the car of that subobject.  Otherwise, returns `#f`,
indicating a typeless compound object.

`(compound-subobjects `*obj*`)`

If *obj* is a compound object, returns a list of its subobjects
Otherwise, returns a list containing only *obj*.

`(compound-values `*obj*`)`

If *obj* is a compound object, returns its subobjects as multiple values.
Otherwise, returns *obj*.

`(compound-length `*obj*`)`

If *obj* is a compound object, returns the number of its subobjects as an exact
integer.  Otherwise, it returns 1.

`(compound-ref `*obj k*`)`

If *obj* is a compound object, returns the *k*th subobject.  Otherwise,
*obj* is returned.  In either case, it is an error if *k* is less than
zero or greater than or equal to the length of *obj*.

`(compound-map `*mapper obj*`)`

If *obj* is a compound object, returns a compound object
whose subobjects result from invoking *mapper* on each subobject of *obj*.
Although the subobjects of the result are in the same order as the subobjects of *obj*,
the order in which *mapper* is applied to them is unspecified.

If any subobject is itself a compound object, it is flattened into its subobjects,
which are then added to the result in sequence.

If *obj* is not a compound object, returns a compound object
whose only subobject is the result of applying *mapper* to *obj*.

`(compound-map->list `*mapper obj*`)`

If *obj* is a compound object, returns a list
whose elements result from invoking *mapper* on each subobject of *obj*.
Although the elemnts of the result are in the same order as the subobjects of *obj*,
the order in which *mapper* is applied to them is unspecified.

If *obj* is not a compound object, returns a list
whose only element is the result of applying *mapper* to *obj*.

`(compound-filter `*pred obj*`)`

If *obj* is a compound object, returns a compound object
that contains the subobjects of *obj* that satisfy *pred*.

If *obj* is not a compound object, it returns a compound object
whose only subobject is *obj* if *obj* satisfies *pred*,
or an empty compound object if *obj* does not satisfy *pred*.

`(compound-predicate `*pred obj*`)`

If *obj* is a compound
object such that at least one of its subobjects satisfies *pred*,
returns what *pred* returns when applied to the first such subobject;
otherwise returns `#f`.

If *obj* is not a compound object, applies *pred* to *obj* and
returns what *pred* returns.

`(compound-access `*pred accessor obj default*`)`

If *obj* is a compound object, *accessor* is applied to
the first subobject that satisfies *pred* and the result is returned;
if there is no such subobject, *default* is returned.

If *obj* is not a compound object, then if the object satisfies *pred*,
it applies *accessor* to *obj* and returns what it returns,
but if *obj* does not satisfy *pred*, *default* is returned.

`(compound-type-properties `*sym obj*`)`

If *obj* is a compound object, then if it contains a subobject
satisfying `compound-type?` whose car is *sym*, then it
returns the cdr of the first such type object; otherwise it returns `#f`.

If *obj* is not a compound object, then if it satisfies `compound-type?`
and its car is *sym*, then it returns the cdr of *obj*; otherwise it returns `#f`.
