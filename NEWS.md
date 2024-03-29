# valaddin

## 1.0.2

This release eliminates a test failure introduced by an upcoming change to the
value `is.atomic(NULL)` (from `TRUE` to `FALSE`). There are no user-facing
changes.

## 1.0.1

This release merely eliminates spurious test failures introduced by an upcoming
change in `all.equal()`. There are no user-facing changes.

## 1.0.0

With this release, valaddin is frozen. The next iteration of valaddin is the
[rong](https://github.com/egnha/rong) package. Future releases of valaddin
itself will only include bug fixes.

* Maintenance of the package has been substantially improved by dropping purrr
  as a dependency (#57, #58). The lazyeval package, whose features are frozen at
  0.2.1, is now the only dependency.

### Breaking changes

* `vld_singleton()` now only checks whether an object has length 1, atomic or not.

* Bare-vector checkers (e.g., `vld_bare_logical()`) have been removed.

## 0.1.2

### Operator for input validation

* A new operator `%checkin%` provides an alternative way to write checks next to
  function headers ([#21](https://github.com/egnha/valaddin/issues/21),
  suggested by @MilkWasABadChoice). For example, writing
  `list(~is.numeric, ~{. > 0}) %checkin% function(a, b) a + b` is equivalent to
  writing `firmly(~is.numeric, ~{. > 0}, .f = function(a, b) a + b)`. Using the
  `%checkin%` operator is the recommended way to apply input validation in
  scripts and packages.
  
### New functions

* The localized check makers `vld_any()` and `vld_all()` correspond to the base
  predicates `any()` and `all()`.

* To match naive expectations, a localized check maker `vld_closure()` is
  introduced to validate _closures_, i.e., non-primitive functions, while 
  `vld_function()` has been redefined to validate functions in general, i.e., it
  corresponds to the base R predicate `is.function` 
  ([#18](https://github.com/egnha/valaddin/issues/18)).
  
### Minor improvements

* dplyr is no longer required.

* Since `loosely()` is typically used to obviate the overhead of input 
  validation, calling it should itself impose as little overhead as possible
  ([#28](https://github.com/egnha/valaddin/issues/28)). Therefore, `loosely()`
  has been streamlined: it no longer checks its inputs. Calling it is now on par
  with calling `firm_core()`.
  
* `firmly()` gets a new option, `.error_class`, that enables you to customize
  the subclass of the error object that is signaled when an input validation
  error occurs ([#25](https://github.com/egnha/valaddin/issues/25)).

* Reduced use of assignment, subsetting and warning suppression speeds up 
  `firmly` to within an order of magnitude of the speed of input validation
  using `stopifnot`.

* The environment of check formulae generated by the `vld_*()` check makers is
  the package namespace environment. (Previously, such check formulae got their
  own environment, though there was no need for such separation.)
  
* Printing of functions (i.e., those underlying firmly applied functions and 
  predicate functions of check-formula makers) is normalized to eliminate
  spurious indentation ([#23](https://github.com/egnha/valaddin/issues/23)).

* Minor edits to vignette.

### Bug fixes

* Validation error messages now display all arguments with specified or default
  value ([#33](https://github.com/egnha/valaddin/issues/33)). Previously, only
  specified arguments were shown, even when the source of a validation failure
  was an invalid default value.

* When evaluating input-validation expressions, the lexical scope of promises is
  now completely isolated from the lexical scope of (check formula) predicate
  functions ([#32](https://github.com/egnha/valaddin/issues/32)). With this fix,
  `firmly()` and `%checkin%` are now safe to use in package namespace 
  environments. Previously, it was possible for a predicate function to be
  hijacked by a homonymous promise, or for an input validation to fail for an
  argument with default value, if that default value was inaccessible from the
  parent frame.
  
* If a formal argument happened to coincide with the name of an object 
  in the input validation procedure (`valaddin:::validating_closure()`), that 
  formal argument could be inadvertently invoked in place of that object. This 
  bug has been fixed by referencing bindings in the enclosing environment. 
  (However, doing something truly ill-advised, such as duping a base R function,
  will still go unsupervised.) See commits 
  [abae548](https://github.com/egnha/valaddin/commit/abae5480392a6fbf81b6faafcfd097dd6a936829)
  and 
  [dcfdcaf](https://github.com/egnha/valaddin/commit/dcfdcaf24966007794949f66e5108030d17f520f).
  
* Minor fixes to documentation, but not code, to ensure compatibility with purrr
  0.2.3.

### Deprecated

* The `.quiet` argument has been dropped from `loosely()`. (If desired, the
  behavior of that option can be replicated by signaling an appropriate warning 
  prior to calling `loosely()`.)

## 0.1.0

* First stable release:
    + Main functional operators: `firmly`, `loosely`
    + Component extractors: `firm_checks`, `firm_core`, `firm_args`
    + Check-scope converters (checker factories): `localize`, `globalize`
    + Localized base-R- and purrr-predicate checkers: `vld_*`
    
* `vld_numeric`, `vld_scalar_numeric` are based on `base::is.numeric`, since the
  corresponding predicates in purrr will be deprecated starting from version 
  0.2.2.9000 ([#12](https://github.com/egnha/valaddin/issues/12)).

* Fulfills aim of purrr proposal 
  [#275](https://github.com/tidyverse/purrr/issues/275) (closed).
