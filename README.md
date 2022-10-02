## zshでCtrl-dを押すとシェルが終了してしまう。
&nbsp;
tmuxなどを使って作業しているときもセッション操作で、
**Ctrl-b d**（セッションを一時的に中断してメインに戻る (Detach)）をするときに
間違って**Ctrl-d**（ペインを終了）をしてしまって不便、
なので**Ctrl-d**を押しても大丈夫なように**Ctrl-dを押す回数を指定してログアウト**させようと思った。
しかし、zshだと少々ややこしいことをしないといけない。

bashだと、回数指定が出来る（例　Ctrl-dを3回押したらログアウト）
```
set -o ignoreeof
IGNOREEOF=2 <- IGNOREEOFが使える　○
```
zshだと、set -o ignoreeofは出来るが回数指定が出来ない
```
set -o ignoreeof // zshの場合Ctrl-dを10回押したら終了で固定されている
IGNOREEOF=2 <- IGNOREEOFが使えない　×
```
&nbsp;
そこで、これを使えばzshでも出来る（.zshrcに書く）
```
#　指定回数でログアウト↓ (例）Ctrl-d3回でログアウト
export IGNOREEOF=2
setopt ignore_eof
function bash-ctrl-d() {
  if [[ $CURSOR == 0 && -z $BUFFER ]]
  then
    [[ -z $IGNOREEOF || $IGNOREEOF == 0 ]] && exit
    if [[ "$LASTWIDGET" == "bash-ctrl-d" ]]
    then
      (( --__BASH_IGNORE_EOF <= 0 )) && exit
    else
      (( __BASH_IGNORE_EOF = IGNOREEOF ))
    fi
  fi
}
zle -N bash-ctrl-d
bindkey "^d" bash-ctrl-d
```
# まとめ
zsh、tmuxで作業中に間違ってCtrl-dを一度押しても終了しない。
指定回数押したら終了できるからtmuxでペインを閉じるのに便利。
