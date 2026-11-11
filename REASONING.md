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
`+` is a function, and it evaluates every argument in order. The inner `*` is evaluated first, before adding the resulting value 6 with 1, returning 7. That is an integer, so the length is `none` and the head is a function.

## P02
There’s a leading `'` which the reader turns into a quote form, so the form is equivalent to `(quote(+ 1 2))`. Its head is `quote`, a special-operator, so `(+ 1 2)` is returned unevaluated instead of being added. Hence, the value is the list `(+,1,2)`, whose elements are the symbol `+`, number 1, and number 2, so it is a cons with length 3.

## P03
`nil` evaluates to itself. It is kind `null`, and as an empty sequence, it has a length of 0. It is an atom, so the operator is `none`.

## P04
`t` evaluates to itself and is a symbol. It is not a sequence, rather it is an atom, so the length and operator are both `none`.

## P05
`symbolp` is a function that checks whether an object is of type symbol. Its argument `nil` is evaluated first, and `nil` is a symbol, so `symbolp` returns `t`. It is a symbol other than `nil`, so the kind is `symbol`, and since it is not a sequence, the length is `none`.

## P06
It heads with the function `car`, which returns the first element of a list, but the quote hands `car` an empty list, so it returns `nil`. `nil`’s kind is `null` and, since `nil` is an empty sequence, it has a length of 0.

## P07
`cons` is a function that takes 2 arguments and returns a new cons cell that contains the 2 values in parenthesis. 1 evaluates to itself, and the `'` is shorthand for quote, which hands over the list `(2 3)` as data instead of evaluating it as a call. `cons` then puts 1 in front of that list, giving `(1 2 3)`, so the kind is `cons` with length 3.

## P08
The head `if` is a special operator, so it controls which subforms get evaluated, and here, it evaluates the condition `(null '())` first, where  `null` is a function and  `'()` is the empty list. Since the condition is true, `if` evaluates only the `'empty` branch and skips `'full`, where the quote makes `’empty` return the symbol `empty` instead of looking up a variable named `empty`. A symbol that isn’t `nil` has the kind `symbol` and its length is `none` since it isn’t a sequence.

## P09
The head `and` short-circuits, which means it stops at the first false subform it meets from left to right, so it returns `nil` and doesn’t reach 3. Since `nil`, it has a kind `null` and length 0. It is a macro, but by section 3.1.2.1.2.2, CLISP also implements it as a special operator, which the course checks first.

## P10
The head is `progn`, a special operator that evaluates its subforms in order and returns the value of the last one. `defvar` defines `*ps3-rate*` globally as 2, and the `let` creates a new binding of 10 that only exists in the text of the `let` form. `ps3-scale` is defined outside the `let`, so it still sees the global value 2, and the result is 3 * 2 = 6, which is an integer with length `none`. 

I predicted the kind, length, and operator right, but my value was 6 and CLISP printed 30. I read the `let` binding as lexical, when `defvar` makes `*ps3-rate*` special, so the `let` rebinding is dynamic and `ps3-scale` sees 10 when it is called.

## P11
`defun` is a macro, and its value is the function’s name as a symbol, which is ps3-twice. The kind then follows as `symbol` and length as `none`.

## P12
The head `labels` is a standard special operator that defines `down` as a local function that can call itself, then evaluates `(down 3)`. It recurses, each call conses `n` onto the result of `(down(- n 1))`, and when n reaches 0 (the base case), it returns nil. The returns unwind to `(cons 3(cons 2(cons 1 nil)))`, which is `(3 2 1)`, a cons of length 3.

## P13
`mapcar` is a function, so its arguments are evaluated first: `#'length’` yields the function object for `length`, and the leading quote gives the list of three lists. `mapcar` calls `length` on each element, giving 2, 0 (since `()` is an empty list), and 1 respectively, where the result is a cons `(2 0 1)` with length 3.

## P14
`mapcar` is a function, so its arguments are evaluated first: the `lambda` form yields an anonymous function object, and the quote gives the list `(1 2 3)`. `mapcar` applies the function to each element, squaring them to 1, 4, 9, giving the result as a cons `(1 4 9)` with length 3.

## P15
The head `lambda` is not a special operator (that is `function`), but it is a macro that can be expanded to `(function (lambda …))`, so the operator is `macro`. Evaluating that expanded form produces a function object without calling it, written as `#<function>`. A function is kind `function` and is not a sequence, so its length is `none`.

## P16
The outer `let` (the head and a special operator) binds a lexical variable `factor` to 2, and the `lambda` in the second `let` captures the lexical binding of `factor` that is in scope where it is written, which holds 2. The innermost `let` creates a separate `factor` bound to 10 that `f` never sees, because `factor` is lexical and not declared special. `(funcall f 3)` therefore computes 3*2 = 6, an integer with length `none`.
