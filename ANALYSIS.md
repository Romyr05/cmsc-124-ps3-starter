# Environments and the Toy Evaluator

## 1. Tracing a form and capturing the defining environment

*Answers: trace one form through your own `eval-expr`, and point to the line
that captures the defining environment.*

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

## 2. Joining arguments to the caller's environment


## 3. Why CLISP needs `funcall`
