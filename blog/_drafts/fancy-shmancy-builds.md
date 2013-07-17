---
title: Shmancy Fancy builds
layout: post
---
Build systems tend to be fairly hefty, especially since the compilation process for their projects tend to take a very long time. However, this approach is naive, and is largely cold build centric. It assumes that the performance cost of the build system doesn't matter because the cost of compiling all the sources is far greater. However, performing a full build is a rare occurance, and is mostly done due to lack of trust in the build system. It's similar to how you could prove the garbage collector for the first versions of Java is correct. It didn't do anything, so it didn't get rid of any live objects. It would eventually (a few versions later) actually remove dead objects from memory, and therefore it was correct.

I've always held that the status quo should be challenged, and in this case, there are better alternatives. For example, there are basically three conditions udner which we want to build a proejct. Either we are actively developing a file, we are pulling in multiple changes to a project, or we are building a fresh copy of the project for the first time. I would contend that the final case is the one where performance doesn't matter, but that it is also the most unlikely. Now, the key to limiting the first two scenarios is to identify accurately what needs to be done.

The traditional approach used by autotools is to create targets that define the desired build state. So if the desired state is install, then it checks which states are needed to reach that state, and so on recursively. Realistically, it should only include the makefile for a directory if a file inside it is needed (in general, Make considers files as states). Rake works in a similar manner, but being built on ruby runs a bit faster. But only a bit. The other approaches I've found are ninja are tup. Ninja operates in a very similar manner, albeit possibly with some preprocessing to cache procedural plans for each build state and optimizations for reducing duplicate operations. Tup takes the dependency tree of build states and inverts it, so it can check each file once and only once to see if it is modified, and then build the states that depend on it.

What I'm proposing is to build a system similar to tup, in the sense that it reacts to file state changes, but that we can run continuously in the background. In this way, we can 