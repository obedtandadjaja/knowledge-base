# Disjoint Set Union
A data structure that keeps track of a set of elements partitioned into a number of disjoint (non-overlapping) subsets. It is most useful to check whether a given graph contains a cycle or not, using `Union-Find` algorithm.

For each edge, make subsets using both the vertices of the edge. If both the vertices are in the same subset, a cycle is found.

Idea behind this data structure is to make each vertices a subset and only join two subsets together when an edge connects the two. In a sense, we are building a graph step by step by iterating through the edges, and trying to figure out if there is any cycle along the way.

Steps:

- Keep track of subsets in an array of same length as the number of vertices, let's call that subsets[].
- Initialize the array with -1s. This indicates that the vertice's subset is the array index.
- Iterate through the edges and find the subset of the two vertices. If they are the same then a cycle exists in the graph. If they are not the same then join the two subsets.

## Pseudocode
```java
public boolean hasCycle(Graph graph) {
  int[] subsets = new int[graph.number_of_vertices];
  
  // initialize array with -1s
  Arrays.fill(subsets, -1);
  
  // Iterate through all edges of graph, find subset of both vertices of every edge
  // if both subsets are the same, then there is a cycle in graph
  for(int i = 0; i < graph.edges.size(); i++) {
    int subset1 = find(subsets, graph.edges.get(i).src);
    int subset2 = find(subsets, graph.edges.get(i).dst);
    
    if(subset1 == subset2) return true;
    
    union(subsets, subset1, subset2);
  }
  
  return false;
}

public int find(int[] subsets, int vertice) {
  if(subsets[vertice] == -1) return vertice;
  
  return find(subsets, subsets[vertice]);
}

public union(int[] subsets, int vertice1, int vertice2) {
  int vertice1_subset = find(subsets, vertice1);
  int vertice2_subset = find(subsets, vertice2);
  
  subsets[vertice1_subset] = vertice2_subset;
}

// Graph implementation
class Graph {
  int number_of_vertices;
  List<Edge> edges;
  
  public Graph(int number_of_vertices) {
    this.number_of_vertices = number_of_vertices;
    this.edges = new ArrayList<Edge>();
  }
  
  private class Edge {
    int src, dst;
    
    public Edge(int src, int dst) {
      this.src = src;
      this.dst = dst;
    }
  }
  
  public Edge(int src, int dst) {
    if(src < 0 || src >= number_of_vertices || dst < 0 || dst >= number_of_vertices)
      throw ArgumentError();
    
    this.edges.add(new Edge(src, dst));
  }
}
```
