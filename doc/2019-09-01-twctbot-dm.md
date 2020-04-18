---
layout: default
title: Twitter懸賞自動応募BotのDMメール機能
---

# Twitter懸賞自動応募BotのDMメール機能

2019-09-01

Twitter上の懸賞に自動応募するBotにダイレクトメッセージをサマライズしてメール送信するツールを追加した。

## はじめに

以前、Twitterの懸賞に自動応募するBotを作った。リツイート＆フォローで応募できるものが対象。結果はDMやリプライを確認すればOK。というものを作った。

[Twitter懸賞自動応募Bot](https://ta9t2.github.io/notes/doc/2019-04-04-twctbot.html)

## ダイレクトメッセージをサマライズしてメール送信するツール

### 作った背景

当選結果はDMで届くことが多いが、いちいちTwitterにログインして確認するのが面倒だった。

### 作ったもの

Botの1つの機能として、DMを取得してメール送信するツールを追加した。メール送信時は前回取得したDMよりも新しい日時のDMを取得して送信する。

[Twitter Contest Entry Bot - GitHub](https://github.com/ta9t2/twitter-contest-entry-bot/blob/master/README.ja.md)

### 所感など

手間が減ってかなりらくになった！

