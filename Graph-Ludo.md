

# Graph API

**Index method**
Method: ```void index(String indexName, Node nodeToIndex, String flatKeyAttributes, Callback<Boolean> callback)```
This method allows to add a node in a global index, identified by its name. The index is created on the fly if it does not exist. Indexed nodes are referenced by a set of their attributes, named keys attributes. After, you can retrieve them with ```find``` methods using the index name and the keys attributes. We reference these indexes as **global index**: they are only accessible from graph, contrary to **local indexes** that are only accessible from node (see Node API documentation to get more information)
They represented the entry point of the graph. That means that when you have a graph instance, the only nodes that you can directly access (i.e. without traversing a relation) are the indexed nodes. **WARNING** It also means that a node without index **AND** without path its and a indexed node could be retrieve **ONLY** with its id.

**Parameters**
* indexName:  A string uniquely identifying the index in the graph.
  - Constraint: Should not be null
* nodeToIndex: The node to add in the index
  - Constraint: Should not be null
* flatKeyAttributes: The set of attributes used as keys to index the node, given as a flat string separated by ','. The order does not matter.
  - Constraint: Should not be null
* callback: Called when the indexing is done. The parameter specifies whether or not the indexing has succeeded.

**Example**
``` java
[...]
//Index a root node with two parameters
Node root = graph.newNode(0,0);
root.set("name","root");
root.set("id",1);
root.set("value",5);

graph.index("indexName",root,"name,id",null);

//Possible, but not recommended (see below)
Node root2 = graph.newNode(0,0);

graph.index("indexName",root2,"name,id",null);

root2.set("name","root2");
root2.set("id",2);
root2.set("value",5874);
[...]
```

**WARNING** Currently, it is possible to index a node before setting its attributes and it is possible to change the attributes used as keys for indexed nodes. However, it is not recommended to do this. If you have to modify theses kind of attribute, you should unindex the node before, as show in the following example.

``` java
[...]
// Change the keys attribute of an indexed node
Node root = graph.newNode(0,0);
root.set("name","root");
root.set("id",1);
root.set("value",5);

graph.index("indexName", root, "name,id", new Callback<Boolean>() {
    @Override
    public void on(Boolean result) {
      graph.unindex("indexName", root, "name,id", new Callback<Boolean>() {
          @Override
          public void on(Boolean result) {
              root.set("id",10);
              graph.index("indexName", root, "name,id",null);
          }
      });
    }
});
[...]
```

**Unindex method**
Method ```void unindex(String indexName, Node nodeToUnindex, String flatKeyAttributes, Callback<Boolean> callback);```
This method allows to remove a node from a global index, identified by its name.
**WARNING** If the node has not a path to an indexed node, the **ONLY** way to reach it is to store its id.

** Parameter**
* indexName: A string uniquely identifying the index in the graph.
* nodeToUnindex: The node to remove from the index.
* flatKeyAttributes: The set of attributes used as keys to index the node, given as a flat string separated by ','. The order does not matter.
* callback: Called when the unindexing is done. The parameter specifies whether or not the unindexing has succeeded.

**Example**
``` java
[...]
//Index a root node with two parameters
Node root = graph.newNode(0,0);
root.set("name","root");
root.set("id",1);
root.set("value",5);

graph.unindex("indexName",root,"name,id",null);
[...]
```
