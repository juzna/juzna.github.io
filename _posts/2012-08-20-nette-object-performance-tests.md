---
layout: post
title: Nette\Object performance tests
original_link: https://gist.github.com/3406894
---


# Nette\Object performance tests

I guess you already saw a short note [Latency Numbers Every Programmer Should Know](https://gist.github.com/2841832). I wanted to check how well *Nette* performs with its [magic properties](http://doc.nette.org/en/php-language-enhancements#toc-properties-getters-a-setters) on `Nette\Object`.

You can see the testing code below together with raw output on my machine.

It show how much time in *seconds* it took to execute *one million iterations* of a particular action, thus it is also time in *microseconds* of one such call. You should compare it to *null test*, which execute empty iterations.


## Results

Compared to a native getter of *public property*:
- *method call* is cca 4x slower
- *magic getter* is cca 15x slower
- *magic getter* in form isSomething() is cca 25x slower


## Summary

In a real app this does **not** cause any **issue**. I tested it on an our eshop where it added only cca 1ms to a page load, which was almost nothing to a total page load time.


## Sample Code
{% gist juzna/3406894 property-read.php %}


## Result
```
PHP:	5.3.11
null:	0.23424816131592
direct:	0.38207101821899
method:	0.66310882568359
magic:	6.6905748844147
magic2:	7.112911939621
---------------------------------
PHP:	5.3.12-dev
null:	0.16769599914551
direct:	0.25442695617676
method:	0.4458281993866
magic:	5.5972790718079
magic2:	7.195659160614
---------------------------------
PHP:	5.5.0-dev
null:	0.080867052078247
direct:	0.10454916954041
method:	0.20173192024231
magic:	3.9384489059448
magic2:	4.4427669048309
---------------------------------
PHP:	5.5.0-dev
null:	0.059589147567749
direct:	0.086474895477295
method:	0.17585802078247
magic:	2.877405166626
magic2:	3.1567361354828
---------------------------------
PHP:	5.4.0
null:	0.056331872940063
direct:	0.088710069656372
method:	0.17899608612061
magic:	2.8614220619202
magic2:	3.3825919628143
---------------------------------
```