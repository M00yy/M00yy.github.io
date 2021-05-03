---
title: 我傻了的MISC——steganography
date: 2019-10-20 20:27:49
tags: [Record,Misc,writeup]
categories: 
            - record
            - misc
---
记录一个巅峰极客的misc题，由于我的智障，导致战队成绩不佳。。。(大哭……

## 前言

题目的名字是steganography，隐写。

## 解题步骤

首先拿到的是一张图，拿到binwalk跑了一下，发现有个压缩包，foremost分出来，是docx文件的结构，直接修改后缀打开，发现是一段base64，暂时先不用。
在压缩包中还看到一个flag.xml,winhex打开是20 09…………
在战队师傅的提醒下是snow隐写，把空格替换成0，把tab替换成1，8位一个ascii码解码可以得到'flag{2806105f-ec43-'一部分的flag
接下来跳坑了。。开始没想到是base64隐写，直接解开了，还找到了原文对比，也没发现什么东西。
又分析了开始给的图，发现了另一个压缩的pyc文件，手动分开发现需要密码，注释中提示'**********@',发现爆破时任何密码都是对的，但是解不开。
这时想到的是base64中可能隐藏了密码，终于回到了base64隐写的正轨上来了。
在GitHub上找到了脚本
```python
import base64

def deStego(stegoFile):
    b64table = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
    with open(stegoFile,'r') as stegoText:
        message = ""
        for line in stegoText:
            try:
                text = line[line.index("=") - 1:-1]
                message += "".join([ bin( 0 if i == '=' else b64table.find(i))[2:].zfill(6) for i in text])[6-2*text.count('='):6] 
            except:
                pass
    return "".join([chr(int(message[i:i+8],2)) for i in range(0,len(message),8)])

print(deStego("base.txt"))

# I4mtHek3y@
```
这就是我背的锅，解文件的时候文件内容谜之错了，我竟然不知道也没发现，还把大家带歪了。。orz
比赛结束后终于发现了这个问题，跑出密码解压拿到pyc
以为回到了专项，结果反编译后
```python
from numpy import *
from random import random
import turtle as t
t.reset()
x = array([
    [
        0.5],
    [
        0.5]])
p = [
    0.85,
    0.92,
    0.99,
    1]
A1 = array([
    [
        0.85,
        0.04],
    [
        -0.04,
        0.85]])
b1 = array([
    [
        0],
    [
        1.6]])
A2 = array([
    [
        0.2,
        -0.26],
    [
        0.23,
        0.22]])
b2 = array([
    [
        0],
    [
        1.6]])
A3 = array([
    [
        -0.15,
        0.28],
    [
        0.26,
        0.24]])
b3 = array([
    [
        0],
    [
        0.44]])
A4 = array([
    [
        0,
        0],
    [
        0,
        0.16]])
cnt = 1
while None:
    cnt += 1
    if cnt == 2000:
        break
    r = random()
    if r < p[0]:
        x = dot(A1, x) + b1
    elif r < p[1]:
        x = dot(A2, x) + b2
    elif r < p[2]:
        x = dot(A3, x) + b3
    else:
        x = dot(A4, x)
    t.color('green')
    t.up()
    t.goto(x[0][0] * 50, x[1][0] * 40 - 240)
    t.down()
    t.dot()
hint = 'I am not the reverse'
```
给的hint是不是逆向，百度了一下有pyc隐写这个东西。
又在GitHub上找到了tool

两部分拼接起来可以得到flag{2806105f-ec43-57f3-8cb4-1add2793f508}

## summary

1.snow隐写 √
2.pyc隐写  √
3.base64隐写 √
4.我×了555……