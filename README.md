## libgraph
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Library for working with graphs

- [Breadth-first search, bfs](#bfs)
- [Breadth-first search, bfs with visitor](#bfs-visitor)
- [Depth-first search, dfs](#dfs)
- [Depth-first search, dfs with visitor](#dfs-visitor)
- [Search for connectivity components in an undirected graph](#connected-components)
- [Topological sorting](#topological-sort)
- [Search for components of strong connectivity in a directed graph](#strongly-connected-components)
- [Dijkstra's algorithm (finding the shortest distance from one vertex to all the others)](#dijkstra)
- [Construction of a minimal spanning tree (Kruskal's algorithm)](#kruskal)
- [Search for the depth (distance) from a given vertex to all others](#depths_vertices)
- [Finding the least common ancestor (LCA)](#lca)
- [Floid 's Algorithm](#floid)
- [Search for cycles in a graph](#finding-cycle)
- [Search for bridges in the graph](#finding-bridges)
- [The Ford-Bellman algorithm](#bellman-ford)

```rust
use libgraph::{GraphKind, Graph, version};
fn main() {

    let ver = version();

    /*
    * Creating a graph of 10 vertices. Vertex numbering starts from 0.
    * 10 vertices will be created that are not connected to each other
    */
    let mut graph = GraphKind::Undirected::<f64>(Graph::new(10));

    /*
    * Connecting the vertices
    */
    graph.add_edge(1, 2, 2.0).unwrap();
    graph.add_edge(2, 3, 2.0).unwrap();
    graph.add_edge(3, 7, 3.0).unwrap();
}
```
#### <a id="bfs"/> Breadth-first search, bfs
Algorithmic complexity <b>O(V + E)</b>, where V is the number of vertices in the graph and E is the number of edges.
```rust
use libgraph::{bfs, path_iter, Graph, GraphKind};

fn main(){
    let mut graph = GraphKind::Undirected(Graph::new(100));
    graph.add_edge(0, 1, 0.0).unwrap();
    graph.add_edge(1, 2, 0.0).unwrap();
    graph.add_edge(2, 3, 0.0).unwrap();
    graph.add_edge(2, 4, 0.0).unwrap();
    graph.add_edge(2, 5, 0.0).unwrap();
    graph.add_edge(4, 8, 0.0).unwrap();
    graph.add_edge(8, 17, 0.0).unwrap();
    let parents = bfs(&graph, 0).unwrap();
    assert_eq!(path_iter(5, &parents).collect::<Vec<usize>>(), vec![5, 2, 1, 0]);
    assert_eq!(path_iter(19, &parents).collect::<Vec<usize>>(), vec![]);
}
```

#### <a id="bfs-visitor"/> Breadth-first search, bfs with visitor
```rust
use libgraph::{GraphKind, Graph, bfs_visitor, BfsEvent};

fn main() {
    let mut graph = GraphKind::Directed(Graph::new(100));

    graph.add_edge(1, 2, 0.0).unwrap();
    graph.add_edge(2, 3, 0.0).unwrap();
    graph.add_edge(2, 4, 0.0).unwrap();
    graph.add_edge(2, 5, 0.0).unwrap();
    graph.add_edge(4, 8, 0.0).unwrap();
    graph.add_edge(8, 17, 0.0).unwrap();

    let mut vertexes = vec![];
    let _parents = bfs_visitor(&graph, 1, |event|{
        match event {
            // The event is called before you start exploring the vertex (extracting it from the queue).
            // Событие вызывается до того, как вы начнете исследовать вершину (извлекая ее из очереди).
            BfsEvent::ExamineVertex(usize) =>{

            }
            // The event is called when the algorithm first encounters a vertex in the traversal.
            // Событие вызывается, когда алгоритм впервые встречает вершину в обходе.
            BfsEvent::DiscoverVertex(vertex) => {
                vertexes.push(vertex);
            }
            // The event is triggered when the edge under investigation is an edge of the tree after traversal.
            // Событие срабатывает, когда исследуемое ребро является ребром дерева после обхода.
            BfsEvent::IsTreeEdge(from, to) => {

            }
            // The event is triggered when the edge under investigation is not an edge of the tree after traversal.
            // Событие срабатывает, когда исследуемое ребро не является ребром дерева после обхода.
            BfsEvent::IsNotTreeEdge(from, to) => {

            }
            // The event is triggered if the target vertex is grayed out at the time of the study.
            // Gray color means that the vertex is currently in the queue.
            // Событие вызывается, если в момент исследования целевая вершина выделена серым цветом
            // Серый цвет означает, что вершина в данный момент находится в очереди.
            BfsEvent::GrayTarget(usize) => {

            }
            // The event is triggered if the target node is colored black at the time of the study.
            // The black color means that the vertex is no longer in the queue.
            // Событие вызывается, если в момент исследования целевой узел окрашен в черный цвет.
            // Черный цвет означает, что вершина больше не находится в очереди.
            BfsEvent::BlackTarget(usize) =>{

            }
            // The event is called after examining all outgoing edges and all neighboring vertices.
            // Событие вызывается после изучения всех исходящих ребер и всех соседних вершин.
            BfsEvent::FinishVertex(vertex) => {

            }
        }
        // If true is returned, the traversal will be completed after calling this callback
        // Если возвращается true, то обход будет завершен после вызова этого обратного вызова
        false
    }).unwrap();

    assert_eq!(vertexes, vec![1, 2, 3, 4, 5, 8, 17]);
}
```
#### <a id="dfs"/> Depth-first search, dfs
Algorithmic complexity <b>O(V + E)</b>, where V is the number of vertices in the graph and E is the number of edges.

```rust
use libgraph::{dfs, path_iter, Graph, GraphKind};

fn main(){
    let mut graph = GraphKind::Directed(Graph::new(10));
    graph.add_edge(1, 2, 0.0).unwrap();
    graph.add_edge(2, 3, 0.0).unwrap();
    graph.add_edge(3, 5, 0.0).unwrap();

    let parents = dfs(&graph, 1).unwrap();
    assert_eq!(path_iter(5, &parents).collect::<Vec<usize>>(), vec![5, 3, 2, 1]);

    let parents = dfs(&graph, 1).unwrap();
    assert_eq!(path_iter(77, &parents).collect::<Vec<usize>>(), vec![]);
}
```

#### <a id="dfs-visitor"/> Depth-first search, dfs with visitor

```rust
use libgraph::{dfs, dfs_visitor, DfsEvent, path_iter, Graph, GraphKind};

fn main(){
    let mut graph = GraphKind::Directed(Graph::new(10));

    graph.add_edge(1, 2, 0.0).unwrap();
    graph.add_edge(2, 3, 0.0).unwrap();
    graph.add_edge(3, 5, 0.0).unwrap();

    let mut vertexes = vec![];

    let _ = dfs_visitor(&graph, 1, |event|{
        match event {
            // The event is called when the algorithm first encounters a vertex in the traversal
            // Событие вызывается, когда алгоритм впервые встречает вершину в обходе
            DfsEvent::DiscoverVertex(vertex, _time_in) => {
                vertexes.push(vertex);
            }
            // The event is called before you start exploring the vertex.
            // Событие вызывается до того, как вы начнете исследовать вершину.
            DfsEvent::ExamineVertex(_vertex) => {

            }
            // The event is triggered when the edge under investigation is an edge of the tree after traversal.
            // Событие срабатывает, когда исследуемое ребро является ребром дерева после обхода.
            DfsEvent::IsTreeEdge(_from, _to) => {

            }
            // The event is triggered when we return to the ancestor from which we explored the vertex.
            // Событие срабатывает, когда мы возвращаемся к предку, из которого мы исследовали вершину.
            DfsEvent::ReturnParent(_from,_) => {

            }
            // The event is triggered when we try to follow the reverse edge.
            // Событие срабатывает, когда мы пытаемся пойти по обратному ребру.
            DfsEvent::BackEdge(_from, _to) => {

            }
            // The event is triggered when we try to walk along a straight or transverse edge (only for a directed graph).
            // Событие срабатывает, когда мы пытаемся пройти по прямому или поперечному ребру (только для ориентированного графа).
            DfsEvent::ForwardOrCrossEdge(_, _to) => {

            }
            // The event is called after examining all outgoing edges and all neighboring vertices.
            // Событие вызывается после изучения всех исходящих ребер и всех соседних вершин.
            DfsEvent::FinishVertex(_vertex, _time_out) => {
            }
            _ =>{

            }
        }
        // If true is returned, the traversal will be completed after calling this callback
        // Если возвращается true, то обход будет завершен после вызова этого обратного вызова
        false
    }).unwrap();
    assert_eq!(vertexes, vec![1, 2, 3, 5]);
}
```
#### <a id="connected-components"/> Search for connectivity components in an undirected graph
The connectivity component of an undirected graph is a subset of vertices reachable from some given vertex.
Due to disorientation, all vertices of a connected component are reachable from each other.
Algorithmic complexity <b>O(V + E)</b>, where V is the number of vertices in the graph and E is the number of edges.
```rust
use libgraph::{connected_components, Empty, Graph, GraphKind};

fn main(){
    let mut graph = GraphKind::Undirected::<Empty>(Graph::new(6));
    graph.add_edge(0, 2, Empty).unwrap();
    graph.add_edge(2, 5, Empty).unwrap();
    graph.add_edge(2, 3, Empty).unwrap();
    graph.add_edge(5, 3, Empty).unwrap();
    graph.add_edge(1, 4, Empty).unwrap();

    let components = connected_components(&graph).unwrap();
    assert_eq!(components[0], [0, 2, 5, 3]);
    assert_eq!(components[1], [1, 4]);
    assert_eq!(components.len(), 2);
}
```

#### <a id="topological-sort"/> Topological sorting
The task of topological sorting of a graph is as follows: given an oriented graph, you need to find such an order of vertices that all its edges lead from an earlier vertex to a later one. The algorithmic complexity is <b>O(V + E)</b>, where V is the number of vertices and E is the number of edges.
<b>Note that this sorting only applies to directed and acyclic graphs.</b>
```rust
use libgraph::{topological_sort, Graph, Empty, GraphKind};

fn main(){
    let mut graph = GraphKind::Directed::<f32>(Graph::new(10));
    graph.add_edge(0, 1, 0.0).unwrap();
    graph.add_edge(1, 2, 0.0).unwrap();
    graph.add_edge(1, 3, 0.0).unwrap();
    graph.add_edge(1, 5, 0.0).unwrap();
    graph.add_edge(1, 4, 0.0).unwrap();
    graph.add_edge(2, 4, 0.0).unwrap();
    graph.add_edge(3, 4, 0.0).unwrap();
    graph.add_edge(3, 5, 0.0).unwrap();
    assert_eq!(topological_sort(&graph).unwrap(), vec![9, 8, 7, 6, 0, 1, 3, 5, 2, 4]);
}
```

#### <a id="strongly-connected-components"/> Search for components of strong connectivity in a directed graph
Two vertices of a directed graph are strongly connected if there is a path from one to the other and vice versa. In other words, they both lie in some cycle. The complexity of the algorithm is <b>O(V + E)</b>, where V is the number of vertices and E is the number of edges.
```rust
use libgraph::{strongly_connected_components, Graph, Empty, GraphKind};

fn main(){
    let mut graph = GraphKind::Directed::<Empty>(Graph::new(10));
    graph.add_edge(1, 4, Empty).unwrap();
    graph.add_edge(4, 7, Empty).unwrap();
    graph.add_edge(7, 1, Empty).unwrap();
    graph.add_edge(9, 7, Empty).unwrap();
    graph.add_edge(6, 9, Empty).unwrap();
    graph.add_edge(9, 3, Empty).unwrap();
    graph.add_edge(3, 6, Empty).unwrap();
    graph.add_edge(8, 6, Empty).unwrap();
    graph.add_edge(2, 8, Empty).unwrap();
    graph.add_edge(8, 5, Empty).unwrap();
    graph.add_edge(5, 2, Empty).unwrap();

    let strongly_connected_components = strongly_connected_components(&graph).unwrap();

    assert_eq!(strongly_connected_components[0], vec![8, 5, 2]);
    assert_eq!(strongly_connected_components[1], vec![9, 3, 6]);
    assert_eq!(strongly_connected_components[2], vec![4, 7, 1]);
    assert_eq!(strongly_connected_components[3], vec![0]);
}
```

#### <a id="dijkstra"/> Dijkstra's algorithm
Dijkstra's algorithm finds the shortest paths from a given vertex to all other vertices of the graph without edges with negative weight. The complexity of the algorithm is <b>O(E log(V))</b>, where V is the number of vertices of the graph, and E is the number of edges.
```rust
use libgraph::{dijkstra, path_iter, Graph, GraphKind};

fn main(){
    let mut graph = GraphKind::Undirected::<f32>(Graph::new(10));
    graph.add_edge(1, 2, 7.0).unwrap();
    graph.add_edge(1, 6, 14.0).unwrap();
    graph.add_edge(1, 3, 9.0).unwrap();
    graph.add_edge(6, 3, 2.0).unwrap();
    graph.add_edge(6, 5, 9.0).unwrap();
    graph.add_edge(3, 2, 10.0).unwrap();
    graph.add_edge(3, 4, 11.0).unwrap();
    graph.add_edge(2, 4, 15.0).unwrap();
    graph.add_edge(4, 5, 6.0).unwrap();

    let (parents, distances) = dijkstra(&graph, 1).unwrap();
    assert_eq!(distances[4].unwrap(), 20.0);
    assert_eq!(path_iter(4, &parents).collect::<Vec<usize>>(), vec![4, 3, 1]);
}
```

#### <a id="kruskal"/> Construction of a minimal spanning tree (Kruskal's algorithm)
Kruskal's algorithm is an efficient algorithm for constructing a minimal spanning tree of a weighted connected undirected graph. The complexity of the algorithm is <b>O(E log(E))</b>, where E is the number of edges in the graph.
```rust
use libgraph::{kruskal, Graph, GraphKind, SpanningTreeEdge};

fn main(){
    let mut graph = GraphKind::Undirected(Graph::new(20));
    graph.add_edge(0, 1, 3.0).unwrap();
    graph.add_edge(1, 2, 7.0).unwrap();
    graph.add_edge(1, 4, 5.0).unwrap();
    graph.add_edge(2, 3, 8.0).unwrap();
    graph.add_edge(2, 5, 7.0).unwrap();
    graph.add_edge(2, 4, 9.0).unwrap();
    graph.add_edge(3, 5, 5.0).unwrap();
    graph.add_edge(5, 7, 9.0).unwrap();
    graph.add_edge(5, 6, 8.0).unwrap();
    graph.add_edge(5, 4, 15.0).unwrap();
    graph.add_edge(6, 7, 11.0).unwrap();
    graph.add_edge(6, 4, 6.0).unwrap();
    let edges: Vec<SpanningTreeEdge<f64>> = kruskal(&graph).unwrap();
    let summary_weight: f64 = edges.iter().map(|edge| edge.weight).sum();
    assert_eq!(42.0, summary_weight);
    let res = edges
        .iter()
        .map(|edge| (edge.from, edge.to))
        .collect::<Vec<(usize, usize)>>();
    assert_eq!(res, vec![(0, 1), (3, 5), (4, 1), (4, 6), (1, 2), (5, 2), (7, 5)]);
}
```

#### <a id="depths_vertices"/> Search for the depth (distance) from a given vertex to all others
The complexity of the algorithm is <b>O(V + E)</b>, where V is the number of vertices and E is the number of edges. This algorithm is applicable only to an undirected graph.

```rust
use libgraph::{depths_vertices, Graph, GraphKind};

fn main(){
    let mut graph = GraphKind::Undirected(Graph::new(13));
    graph.add_edge(1, 4, 0).unwrap();
    graph.add_edge(1, 2, 0).unwrap();
    graph.add_edge(4, 11, 0).unwrap();
    graph.add_edge(4, 12, 0).unwrap();
    graph.add_edge(12, 3, 0).unwrap();
    graph.add_edge(2, 5, 0).unwrap();
    graph.add_edge(2, 6, 0).unwrap();
    graph.add_edge(5, 9, 0).unwrap();
    graph.add_edge(5, 10, 0).unwrap();
    graph.add_edge(6, 7, 0).unwrap();
    graph.add_edge(7, 8, 0).unwrap();

    let depths = depths_vertices(&graph, 1).unwrap();
    assert_eq!(depths[3], Some(3));
    assert_eq!(depths[8], Some(4));
    assert_eq!(depths[11], Some(2));
}
```

#### <a id="lca"/> Finding the least common ancestor (LCA)
The algorithmic complexity of the construction is <b>O(V + E)</b>, where V is the number of vertices of the graph, and E is the number of edges. The algorithmic complexity of the response is O(log(V)). This algorithm is valid only for an acyclic undirected graph (tree)

```rust
use libgraph::{Graph, GraphKind, Lca};

fn main(){
    let mut graph = GraphKind::Undirected(Graph::new(9));
    graph.add_edge(0, 1, 0).unwrap();
    graph.add_edge(1, 2, 0).unwrap();
    graph.add_edge(1, 3, 0).unwrap();
    graph.add_edge(2, 4, 0).unwrap();
    graph.add_edge(2, 5, 0).unwrap();
    graph.add_edge(3, 6, 0).unwrap();
    graph.add_edge(3, 7, 0).unwrap();
    graph.add_edge(7, 8, 0).unwrap();

    let lca = Lca::build(&graph, 0).unwrap();

    assert_eq!(2, lca.query(4, 5).unwrap());
    assert_eq!(1, lca.query(4, 8).unwrap());
    assert_eq!(2, lca.query(4, 2).unwrap());
    assert_eq!(1, lca.query(2, 8).unwrap());
    assert_eq!(3, lca.query(6, 8).unwrap());
}
```

#### <a id="floid"/> Floid 's Algorithm
An algorithm for finding the length of shortest paths between all pairs of vertices in a weighted directed graph. It works correctly if there are no negative value cycles in the graph, and if there is such a cycle, it reports its presence.
The complexity of the algorithm is <b>O(V^3)</b>, where V is the number of vertices of the graph.

```rust
use libgraph::{GraphKind, Graph, floid};

fn main(){
    let mut graph = GraphKind::Directed::<i32>(Graph::new(5));
    graph.add_edge(1, 2, 1).unwrap();
    graph.add_edge(1, 3, 6).unwrap();
    graph.add_edge(2, 3, 4).unwrap();
    graph.add_edge(2, 4, 1).unwrap();
    graph.add_edge(4, 3, 1).unwrap();

    let dist = floid(&graph).unwrap();
    assert_eq!(dist.get_dist(1, 2).unwrap(), 1);
    assert_eq!(dist.get_dist(1, 4).unwrap(), 2);
}
```

#### <a id="finding-cycle"/> Search for cycles in a graph
An algorithm for searching for a cycle in a graph. The algorithm is applicable to graphs of both types.
The complexity of the algorithm is <b>O(E)</b>, where E is the number of edges of the graph.

```rust
use libgraph::{GraphKind, Graph, finding_cycle};

fn main(){
    let mut graph = GraphKind::Undirected(Graph::new(10));
    graph.add_edge(1, 2, 0.0).unwrap();
    graph.add_edge(2, 3, 0.0).unwrap();
    assert_eq!(None, finding_cycle(&graph).unwrap());

    graph.add_edge(3, 1, 0.0).unwrap();
    assert_eq!(vec![3, 2, 1], finding_cycle(&graph).unwrap().unwrap());
}
```

#### <a id="finding-bridges"/> Search for bridges in the graph
An algorithm for finding bridges in a graph. The algorithm is applicable to an undirected graph. The complexity of the algorithm is <b>O(V + E)</b>, where V is the number of vertices of the graph, and E is the number of edges. The algorithm does not work with parallel edges.

```rust
use libgraph::{Graph, GraphKind, find_bridges};

fn main(){
    let mut graph = GraphKind::Undirected(Graph::new(8));
    graph.add_edge(1, 2, 0.0).unwrap();
    graph.add_edge(2, 3, 0.0).unwrap();
    graph.add_edge(1, 3, 0.0).unwrap();
    graph.add_edge(3, 4, 0.0).unwrap();
    graph.add_edge(4, 5, 0.0).unwrap();
    graph.add_edge(4, 6, 0.0).unwrap();
    graph.add_edge(5, 6, 0.0).unwrap();
    graph.add_edge(5, 7, 0.0).unwrap();
    let bridges = find_bridges(&graph).unwrap();
    assert_eq!(bridges, vec![(5, 7), (3, 4)])
}
```

#### <a id="bellman-ford"/> The Ford-Bellman algorithm
The Bellman—Ford algorithm is an algorithm for finding the shortest path in a weighted graph. Unlike Dijkstra's algorithm, the Bellman—Ford algorithm admits edges with negative weight. Algorithmic complexity <b>O(V * E)</b>, where V is the number of vertices in the graph and E is the number of edges.

```rust
use libgraph::{bellman_ford, Graph, GraphKind, path_iter};

fn main() {
    let mut graph = GraphKind::Directed(Graph::new(8));

    graph.add_edge(1, 2, 2.0).unwrap();
    graph.add_edge(2, 3, 5.0).unwrap();
    graph.add_edge(3, 5, 7.0).unwrap();
    graph.add_edge(1, 5, 19.0).unwrap();


    let (parents, distances) = bellman_ford(&graph, 1).unwrap();
    assert_eq!(path_iter(5, &parents).collect::<Vec<usize>>(), vec![5, 3, 2, 1]);
    assert_eq!(distances[5].unwrap(), 14.0);
}
```
