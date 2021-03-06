SyntaxErl
=========

SyntaxErl is a syntax checker tool for Erlang. The syntax checker currently supports erlang source files (.erl), erlang header files (.hrl), and erlang terms (.config, ...). Its main purpose is to be used by tools like Emacs's flymake http://www.emacswiki.org/emacs/FlymakeErlang and Vim's syntastic https://github.com/scrooloose/syntastic. SyntaxErl uses the Rebar https://github.com/basho/rebar config file to determine correct deps and libs paths. Some generic compile options are hardcoded, others, project specific, are read from the rebar config file. To make it possible for the syntax checker to work its magic, make sure that your project is compiled first (using the Rebar or something) and all the deps are at their places. Sinan https://github.com/erlware/sinan and Emakefile http://www.erlang.org/doc/man/make.html support is also possible to incorporate. Pull requests are welcome.

Building
--------

Information on building and installing [Erlang/OTP](http://www.erlang.org)
can be found [here](https://github.com/erlang/otp/wiki/Installation)
([more info](https://github.com/erlang/otp/blob/master/INSTALL.md)).

### Dependencies

To build SyntaxErl you will need a working installation of Erlang, git, and GNU make.

#### Building SyntaxErl

```sh
$ git clone git://github.com/ten0s/syntaxerl.git
$ cd syntaxerl
$ make
```

After performing the steps above in the current working directory you now
have a script called `syntaxerl'. Place this script anywhere in your path.

Usage
-----

### Command line

```sh
$ syntaxerl
Usage: syntaxerl [-d] filename
Usage: syntaxerl [-h]
Syntax checker for Erlang (0.0.5)

  -d, --debug    Enable debug output
  -h, --help     Show this message
```

### Emacs

#### Flymake

```elisp
;;;----------------------------------------
;;; erlang-mode
;;;----------------------------------------

(setq erlang-root-dir "/opt/otp-r15b01/lib/erlang")
(setq load-path (cons (car (file-expand-wildcards (concat erlang-root-dir "/lib/tools-*/emacs"))) load-path))
(setq erlang-electric-commands nil)
(require 'erlang-start)

(add-hook 'erlang-mode-hook
  '(lambda()
	 (imenu-add-to-menubar "Imenu")))

; define auto erlang mode for these files/extensions.
(add-to-list 'auto-mode-alist '(".*\\.app\\'" . erlang-mode))
(add-to-list 'auto-mode-alist '(".*app\\.src\\'" . erlang-mode))
(add-to-list 'auto-mode-alist '(".*\\.config\\'" . erlang-mode))
(add-to-list 'auto-mode-alist '(".*\\.rel\\'" . erlang-mode))
(add-to-list 'auto-mode-alist '(".*\\.script\\'" . erlang-mode))

; add include directory to default compile path.
(defvar erlang-compile-extra-opts
  '(bin_opt_info debug_info (i . "../include") (i . "../deps") (i . "../../") (i . "../../../deps")))

; define where put beam files.
(setq erlang-compile-outdir "../ebin")

;;;----------------------------------------
;;; flymake
;;;----------------------------------------

(require 'flymake)
(setq flymake-log-level 3)

(defun flymake-compile-script-path (path)
  (let* ((temp-file (flymake-init-create-temp-buffer-copy
					 'flymake-create-temp-inplace))
		 (local-file (file-relative-name
					  temp-file
					  (file-name-directory buffer-file-name))))
	(list path (list local-file))))

(defun flymake-syntaxerl ()
  (flymake-compile-script-path "~/bin/syntaxerl"))

(add-hook 'erlang-mode-hook
  '(lambda()
	 (add-to-list 'flymake-allowed-file-name-masks '("\\.erl\\'" flymake-syntaxerl))
	 (add-to-list 'flymake-allowed-file-name-masks '("\\.hrl\\'" flymake-syntaxerl))
	 (add-to-list 'flymake-allowed-file-name-masks '("\\.app\\'" flymake-syntaxerl))
	 (add-to-list 'flymake-allowed-file-name-masks '("\\.app.src\\'" flymake-syntaxerl))
	 (add-to-list 'flymake-allowed-file-name-masks '("\\.config\\'" flymake-syntaxerl))
	 (add-to-list 'flymake-allowed-file-name-masks '("\\.rel\\'" flymake-syntaxerl))
	 (add-to-list 'flymake-allowed-file-name-masks '("\\.script\\'" flymake-syntaxerl))

	 ;; should be the last.
	 (flymake-mode 1)
))
```

#### Erlang-flymake

Your help is welcome.

### Vim

Your help is welcome.

