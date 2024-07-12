ob-sml
====
EDIT: Temporary fix for this [issue](https://lists.gnu.org/archive/html/emacs-orgmode/2018-11/msg00162.html) I ran into. Also added
eval-as-file header arg, which loads and compiles the source block as a temp file via `use "tmp";` instead of evaluating line-by-line, so e.g.
```
#+BEGIN_SRC sml
val x = 3;
val y = x+1;
val x = 1;
val w = x;
#+END_SRC
```
yields
```
#+RESULTS:
: val x = 3 : int
: val y = 4 : int
: val x = 1 : int
: val w = 1 : int
```
but
```
#+BEGIN_SRC sml :eval-as-file t
val x = 3
val y = x+1
val x = 1
val w = x
#+END_SRC
```
returns
```
#+RESULTS:
: [opening /tmp/babel-A9D27F/sml-xn93nD]
: val x = <hidden-value> : int
: val y = 4 : int
: val x = 1 : int
: val w = 1 : int
: val it = () : unit
```

Put `ob-sml.el` where it can be loaded by Emacs. For example I place
it in a directory `~/.emacs.d/vendor` which is automatically put on
the load path with the following:

```
(let ((default-directory  "~/.emacs.d/vendor/"))
  (normal-top-level-add-subdirs-to-load-path))
```

Then require it and make sure `org-babel` knows about it like so:

```elisp
(require 'ob-sml nil 'noerror)

(org-babel-do-load-languages
 'org-babel-load-languages
 '((clojure . t)
   (ruby . t)
   (scheme . t)
   (js . t)
   (haskell . t)
   (prolog . t)
   (sml . t) ;; added!
   ))
```

To test, create a new `org-mode` file such as `sml.org`. Then add the
following to this file:

```
ML for the Working Programmer

* Chapter 1
#+BEGIN_SRC sml :session *sml*
  fn n => n + 1
#+END_SRC
```

Start a `sml` REPL with `M-x run-sml`. You should now be able to place
your cursor in the code block and evaluate with `C-c C-c` and the
contents of your `sml.org` file should now look like:

```
* Chapter 1
#+BEGIN_SRC sml :session *sml*
  fn n => n + 1
#+END_SRC

#+RESULTS:
: val it = fn : int -> int
```

