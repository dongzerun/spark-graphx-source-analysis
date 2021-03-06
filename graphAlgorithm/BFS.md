# 广度优先遍历

```scala
val graph = GraphLoader.edgeListFile(sc, "graphx/data/test_graph.txt")

val root: VertexId = 1
val initialGraph = graph.mapVertices((id, _) => if (id == root) 0.0 else
Double.PositiveInfinity)

val vprog = { (id: VertexId, attr: Double, msg: Double) => math.min(attr,msg) }

val sendMessage = { (triplet: EdgeTriplet[Double, Int]) =>
		var iter:Iterator[(VertexId, Double)] = Iterator.empty
		val isSrcMarked = triplet.srcAttr != Double.PositiveInfinity
		val isDstMarked = triplet.dstAttr != Double.PositiveInfinity
		if(!(isSrcMarked && isDstMarked)){
		   	if(isSrcMarked){
				iter = Iterator((triplet.dstId,triplet.srcAttr+1))
	  		}else{
				iter = Iterator((triplet.srcId,triplet.dstAttr+1))
	   		}
		}
		iter
}

val reduceMessage = { (a: Double, b: Double) => math.min(a,b) }

val bfs = initialGraph.pregel(Double.PositiveInfinity, 20)(vprog, sendMessage, reduceMessage)

println(bfs.vertices.collect.mkString("\n"))
```