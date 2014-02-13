Design project 2014 - Demoustification de la zone alluviale de la Broc
=====================================================================

How to launch the demoustification model
---------------------------------------
In matlab run the `distributed_network.m` file for desired N (node), K (connectivity),
 R (communication radius), F (simultaneously broadcasting nodes), noise and 
 M (number of particles).
 
How to launch the different parameter sets
-----------------------------------------
Use the 4 different scripts: `test_paramsS1.m`, `test_paramsS2.m`,`test_paramsS3.m`,
and `test_paramsS4.m`. 
The number of particle can be change in the `distributed_network_funct.m` file.

Important files
===============

Only some files are really *interesting* from an algorithmic point of view. Note that
in the descriptions N is the number of nodes and M 

* `f_compute_messages.m` contains the logic to take a node's state and a distance
  measurement to compute the messages that need to be sent to the other nodes
  
  Input:
  * `id`: The node's ID
  * `mmnts`: N x 1, contains measurements between another node and this node. NaN if
    there is no measurement in this round.
  * `messages`: N x 1
  * `data`: struct containing the node's memory:
    * `pos`: 1 x 2
    * `means`: M x 2
	* `weights`: M x 1
	* `variance`: 1 x 1
	* `isAnchor`: 1 x 1
	* `neighborPrevious`: N x 1, list of last received samples of all neighbors:
      * `means`: M x 2
      * `weights`: M x 1
      * `variance`: 1 x 1
  
  Output:
  * `messages`: N x 1, outgoing message with the following contents:
    * `means`: M x 2
	* `weights`: M x 1
	* `variance`: 1 x 1

* `f_node_update.m` contains the logic to take messages and update a node's state.
  Cleans data for and calls `f_sample` and `f_estimate_location`.
  
  Input:
  * `id`: The node's ID
  * `messages`: N x 1
  * `data`: struct containing the node's memory:
    * `means`: M x 2
	* `weights`: M x 1
	* `variance`: 1 x 1
	* `isAnchor`: 1 x 1
	* `neighborPrevious`: N x 1, list of last received samples of all neighbors:
      * `means`: M x 2
      * `weights`: M x 1
      * `variance`: 1 x 1
  
  Output:
  * `data`: 1 x 1, node's state (see `f_compute_messages.m` for a full definition).

* `f_sample.m` contains a function that takes a certain number of messages
  from neighboring nodes and executes a sampling on these. Note: All elements of
  the messages list are _guaranteed_ to be non-empty (if there are N nodes in the
  graph, only the n neighbors will be in the list).
  
  Input:
  * `messages`: N x 1, a list of the messages of cell type and containing structs with
    the following fields:
    * `means`: M x 2
    * `weights`: M x 1
    * `variance`: 1 x 1
  
  Output:
  * `new_samples`: M x 2
  * `new_weights`: M x 1

* `f_estimate_location.m` contains a function that takes a sampling (the "memory" of
  the nodes) and returns a single position.
  
  Input:
  * `samples`: M x 2
  * `weights`: M x 1
  
  Output:
  * `loc`: M x 1
  