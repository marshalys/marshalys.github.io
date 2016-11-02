---
layout: post
title: "DFA实现敏感词过滤—javascript实现"
date: 2013-05-20 17:35
comments: true
categories: 技术点滴
tags: [javascript, web开发]
---

前几天让一个同事去改进一下目前的敏感词过滤机制，因为原来的机制很简单粗暴，就是弄个循环一个一个判断词库里的词是否在提交内容中，词库大了之后效率不太高。

同事在网上找了找，发现了一个DFA的实现，http://www.nodejser.com/?p=146401 于是把代码整过来试了试，他打算比较一下这个实现跟之前的性能上能有多少改进。
<!-- more -->
昨天他很郁闷的过来跟我说，这个算法不行，他运行了测试代码后发现性能比之前的更差。于是我过去看了看他的测试代码，他测试用的数据：词库大约有6000个词，内容有5000字左右，应该没有问题，这个DFA的实现性能确实比之前的那种简单的循环判断性能还要差，于是我仔细看了看这个DFA的实现，发现这个实现没有用hash结构去保存树的节点，而是用了列表，这样查找起来效率还是不高的，于是自己简单实现了一个，测试了一下，改进还是很明显的，使用之前的测试数据，大约比之前的简单循环方式快了5倍左右：）

 附上代码：

``` js
    function buildMap(wordList) {
        var result = {};
        var count = wordList.length;
        for (var i = 0; i < count; ++i) {
            var map = result;
            var word = wordList[i];
            for (var j = 0; j < word.length; ++j) {
                var ch = word.charAt(j);
                if (typeof(map[ch]) != "undefined") {
                    map = map[ch];
                    if (map["empty"]) {
                        break;
                    }
                }
                else {
                    if (map["empty"]) {
                        delete map["empty"];
                    }
                    map[ch] = {"empty":true};
                    map = map[ch];
                }
            }
        }
        return result;
    }

    function check(map, content) {
        var result = [];
        var count = content.length;
        var stack = [];
        var point = map;
        for (var i = 0; i < count; ++i) {
            var ch = content.charAt(i);
            var item = point[ch];
            if (typeof(item) == "undefined") {
                i = i - stack.length;
                stack = [];
                point = map;
            }
            else if (item["empty"]) {
                stack.push(ch);
                result.push(stack.join(""));
                stack = [];
                point = map;
            }
            else {      
                stack.push(ch);
                point = item;
            }
        }
        return result;
    }

    var test3 = function (sensitive_words,content) {
        var map = buildMap(sensitive_words.sort());         
        var begin = new Date();
        var words = check(map, content);
        console.log((new Date())-begin);
        console.log(words);
    }

```
