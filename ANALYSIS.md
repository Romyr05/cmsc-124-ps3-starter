# Environments and the Toy Evaluator

## E3: let

An environment is an association list, and `lookup` takes the first match.
`extend-env` puts the new pairs in front with `append`. It copies
only the new pairs and shares the old `env` as its tail, so the caller's list
is never changed.

`eval-let` evaluates every initializer in the outer `env` first (line 73), and
only then extends (line 74). That makes the bindings parallel. In
`(let ((x 1)) (let ((x 2) (y x)) (+ x y)))`, `y` is evaluated while `x` is
still 1, so the result is 3, not 4. The extended environment is only passed
down to the body, so it disappears when the body returns.

## E4: closures

Trace the scope case:

```lisp
(let ((x 2))
  (let ((f (lambda (y) (+ x y))))
    (let ((x 100))
      (f 1))))
```

1. The outer `let` gives `((x . 2))`.
2. `eval-expr` meets the `lambda` and line 127 runs `make-closure` with the
   current `env`. This is the line that captures the defining environment.
   At that moment it holds only `((x . 2))`.
3. The next `let` binds `f` to that closure, and the innermost one adds
   `(x . 100)` in front.
4. `(f 1)` reaches the application branch. `apply-closure` reads the saved
   environment (line 96) and extends it with `(y . 1)` (line 100). The
   caller's environment is never used.
5. The body `(+ x y)` looks up `x` in `((y . 1) (x . 2))` and returns 3.

If line 100 extended the caller's environment instead, `x` would be found as
100 and the result would be 101. That would make the language dynamically
scoped: a free variable would mean whatever binding is nearest at call time,
not where the function was written.
