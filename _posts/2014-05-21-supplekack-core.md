---
layout: page
title: "Supplejack Common"
category: dev
date: 2014-05-21 15:02:56
order: 4
---

The Supplejack Common is a gem which provides the DSL for parsers in Supplejack. It is comprised of strategies for different types of harvests, for example JSON, XML, RSS, and OAI. It also implements a strategy for parsing one of DigitalNZ's more tricky harvests, [TAPUHI](http://tapuhi.natlib.govt.nz/). The purpose of the gem is to abstract all the DSL specific code for the [Supplejack Manager](http://digitalnz.github.io/supplejack/start/supplejack-manager.html) and the [Supplejack Worker](http://digitalnz.github.io/supplejack/start/supplejack-worker.html).

You should not need to update this project unless a change to [the DSL](http://digitalnz.github.io/supplejack/manager/parser-dsl-domain-specific-language.html) needs to be made.
