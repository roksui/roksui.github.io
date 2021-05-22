---
# layout: posts
title:  "Setting up OCaml Environment on Ubuntu(Linux)"
# date:   2021-04-05 10:10:56 +0900
# categories: jekyll update
---

## Opam 설치

Linux OS에 Ocaml을 설치하는 방법은 윈도우보다 간단한 것 같다.
먼저 VMWare를 통해 가상환경을 구축하고 Ubuntu 20.04를 설치하였다.
그 후 OCaml 공식 사이트와 https://ilyasergey.net/YSC2229/wsl.html를 참조하여 다음과 같이 터미널로 설치를 진행하였다.

1. Ocaml을 위해 필요한 몇 가지 Linux package들을 설치해준다.
{% highlight ruby %}

sudo apt install make m4 gcc pkg-config libx11-dev

{% endhighlight %}

2. opam package manager(Python의 pip과 비슷함)를 설치하면 ocaml도 함께 설치된다. 또 여러 Ocaml 라이브러리를 설치해준다.
{% highlight ruby %}

sudo add-apt-repository ppa:avsm/ppa # 현재 Ubuntu 20.04 버전에서는 이 레포지토리 설정을 skip해도 무방하였다.
sudo apt install opam
opam init -y --compiler=4.10.0 --disable-sandboxing
eval $(opam env)
opam pin add ppx_inline_test v0.14.0
opam install -y dune core batteries utop graphics merlin ocp-indent

{% endhighlight %}

3. ~/.bashrc file에 다음 줄을 추가해준다.
{% highlight ruby %}
eval $(opam env)
{% endhighlight %}

다 설치한 후 터미널을 새로 열어서 ocamlc --version을 해보면 4.10.0 버전의 컴파일러가 찍힐 것이다.

## Emacs 세팅

아래 커맨드를 통해 Emacs를 설치한다.
{% highlight ruby %}
sudo add-apt-repository ppa:kelleyk/emacs
sudo apt update
sudo apt install emacs
{% endhighlight %}

윈도우와 비슷한 쇼트컷들을 사용하기 위해 Cua mode를 설치해준다. Cua mode를 활성화하기 위해서 home folder에 .emacs file을 만들어준다. (~/.emcas)
다음 줄을 추가해준다.

{% highlight ruby %}
(cua-mode t)
(setq cua-auto-tabify-rectangles nil) ;; Don't tabify after rectangle commands
(transient-mark-mode 1) ;; No region when it is not highlighted
(setq cua-keep-region-after-copy t) ;; Standard Windows behaviour
{% endhighlight %}

{% highlight ruby %}
opam install -y tuareg user-setup
opam user-setup install --editors=emacs
{% endhighlight %}

추가로 Code Completion feature를 추가해주기 위해 Alt-X:package-list-packages를 실행한다. 목록에서 company -> Install -> Yes를 선택한다.

~/.emacs configuration에 다음 줄들을 추가해준다.

{% highlight ruby %}
; Make company aware of merlin
(with-eval-after-load 'company
(add-to-list 'company-backends 'merlin-company-backend))
; Enable company on merlin managed buffers
(add-hook 'merlin-mode-hook 'company-mode)
{% endhighlight %}

또, merlin과 ocp-indent를 위해 다음과 같이 ~/.emacs에 추가해준다.

{% highlight ruby %}
(let ((opam-share (ignore-errors (car (process-lines "opam" "config" "var"
   "share")))))
      (when (and opam-share (file-directory-p opam-share))
       ;; Register Merlin
       (add-to-list 'load-path (expand-file-name "emacs/site-lisp" opam-share))
       (autoload 'merlin-mode "merlin" nil t nil)
       ;; Automatically start it in OCaml buffers
       (add-hook 'tuareg-mode-hook 'merlin-mode t)
       (add-hook 'caml-mode-hook 'merlin-mode t)
       ;; Use opam switch to lookup ocamlmerlin binary
       (setq merlin-command 'opam)))


(add-to-list 'load-path "/home/roksui/.opam/4.10.0/share/emacs/site-lisp")
(require 'ocp-indent)
{% endhighlight %}