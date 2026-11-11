# Reasoning for the Predictions Table

Write one entry under every heading below, then commit this file together with
`predictions.tsv`, before you run a case or open `tests/expected.tsv`.

Each entry names the rule that produces the four predicted fields for that
case. One to three sentences is enough. The grader checks structure only. It
does not judge quality, and it sets no word minimum.

After you run the cases, add a short correction under each case you predicted
wrongly. Say what you predicted, what CLISP printed, and which rule you
misread. Those entries belong in the correction commits.

## P01
`+` is a function, so its arguments get evaluated first. `(* 2 3)` gives 6, then `(+ 1 6)` gives 7, an integer with no length.

## P02
The leading `'` is read as `(quote (+ 1 2))`, and `quote` is a special operator that returns its argument without evaluating it. So the result is the list `(+ 1 2)`, a cons of length 3.

## P03
`nil` evaluates to itself. It is the empty list, so its kind is `null` with length 0, and since it is an atom there is no operator.

## P04
`t` evaluates to itself and is a symbol. It is an atom and not a sequence, so both length and operator are `none`.

## P05
`symbolp` is a function that checks if its argument is a symbol. `nil` is a symbol, so it returns `t`, which is kind `symbol` with no length.

## P06
`car` is a function that returns the first element of a list. The empty list has no first element, so `car` returns `nil`, kind `null` with length 0.

## P07
`cons` is a function that puts its first argument in front of the second. The quoted `'(2 3)` stays as data, so the result is `(1 2 3)`, a cons of length 3.

## P08
`if` is a special operator, so it only evaluates the branch it picks. `(null '())` is true, so it returns the quoted symbol `empty` and never touches `'full`.

## P09
`and` stops at the first false value from left to right, so it returns `nil` and never reaches 3. It is a macro in the standard, but CLISP also implements it as a special operator.

## P10
`progn` is a special operator that runs each subform in order and returns the last one. `defvar` makes `*ps3-rate*` special, so the `let` rebinds it dynamically and `ps3-scale` sees 10 while it runs. That gives 3 * 10 = 30, an integer.

## P11
`defun` is a macro, and it returns the name of the function it defines. So the value is the symbol `ps3-twice` with no length.

## P12
`labels` is a special operator that defines local functions that can call themselves. `down` conses `n` onto `(down (- n 1))` until `n` hits 0 and returns `nil`, which builds `(3 2 1)`, a cons of length 3.

## P13
`mapcar` is a function that applies `length` to each list. The lengths are 2, 0, and 1, so the result is `(2 0 1)`, a cons of length 3.

## P14
`mapcar` is a function that applies the lambda to each element, squaring them. That gives `(1 4 9)`, a cons of length 3.

## P15
`lambda` is a macro that expands to `(function (lambda ...))`. Evaluating it returns a function object without calling it, so the kind is `function` with no length.

## P16
`let` is a special operator, and `factor` here is lexical since it was never declared special. The lambda captures the `factor` bound to 2 where it was written, so the inner `factor` of 10 is ignored and `(funcall f 3)` gives 6.
