---
layout: post
title:  "Starting with SCCs"
date:   2017-04-29 02:20:00 +0530
author: "Sarv Shakti Singh"
---

Hello friends, this post is dedicated to knowing what **Strongly Connected Components(SCCs)** mean and for a given directed graph, what would be an effective algorithm to find out all the SCCs in the particular graph. We will also write the code that implements the above said algorithm.

I will try to make this post as easy as possible and interactive.

It is expected that you have some knowledge about implementing **graphs using adjacency lists** and  **depth-first-search(dfs)** in a directed graph in C or C++ beforehand.

## What are SCCs?

In a graph, two nodes(say A and B) are said to be strongly connected if there exists a path from A to B and and back again from B to A. In an undirected graph, if there exists a set of edges that form a path from A to B, then A and B are strongly connected. In a directed graph, there must exist two paths A->B and B->A for them to be strongly connected.

Hence, we can say that **strongly connected components(SCCs)** are the set of vertices in a graph in which we can go from one vertex to any other vertex.

![Just a graph](/../../../scc/screenshot-from-2016-08-06-07-37-161.jpg?raw=true "Image1") 

The above graph has 3 different SCCs. We can see here that since 0 and 2 lie in the same SCC we can move from 0 to 2 and 2 to 0 again. But for vertices 1 and 5, we can move from 1 to 5 but we cannot come back to 1 from 5 since they do not belong to the same SCC.

![Part of graph](/../../../scc/screenshot-from-2016-08-06-07-50-29.jpg?raw=true "Image2")

Now, let us consider the SCCs as individual vertices and create a new graph. Our graph would be reduced to the above graph. Here vertex 0 represents  vertices {0,1,2,3} of our graph, vertex 1 represents vertex {7} and vertex 2 represents vertices {4,5,6}.

**When any graph is reduced to such form, there will be at least one source vertex and at least one sink vertex.** That implies that our original graph will always have one source SCC and one sink SCC which further implies that there will always exist a path from any vertex in the source SCC to any vertex of the graph. And there will always exist a path from any vertex of the graph to any vertex of the sink SCC. (Try taking various examples :) )

Now let us see how to find out the SCCs of a given directed graph.

## The naive approach

The most naive approach that one could use here is taking all the pairs of vertices of the graph and applying dfs. If we can reach from one point to another then we need to apply dfs again from the second point to first. If this dfs is also fruitful, then we will add these two vertices in a SCC.

Now this approach will definitely take a lot of time since in worst case we will have to apply dfs 2 times for every pair of vertices. And since I am too lazy to calculate the exact complexity, we will call it close to O(V*V*E). This is way too slow for bigger graphs.

## Kosaraju's Algorithm

For some time, let us forget that there already exists an algorithm. Let us try to drive it out using our information.

We know what dfs does. Starting from a vertex, it visits all the vertices that are findable from that vertex. And we have learnt just now that "there will always exist a path from any vertex of a graph to any vertex of the sink SCC." What does that mean?

Can it mean that if somehow we apply dfs from a vertex of the sink SCC, it will only traverse those nodes that lie in the sink SCC and not go out of the SCC? Yes. It does mean that. Because, the sink SCC will always have arrows pointed towards itself and no arrow leaves out of the sink SCC.

Now, the next thought is: What if we mark all the nodes 'visited' in this dfs and apply dfs again in that SCC that is nearest to the sink SCC? What will happen is in this dfs we will come to visit all the nodes of that SCC plus the nodes of the sink SCC but as we have already marked them 'visited', all the new nodes found during this dfs must belong to a new SCC.

Congratulations to ourselves, we just found out the most important thing. That is, if somehow, we could manage to find out vertices belonging to different SCCs and arrange them in a manner such that vertex of the sink SCC comes first, then vertex of the SCC nearest to it, and so on, and finally the vertex of the source SCC, then we could easily apply dfs at these nodes in the same order and the vertices that are covered in each dfs will be the vertices of the corresponding SCC.

Please re-read the above three paragraphs until it is clear why we are doing what we are doing.

Now, we will move on to finding the first part of our solution, that is, the arrangement of vertices such that the above 'thing' can be applied.

This can be solved using an interesting property of dfs. In a dfs, a vertex is marked visited when there is no other vertex reachable from it or when all other vertices reachable from it are already marked. So, it can be proved that the vertex that will be marked visited at last will be from the source SCC. The proof is left for the reader's curiosity. :P

