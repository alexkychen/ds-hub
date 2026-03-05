# Knowledge Graph Data Science

## Centrality Algorithms
Centrality algorithms aim at finding important nodes

### Degree centrality
It counts how many direct connections a node has.

"How many friends do I have?", "How many people have one patient contacted with?"

```{code-block} cypher
// Create a in-memory graph projection 
MATCH (a1:Patient)-[:VISITED]->(:Place)<-[:VISITED]-(a2:Patient)
WITH gds.graph.project('patient-network', a1, a2, {}, {}) AS g
RETURN g.graphName, g.nodeCount, g.relationshipCount

// Run degree centrality
CALL gds.degree.stream('patient-network')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS patient,
  score AS contact
ORDER BY score DESC
LIMIT 10
```

### Page Rank
Pange Rank not only consider how many connections one node has but also consider how many influential nodes are connected.

"How many influential friends I have?" One could be ranked higher when connecting to one important friend than to many average friends.

```{code-block} cypher
CALL gds.pageRank.stream('patient-network')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS patient, score AS influence
ORDER BY score DESC
LIMIT 10
```

### Betweenness centrality

Betweenness finds nodes that are bridging other nodes

"If I leave, does the network fall apart?", "Who is the most important one making connection between groups?"

```{code-block} cypher
CALL gds.betweenness.stream('patient-network')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS actor, score AS bridging
ORDER BY score DESC
LIMIT 10
```

## Community Detection
Fidning similar groups

Two common methods - Louvain and Leiden

"What natural groups exist in the network?"

```{code-block} cypher
// The following code creates N number of communityId which composes of X patients within each community

CALL gds.louvain.stream('patient-network')
YIELD nodeId, communityId
RETURN communityId,
  collect(gds.util.asNode(nodeId).name) AS patient,
  count(*) AS size
ORDER BY size DESC
LIMIT 5
```

Louvain tends to create larger communities. 

## Similarity
Finding nodes that look alike.

Node Similarity finds nodes that connect to similar neighbors. Two nodes are similar if they share many of the same connections.

```{code-block} cypher
// Project subgraph for user rating movies
MATCH (u:User)-[r:RATED]->(m:Movie)
WITH gds.graph.project(
  'user-movie',
  u, m,
  {
    sourceNodeLabels: labels(u),
    targetNodeLabels: labels(m),
    relationshipType: type(r)
  },
  {}
) AS g
RETURN g.graphName, g.nodeCount, g.relationshipCount

// Find users who rate common movies
CALL gds.nodeSimilarity.write('user-movie', {
  writeRelationshipType: 'SIMILAR',
  writeProperty: 'score'
})
YIELD nodesCompared, relationshipsWritten
```

## Pathfinding
Pathfinding algorithms find the shortest or best paths between nodes. They’re essential for route optimization and navigation problems.

## Node Embeddings
Embedding algorithms convert nodes into vector representations that capture their structural properties. These vectors can be used for machine learning tasks.

---
**Five types of questions/algorithms in graph DS**

- "Who is important?" → Centrality (Degree, PageRank, Betweenness)
- "What groups exist?" → Community Detection (Louvain, Leiden)
- "Who is similar?" → Similarity (Node Similarity)
- "What’s the best route?" → Pathfinding (Dijkstra, Yen’s)
- "How do I represent structure numerically?" → Embeddings (FastRP)
