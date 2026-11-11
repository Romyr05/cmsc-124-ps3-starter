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
*Answers: what the scope case return if `apply-closure` extended the caller's environment, and what that makes the language.*

If line 100 extended the caller's environment instead of `saved-env`, `x` would resolve to the nearest binding at call time, `(x, 100)`, and the case would return 101 instead of 3. That makes the language dynamically scoped: a free variable means whatever binding is nearest when the function is called, not where it was written. Line 127 would then capture an evironment that no longer determines how free variables are resolved, defeating the purpose of storing it in the closure.

## 3. Why CLISP needs `funcall`
*Answers: why the host needs `funcall` where the toy language doesn't.*

CLISP is a Lisp-2, so variables and functions live in separate namespaces. In `(f 1)`, `f` is looked up as a function, not as the closure held in the variable `f`, so the host needs `funcall` to call it and `#` to pass a function as a value. The toy language has one alist for everything. The application branch evaluates the head with `(eval-expr (car expr) env)`, which goes through `lookup`, so a variable holding a closure is found the same way as any other value and `apply-closure` runs it. `(f 1)` works with no `funcall`.