If we arrange the vertices of a graph in this order(that is, according to the 'marked visited' time criteria) the arrangement will be such that the last node will be from the source SCC and towards the first node, the nodes will be such that they belong at an apt distance from the source SCC.

**If we rank the vertices of the graph according to the rule that SCCs nearer to the source SCC are ranked higher, we will always find that from backside of the array formed after applying dfs from any point, there will be at least one vertex of a higher rank before any vertex of lower rank.**

Let's take the following graph as example:

![The first image again](/../../../scc/screenshot-from-2016-08-06-07-37-161.jpg?raw=true "Image1")

If we apply dfs at vertex 0,the order would be: {1,4,6,5,2,7,3,0} or {4,6,5,1,2,7,3,0} or similar. We find here that before 5, 7 will always be there. Before 7, at least one of {0,1,2,3} will be there and so on. It is left on the reader to check this by applying dfs from various vertices.

**What if we apply dfs and calculate this array on a graph whose edges are reversed?** Then, we will get an array of the vertices such that from the back side of the array, there will always exist at least one vertex of lower rank before any vertex of higher rank. The reason of this oppositeness is that when the graph is reversed(that is, edges are reversed) the sink SCC becomes source and vice versa. Also, all the other SCC also reverse their states(distance from source SCC becomes distance from sink SCC and vice versa).

Hence, by reversing a given directed graph and applying dfs on any vertex, and then applying dfs on the vertices that are not visited from the back side of the formed array, we will get the SCCs of the given graph.

This is Kosaraju's Algorithm.

## Formal Algorithm:

Given directed graph G,

1.Let Gʳᵉᵛ=G with all edges reversed

2.Run dfs on Gʳᵉᵛ and calculate f(v){finishing time for all vertices} for all v∈V

3.Run dfs on G (processing nodes in decreasing order of finishing times)

## Running Code:

| 

<pre>
#include < bits/stdc++.h >
#define MAXV 1000
using namespace std;
vector < int > g[MAXV], grev[MAXV];
bool visited[MAXV];
int leader[MAXV], finish[MAXV], t=0, V, E;
void dfs_reverse(int src) {     <span style="color: #00ff00;">// running DFS on the reverse graph</span> 
    visited[src] = true;
    for(int i = 0 ; i < grev[src].size() ; i++) {
        if(!visited[grev[src][i]])
            dfs_reverse(grev[src][i]);
    }
    t++;
    finish[t] = src;
}
void dfs(int src) {    <span style="color: #00ff00;">// running DFS on the actual graph</span> 
    visited[src] = true; 
    printf("%d ",src); 
    for(int i = 0 ; i < g[src].size() ; i++)    {
        if(!visited[g[src][i]])
            dfs(g[src][i]);
    }
}
int main()  {
    int i,u,v;
    scanf("%d %d",&amp;V,&amp;E);  <span style="color: #00ff00;">// Enter the number of vertices &amp; edges</span> 
    for(i = 0 ; i < E ; i++)  {
        scanf("%d %d",&amp;u,&amp;v);
        g[u].push_back(v);      <span style="color: #00ff00;">// Insert into the adjacency list of the graph </span>
        grev[v].push_back(u);   <span style="color: #00ff00;">// and the reverse graph</span> 
    }
    <span style="color: #00ff00;">// run dfs on the reverse graph to get finishing time</span> 
    memset(visited, false, V*sizeof(bool)); 
    for(i = 0 ; i < V ; i++)    {
        if(!visited[i])
            dfs_reverse(i);
    }
    <span style="color: #00ff00;">// run dfs on the actual graph in reverse finishing time</span>
    memset(visited, false, V*sizeof(bool)); 
    int count = 0; 
    for( i = V - 1 ; i >= 0 ;i--)  {
        if(!visited[finish[i]]) { 
            printf("SCC #%d: ",++count); 
            dfs(finish[i]); 
            printf("\n"); 
        } 
    }
    return 0; 
}
</pre>

This is the complete working code of finding out SCCs of a directed graph.

I tried as much as possible to give logical explanation of each and every point and tried avoiding any mathematical aspect in the proof or correctness of the algorithm and I hope you did understand this.

**Simple problem for implementation of this is:** 

[CAPCITY](http://www.spoj.com/problems/CAPCITY)

Thank you every one for reading this. Hope you enjoyed reading this. :) Any type of questions will be entertained.