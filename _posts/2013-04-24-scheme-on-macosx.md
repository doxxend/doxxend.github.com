---
layout: post
title: "Scheme编程环境设置(Mac OS X + Racket + Emacs)"
description: ""
category: programming
tags: [scheme]
---
{% include JB/setup %}

## 0.前言
---
强烈建议先阅读[王垠的教程](http://www.yinwang.org/blog-cn/2013/04/11/scheme-setup/)。我的教程是对他的补充。
<br>

## 1.安装Scheme
---
我用的Scheme解释器是`Racket`, [点这里下载](http://racket-lang.org/download/)。进入页面后选择`Mac OS X (Intel 64-bit)`，下载一个`racket-5.3.3-bin-x86_64-osx-mac.dmg`文件(**注意：你应该依照你的情况进行下载**)。下载成功后，把`Racket v5.3.3`拖到`Applications`。打开`Terminal`终端，执行：

    ln -s /Applications/Racket\ v5.3.3/bin/racket /usr/bin/racket
	
现在你在`Terminal`输入：`racket`即可启动它。当然你也可以通过`设置$PATH`来达到在`终端`启动`Racket`的目的。

## 2.配置Emacs
---
参照[王垠的教程](](http://www.yinwang.org/blog-cn/2013/04/11/scheme-setup/)。在你的`.emacs`文件里加入如下代码：

	;;;;;;;;;;;;
	;; Scheme ;;
	;;;;;;;;;;;;

	(require 'cmuscheme)
	(setq scheme-program-name "racket")         ;; 如果用 Petite 就改成 "petite"


	;; bypass the interactive question and start the default interpreter
	(defun scheme-proc ()
	"Return the current Scheme process, starting one if necessary."
	(unless (and scheme-buffer
    (get-buffer scheme-buffer)
    (comint-check-proc scheme-buffer))
    (save-window-excursion
    (run-scheme scheme-program-name)))
	(or (scheme-get-process)
    (error "No current process. See variable `scheme-buffer'")))


	(defun scheme-split-window ()
	(cond
	((= 1 (count-windows))
    (delete-other-windows)
    (split-window-vertically (floor (* 0.68 (window-height))))
    (other-window 1)
    (switch-to-buffer "*scheme*")
    (other-window 1))
	((not (find "*scheme*"
    (mapcar (lambda (w) (buffer-name (window-buffer w)))
    (window-list))
    :test 'equal))
    (other-window 1)
    (switch-to-buffer "*scheme*")
    (other-window -1))))


	(defun scheme-send-last-sexp-split-window ()
	(interactive)
	(scheme-split-window)
	(scheme-send-last-sexp))


	(defun scheme-send-definition-split-window ()
	(interactive)
	(scheme-split-window)
	(scheme-send-definition))

	(add-hook 'scheme-mode-hook
 	(lambda ()
    (paredit-mode 1)
    (define-key scheme-mode-map (kbd "<f5>") 'scheme-send-last-sexp-split-window)
    (define-key scheme-mode-map (kbd "<f6>") 'scheme-send-definition-split-window)))

## 3.完成
---
在你的Emacs里按`M X run-scheme`，即可调出Racket。

## 4.结语
---
在[王垠的教程](http://www.yinwang.org/blog-cn/2013/04/11/scheme-setup/)里还涉及到`Paredit mode`以及`设置括号颜色`，你可以参考他的文章来配置，非常简单。另外，他其他的其他文章也非常值得一读。
