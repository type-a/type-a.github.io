---
layout: post
title:  "hello Blockchain!"
date:   2018-06-20 15:14:54
categories: python, blockchain
tags: python, blockchain
excerpt: How to build a blockchain from scratch and its different miners too
mathjax: true
---


Well we all know what blockchain is. 

**FOR THOSE WHO ARE NEW TO THE PARTY HAVE SEAT BECAUSE THINGS ARE GOING TO GET INTERESTING**

This post is all about getting you started with coding your own blockchain or you might wanna call it **your-name-chain** that is cool too.

So before we begin with all the technicalities we might wanna know why this particular thing was invented in the first place.

Well we might want to rewind our timeframe a little bit and go the era when there was no internet! (CAN'T IMAGINE I KNOW) but still there is this place called earth where there is no internet and everyone is living there life thinking that this place so called earth is huge. Now suddenly a word spreads that there is this new invention called INTERNET and soon this world was at out fingertips and everything became modern from businesses to societies everything became connected and services soon vent offline(NOW YOU'D BE THINKING WHERE IS THE PROBLEM HERE). By 2008 about 2 decades of internet later every single trade that was ever made was on internet. 100's of billion of dollars of commodities were traded everyday without knowing who was there on the other side. Now what is the problem here? the problem was trust every business on the internet has to trust a third party to soo the business and that my friend is a problem. Because it is exactly the same reason as you don't trust me with your money or your girlfriend, people don't trust others with their stuff either. Now something had to be done so **SATOSHI NAKAMOTO** came up with this fantastic solution called a *BLOCKCHAIN*. Now the idea of blockchain was very clever to build trust over the internet build a dependent source where you trust not one but everyone on the internet. Ummmmmmmmm.... WHAT?

Let's take an example:

I have 4 dollar and I give it to you and trust you with it but you are no good friend and give me back only 1 dollar telling that I gave you only one dollar. Now it's a huge loss for me and I cannot prove him wrong because nobody else knows about the deal(DAMNNNNN). (THIS IS HOW INTERNET WORKS BTW)

Well now I say, Hey keep 4 dollar and I give away 1 cent to 50 people to keep a record of this deal. Next time if he return you 1 dollar you can verify very easily that he is lying. Now you don't really need to know those 50 people because they only care about the incentive they get. But let's say somehow out of those 50 people 3 people scam you to change the registry of the deal to 1 dollar (THIS IS A GOOD CASE BECAUSE THIS WILL HAPPEN TO YOU). this means either 3 people are lying or the rest 47, well we can safely assume that the 3 are lying and set the deal back to 4 dollar. so I spend about 4.50 dollar to make sure that I can trust the person on the other side of the internet. --NOT A VERY DESCRIPTIVE BUT VERY DEFINITIVE EXAMPLE

So for all the geeks out there ENOUGH OF THEORY LET's CODE SOME BLOCKCHAIN

Tho before we start coding we might wanna know about what do we really want to code and how do we put all the idea of blockchain into a single .code file

Now let's imagine a train(in our case a LinkedList). the train starts with a engine(we'll call it our genesis block because it sounds cool) and everything that happens will be added to the train as it's component or a block. 

**A BLOCK AND A CHAIN**

Now everything I mean every transaction that happens between multiple parties will be stored in the block or the component. Now if anyone wants to change it the link of the train breaks and basically the train crashes.

FANTASTIC now we know how the train is built but before adding anything to the train we want to make sure that a lot of people know about the transaction. How do we do that? let people mine(now by mining I mean keep a record of the transaction to the block). So the fundamental idea of blockchain is nothing but a train which adds everything as block after getting mined by someone.(SORTED)

I'll be coding in python, you can code in any language you like.

Block.py
```python
from hashlib import sha256
import time


class Block:
    # the basic definition of a component of transaction (A BLOCK)
    previousHash = "0x7784" # some random variable to start with ..
    newHash = "0x12344" # this too
    nNonce = -1 # adds a bit of protection to blockchain

    def __init__(self, index, data, difficulty):
        self.index = index # the index of the block where it is positioned in the train
        # it's my blockchain so it can take any data or index or whatever suits my need of my blockchain
        self.data = data # the data that passes through it 
        self.difficulty = difficulty # this really is the most important part we'll talk about it in detail

    def gethash(self):
        return self.previousHash # the hash is nothing but a clever way to hide inormation from humans
        # it basically points to the hash that the block contain
        # it plays a really important part of also pointing to the next block

    def mineblock(self): # here comes the interesting part about the blockchain
        while self.difficulty < 1000000: # it's my mining test 
        # actual mining algorithms are not this shitty (We'll talk about that in details)
            self.newHash = self.calculatehash() # we calculate the cryptic HASH
            self.difficulty += 1
            self.nNonce += 1
        print("New Block mined : "+self.newHash)

    def calculatehash(self):
        tre = str(str(self.index) + self.data + str(time.asctime()) + self.previousHash + str(self.nNonce))
        tre = tre.encode('utf-8')
        return sha256(tre).hexdigest() # return an completely unreadable hexcode 

```

what we just saw was a basic prototype of how our train's component should look like. (GET CREATIVE HERE)
write your own mining algorithm(maybe make it more complex)

Next we wanna see how our train should look like 


```python 
import Block # import all the above code 

class Blockchain():

    lis = [Block.Block(0,"Genesis block",10)] # out train engine 
    newblock = Block.Block # take a new block

    def addnewblock(self,newblock): # we start adding our transactions to our train
        newblock.previousHash = self.getlasthash().gethash() # link the last transaction to the new one
        # why do we do that? to make sure that we add every record in a seqeuntial way 
        # can we build it using a tree data structure? sadly we can't we want a element dependent data 
        # structure. so that all elements know where the fault is. well we can do that with a tree too
        # to answer it you sgoud really implement one (FOOD for THOUGHT)
        newblock.mineblock() # before we add we let someone know about it 
        self.lis.append(newblock) # add add add

    def getlasthash(self):
        return self.lis[len(self.lis)-1] # the last block of the train

```

This is simple isn't it? **HELL YEAAH**

so we have all the pieces of our train and components we might wanna try it out now..


**YAYYYYYYYYYYAYAYYAYAYAYA**

```python

import Blockchain
import Block

a = Blockchain.Blockchain()
a.addnewblock(Block.Block(1,"this is block #1",10)) # add your data to the block
a.addnewblock(Block.Block(2,"block #2",10)) # same goes here 
a.addnewblock(Block.Block(3,"you should be coding by now",10)) # and here
a.addnewblock(Block.Block(4,"stop looking start coding",10)) # here too
a.addnewblock(Block.Block(5,"great !!!",10)) # .... 
a.addnewblock(Block.Block(6,"you don't know how to code? :(",10)) # :)
```

This is easy isn't it. 
If you are still reading the blog and haven't slept off or broke you computer or killed yourself. It is a good idea for the weekend to code it the way you want it. 

Next time we will dig deep in *MINING* and look about all the mining algorithms that are used.
And we might also implement some real example like a chat server or you suggest.

Have suggestions mail me @ [ME][sarkar.anurag@outlook.com]

PEACE :)

