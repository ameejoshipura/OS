#Memory Allocator
COMP 310 Assignment 4
Amee Joshipura
Id: 260461226

An implementation of malloc and free. It uses the boundary tag scheme and each block of free or allocated memory on the heap is refered to as a node. Each node has the following format (10 bytes of metadata):

[ 1 byte | 4 bytes | Data bytes | 4 bytes | 1 byte ] 
When printing a node, the program output will omit the data bytes

The first and last bytes of the node are the tags. They indicate if the node is allocated (1) or free (0). 
The next 4 bytes on either side of the data bytes indicate the how many data bytes there are in the node. The program assumes that at least one malloc will be called before a free. 
An additional 5120 bytes of free space are added to the top of the heap every time sbrk() is called to minimize the number of sbrk() system calls. When malloc actually returns, the pointer is adjusted to point to the data bytes. This is because the implementation works with pointers to the beginning of a node including the metadata.

##Malloc
The malloc implementation uses either first fit or best fit algorithms to find a free node in the heap. 
The first fit algorithm searches from the bottom of the heap to the top, looking for a free node that is large enough to fit the requested size. There are two cases:
1. The free block can be split into an allocated node of the requested size and a free node of the remaining size
2. The whole free block can be overwritten as allocated. 
In the former case, when trying to split would result in a free node of useless size (i.e. <10 bytes).

The best fit algorithm also loops through the nodes from the bottom of the heap to the top. As soon as it finds a free block large enough it stores a pointer to it. It continues searching and if it finds a smaller block that is big enough to fit the requested size, it stores that as an even better best fit pointer. Once finished searching, the first fit algorithm is called to start searching at the best fit pointer. The first fit algorithm will return a result at the first node it checks, and in this way the code is reused and when the first fit algorithm is called, it returns the result almost immediately.

##Free
Free checks the adjacent nodes to see if they are free as well or not. If there are adjacent free nodes it merges with them, to form one large free node. After the merge the data bytes may contain junk. If there are no adjacent free nodes, the tag bytes are simply overwritten to zero.

##Testing
1. The test file first generates an array of random malloc sizes.
2. Malloc is called with those sizes sequentially using the best fit algorithm, printing each node at allocation and after allocation.
3. Free is called on all those mallocs, printing each node as it is freed and after they have all been freed.
4. The heap size and breakdown of the heap (#bytes allocated, free, metadata) is printed.
5. Repeat (Malloc - free - heap size and breakdown) with first fit algorithm.
6. Check that each malloc/free/heap breakdown call matches the printed nodes. 
7. Check that the best fit exectues correctly - it finds the smallest block that can fit the request.
