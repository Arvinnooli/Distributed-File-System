#Big Data Yet Another Distributed File System (YADFS)

##High Level Architecture
-We have  implemented data nodes and name nodes for storage and management of data across multiple machines.
-Data nodes are responsible to handling data storage whereas name nodes are responsible to store the meta data regarding the data blocks and data nodes.
-To simplify the project we have only 1 name node and assume that the name node service has 100% up time. All the datanodes and the namenode will be in the network.

![image](https://github.com/Arvinnooli/Distributed-File-System/assets/93171827/70baee94-f59a-45f0-9552-cc4f79767e2a)

##Name Nodes
-We have Designed and implemented the Name Node, which manages metadata about files and directories. It maintains a namespace hierarchy, file-to-block mapping, and data about Data Nodes’ health and availability.

check for availability(this is just one way, can be done in multiple other ways):

name node pings each data node periodically.
data nodes acknowledge the name node ping.

##Data Nodes
The data nodes are responsible for handling the reading and writing of data.
Provide an API (not to be confused with rest API, but can be) to write and read operations on data blocks.
Replication to be considered across data nodes.
Replication factor can be taken as 3.
Organizing Data in Data Nodes
Data in form of files needs to be stored within folders. There should be at least one folder called the root folder. New files can be stored within the root folder or in folders created in the root folder. The namespace resolution components help map file and directory names to their physical locations.

The user should be able to view a virtual tree of all folders and files.

###Note on Data Blocks
In a distributed file system, data blocks are used to efficiently store and manage large files across multiple storage nodes (DataNodes). Each file is divided into these fixed-size blocks, and the blocks are distributed across different DataNodes in the cluster. The purpose of using fixed-size blocks is to simplify storage management, data replication, and data retrieval.

For example, let’s say you have a file that has 100 lines and you’ve configured a data block size of 64 lines. This file would be divided into two data blocks: one with 64 lines and another of 36 lines. These blocks are then distributed across different DataNodes in the cluster. The metadata stored by the NameNode keeps track of which DataNodes hold each block and the arrangement of blocks to form the complete file.

##Fault Tolerance
Implement mechanisms to handle DataNode failures. This could involve strategies like maintaining multiple replicas of data blocks, detecting failed nodes, and redistributing data blocks to healthy nodes.

##Client Interaction and Features
Develop a command line interface or a web interface to interact with the distributed file system and perform the following actions:

##Metadata Operations
These operations can be limited to the name node and depends on the precise construction of the virtual tree of directories and files of the DFS.

Create, Delete, Move and Copy Directories and files.
List files and directories within a directory.
Traverse directories.
##DFS Operations
Upload and Download files from the DFS.
Process Flows
Upload Process Flow
Here’s an overview of how the upload process works:

File Splitting: When a client wants to upload a file to the distributed file system, it first divides the file into fixed-size blocks.
Block Creation: After the file is split into blocks, the client assigns a unique identifier to each block. These identifiers, often referred to as block IDs, are used to keep track of the individual data blocks.
Uploading Blocks: The client then initiates the upload process by sending the data blocks to the appropriate data nodes or storage servers in the distributed file system. The client is responsible for distributing these blocks across the network to available data nodes.
Replication: Once the blocks are uploaded to the data nodes, the distributed file system’s replication mechanism comes into play. The system creates additional copies (replicas) of these blocks for fault tolerance and data availability. The data nodes are responsible for creating and managing these replicas.
Metadata Update: While the data blocks are being uploaded, the client also communicates with the metadata servers to update the metadata associated with the file. This metadata includes information about the file’s name, structure, andblock locations. The metadata servers store this information.
Namespace Resolution: The client and the file system coordinate to determine where each block should be stored and which data nodes will host these blocks. The client sends this information to the data nodes.
Client Acknowledgment: The client receives acknowledgments from the data nodes as each block is successfully uploaded. This allows the client to keep track of the progress of the upload.
Download Process Flow
Below is a high-level process flow for downloading a file from the DFS:

##Client Request:
A client application initiates a request to download a specific file from the DFS. The client may provide the file’s path
Metadata Retrieval:
The client interacts with the namenode to retrieve information about the file. This metadata includes details such as the file’s structure, block locations, and replication factor.
Block Location Retrieval:
Based on the metadata obtained, the client learns the locations of the data blocks that make up the file. The file is divided into blocks, and the client retrieves the information about the data nodes (servers) where each block is stored.
Data Block Retrieval:
The client communicates with the data nodes hosting the data blocks it needs to download. It sends requests to these data nodes to retrieve the individual data blocks. This can be done in parallel for multiple blocks to maximize download speed.
Data Transfer:
The data nodes respond to the client’s requests by transferring the data blocks to the client over the network.
Reassembly:
As the data blocks are received, the client reassembles them in the correct order to reconstruct the original file. This is especially important for large files that are split into multiple blocks.
File Completion Check:
Once all the required data blocks are downloaded and the file is reassembled, the client checks if all the data blocks are successfully retrieved.
Cleanup:
After successful download, the client may choose to delete temporary data and close the connection to data nodes, as needed.
NOTE: Incase of datanode failure, it is the namenode’s responsibility to provide the metadata of the replicated blocks to client.

##Metadata Operation Process Flow
Client Request
The client sends a request of the operation to the namenode (example: creating a new directory, etc)
NameNode verifies operation
The namenode verifies weather the operation is valid and either proceeds or terminates the operation (example: Deleting a non-existing file/folder is invalid).
NameNode performs the operation
NameNode makes changes to its metadata for the operation (example: Update the metadata of a new folder being created, etc).
Clients receives a response from the NameNode regarding the status of the operation.
