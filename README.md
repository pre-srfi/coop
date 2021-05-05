# SRFI nnn: coop

by Amirouche Boubekki

## Status

Early Draft

## Abstract

Helpers to handle concurrency.

## Issues

??? Optional section that may point out things to be resolved. This
will not appear in the final SRFI.

## Rationale

??? detailed rationale. This should be 200-500 words long. Please
explain why the proposal should be incorporated as a standard feature
in Scheme implementations. List related standards and SRFIs, including
dependencies, conflicts, and replacements. If there are other
standards which this proposal will replace or with which it will
compete, please explain why the present proposal is a substantial
improvement.

### Survey of prior art

GitHub's version of Markdown can make tables. For example:

| System        | Procedure | Signature                 |
| ------------- |:---------:| ------------------------- |
| System A      | `jumble`  | _list_ _elem_             |
| System B      | `bungle`  | _elem_ _list_             |
| System C      | `frob`    | _list_ _elem_ _predicate_ |

## Specification

### `(coop-time) → positive? positive?`

Rationale: it call help to benchmark at runtime the execution of
flows, and scale accordingly the number of flows.

Returns two numbers, the first represents seconds, the second
nanoseconds. The whole represent an absolute time.

Note: it might just be a shortcut for jiffies.

### `(coop-spawn thunk) procedure?`

Starts a new flow of execution with `THUNK`. If `THUNK` raise an
object the behavior is unspecified.  When `THUNK` returns, returned
values, if any, are unreachable.

### `(coop-priority [number]) positive? → positive?`

Returns or set the priority of the current flow of execution. A
priority of `+inf.0` may be used to instruct the host that the current
thunk should have exclusive access to one computation unit.

Note: The behavior of parameters related to flows are not specified.

### `(make-coop-channel)`

Returns a channel.

### `(coop-channel? obj)`

Returns true, if `OBJ` is channel, otherwise false. A channel allow
communication between flows.

### `(coop-operation? obj)`

Returns true, if `OBJ` is an operation, otherwise false.

### `(coop-wrap operation proc)`

Wrap an operation with `PROC`. If the operation `OPERATION` when
applied by `coop-apply` would return values `objs`, then when
`coop-wrap` is applied, the returned value is `(apply PROC objs)`.

### `(coop-produce channel objs ...)`

Returns an operation, that will produce `OBJS` in `CHANNEL` when
applied by `coop-apply`, and will return no values.

A flow that is producing values on a channel, may wait indefinitly if
there is nobody consuming on the same channel.

`coop-produce` will synchronize once and only once with one and only
one `coop-consume`.

### `(coop-consume channel)`

Returns an operation, that will consume and return objects produced on
`CHANNEL` when applied by `coop-apply`.

A flow may wait indefinitly if there is nobody producing on the same
channel.

`coop-consume` will synchronize once and only once with one and only
one `coop-produce`.


### `(coop-sleep seconds [nanoseconds])`

Returns an operation. It may return after `SECONDS` and `NANOSECONDS`
when applied with `coop-apply`. In other words the following code
will return after one second, the value `'done-sleeping`:

```scheme
(coop-apply (coop-wrap (coop-sleep 1) (lambda () 'done-sleeping)))
```

It can be used with `coop-choice` to implement timeouts, and avoid the
situations where flows wait indefinitly.

### `(coop-choice operations)`

Returns an operation made of all `OPERATIONS`. When applied, it will
return the return values, if any, of one and only one operation.

### `(coop-apply coop)`

Apply an operation. The flow that called `coop-perform` does not
necessarly return immediatly.

## Examples

## Implementation

??? explanation of how it meets the sample implementation requirement
(see process), and the code, if possible, or a link to it Source for
the sample implementation.

## Acknowledgements

??? Give credits where credits is due.

## References

Many.

## Copyright

Copyright (C) Firstname Lastname (20XY).

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
