---
title: "A functional implementation Depth-First Search"
linktitle: "A functional implementation Depth-First Search"
date: 2020-12-09
lastmod: 2020-12-09
draft: false
categories: ["Computer Science", "Racket"]
---

For the last few weeks I have been learning [Racket](https://racket-lang.org/). Coincidentally, [Advent Of Code](https://adventofcode.com) is back one more year. I decided to try to solve it with Racket, to force myself to learn more about the syntax and, well, use it.

In particular, [Day 7](https://adventofcode.com/2020/day/7) is a graph problem. Basically we want to see how many vertices can reach a particular destination.

This is straighforward. We can use breadth-first search (BFS) or depth-first search (DFS) on every vertex, and that way we'll know how many can reach it.


Racket already has a [graph library](https://docs.racket-lang.org/graph/index.html). But I'm doing this to learn, not to ship production code. I have implemented DFS several times in other languages, and I also wanted to write my own implementation in Racket.

## Depth-first search ##

DFS is easy to implement following an imperative paradigm. We have the following code from the [Wikipedia](https://en.wikipedia.org/wiki/Depth-first_search#Pseudocode):

```
 procedure DFS(G, v) is
    label v as discovered
    for all directed edges from v to w that are in G.adjacentEdges(v) do
        if vertex w is not labeled as discovered then
            recursively call DFS(G, w)
```

We are recursively applying DFS on every neighbor of the origin. We can add some termination conditions as well:

```
 procedure DFS(G, v, dest) is
    if vertex is discovered
	    return false
    if there are no neighbors
	    return false
    if v is equal to dest
	    return true
    label v as discovered
    for all directed edges from v to w that are in G.adjacentEdges(v) do
        if vertex w is not labeled as discovered then
            recursively call DFS(G, w)
```

The problem is that we are relying on an outside variable `v` to carry the state. The output of `DFS` depends on the input arguments AND on this variable. If we were to implement it this way we would be following an [imperative](https://en.wikipedia.org/wiki/Imperative_programming) style, rather than [functional](https://en.wikipedia.org/wiki/Functional_programming) style.

## An imperative approach ##

Here's an example of my first take at this problem. Notice that discovered is a global variable that we [modify](https://en.wikibooks.org/wiki/Scheme_Programming/Mutability) from within our function:

{{< highlight racket >}}
(define (DFS graph node dest)
  (define counter 0)
  (define (DFS* node discovered)
    (set-add! discovered node)
    (for ([neighbor (hash-ref graph node)])
      (cond ((equal? (neighbor-name neighbor) dest)
             (set! counter (+ counter 1)))
            ((equal? (set-member? discovered (neighbor-name neighbor)) #f)
             (DFS* (neighbor-name neighbor) discovered)))))
  (DFS* node (mutable-set empty))
  (if (> counter 0)
      1
      0))
{{< / highlight >}}

Not the pretties implementation, it was a quick and dirt hack to solve the particular problem I was facing. But here we can see how I'm modifying a global variable by mutating `discovered`. This is fine in other languages, like Python, but it's not idiomatic in Racket.

## Functional DFS ##

The following functions show a possible approach on how to implement DFS following a functional style (from [HtDP](http://htdp.org/2003-09-26/Book/curriculum-Z-H-35.html), with minor modifications):

{{< highlight racket >}}
#lang racket

(define graph
;;  B ---> C
;;  ^\     |
;;  | \    v
;;  |  \-->F 
;;  |      |
;;  A<------
;;  |
;;  v
;;  E----->D
  (hash 'A '(B E)
	'B '(F C)
	'C '(F)
	'D empty
	'E '(D)
	'F '(A)))

(define (node-neighbors node graph)
  (hash-ref graph node))


(define (DFS origin destiny graph)
  (cond [(equal? origin destiny) (list destiny)]
        [else
         (let ((route (DFS* (node-neighbors origin graph) destiny graph)))
           (if (equal? route #f)
               #f
               (cons origin route)))]))


(define (DFS* origin-nodes destiny graph)
  (cond [(empty? origin-nodes) #f]
        [else
         (let ((route (DFS (first origin-nodes) destiny graph)))
           (if (equal? route #f)
               (DFS* (rest origin-nodes) destiny graph)
               route))]))
{{< / highlight >}}

First we define our graph as a [hash table](https://docs.racket-lang.org/reference/hashtables.html), where the key represents the vertex, and the value the neighboring vertices. It represents a direct, unweighted graph.

`DFS` is the main function. It will return `#f` if no path exist, and a path if a connection exists. For example:

{{< highlight scheme >}}
> (DFS 'A 'C graph)
> '(A B C)
> (DFS 'D 'C graph)
> #f
{{< / highlight >}}

We use the helper function `DFS*`. If there are no neighbors it will terminate (return false). If there are any neighbors it will first try to find a route from the origin to the first neighbor calling `DFS`. If such route exists, it will return it. If it doesn't, it will recursively call itself on remaining neighbors.

## Where was I?  ##

Do you notice something missing in this implementation? We are not keeping track of vertices we have visited.

What problems might this cause? Let's try to find the path between `B` and `D`

{{< highlight racket >}}
> (DFS 'B 'D graph)
> ...
{{< / highlight >}}

The program will never finish. The reason is that we have a cyclic graph: `B -> F -> A -> B` and `B -> C -> F -> A -> B`; our program will enter an infinite recursion.

If we add a print statement in the `DFS` call to see which nodes we are visiting, this is what we will see:

```
...
'A
'B
'F
'A
'B
'F
'A
'B
'F
'A
...
```

`DFS` is visiting the same vertices again and again and again, since it has no memory of having visited them already.

## Keeping track of the state (without a global state): accumulator ##

To summarize, the problem with our previous algorithm is that it "forgets" which vertices it has visited. In the first implementation I showed I kept track of this information by using a global variable. How can we do it following a functional style?

A common pattern in functional programming to solve this problem is to pass an extra argument to the function, the accumulator, in which we accumulate the knowledge we want to keep. 

Let's apply it to our DFS function:
{{< highlight racket >}}
(define (DFS.v2 origin destiny graph accu)
  (cond [(equal? origin destiny) (list destiny)]
        [(set-member? accu origin) #f]
        [else
         (let ((route (DFS*.v2 (node-neighbors origin graph)
			          destiny
					  graph
					  (set-add accu origin))))
           (if (equal? route #f)
               #f
               (cons origin route)))]))


(define (DFS*.v2 origin-nodes destiny graph accu)
  (cond [(empty? origin-nodes) #f]
        [else
         (let ((route (DFS.v2 (first origin-nodes) destiny graph accu)))
           (if (equal? route #f)
               (DFS*.v2 (rest origin-nodes) destiny graph accu)
               route))]))

{{< / highlight >}}

We add a new termination condition: if we have already visited a vertex, there is no path. Also, when we call the helper function, we add the current vertex to the accumulator.

Now we can get a path from `B` to `D`, even though we have a cycle; we know our function will always terminate:

{{< highlight racket >}}
> (DFS.v2 'B 'D  graph (set empty))
> '(B F A E D)
> (DFS.v2 'D 'A graph (set empty))
> #f
{{< / highlight >}}

Finally to make the interface the same as our original `DFS`, we can wrapp this new version around a new function:

{{< highlight racket >}}
(define (DFS-accu origin destiny graph)
  (DFS.c2 origin destiny graph (set empty)))
{{< / highlight >}}
