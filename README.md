Memory Allocator
COMP 310 Assignment 4

An implementation of the Standard C Library Functions malloc and free. It uses the boundary tag scheme and each block of free or allocated memory on the heap is refered to as a node. Each node has the following format (10 bytes of metadata):

[ 1 byte | 4 bytes | Data bytes | 4 bytes | 1 byte ] //Note that the program output will omit the data bytes when printing a node

The first and last bytes of the node are the tags. They indicate if the node is allocated (1) or free (0). The next 4 bytes on either side of the data bytes indicate the how many data bytes there are in the node. The program assumes that at least one malloc will be called before a free. To minimize the number of sbrk() system calls, an additional 5120 bytes of free space are added to the top of the heap every time sbrk() is called. Note that the implementation works with pointers to the beginning of a node including the metadata, so when malloc actually returns, the pointer is adjusted to point to the data bytes.

##Malloc
The malloc implementation uses either first fit or best fit algorithms to find a free node in the heap. The first fit algorithm searches from the bottom of the heap to the top, looking for a free node that is large enough to fit the request. There are two cases, either the free block can be split into an allocated node of the requested size and a free node of the remaining size, or the whole free block can be overwritten as allocated. The latter case would occur when trying to split would result in a free node of useless size (i.e. <10 bytes).

The best fit algorithm also loops through the nodes from the bottom of the heap to the top. As soon as it finds a free block large enough it stores a pointer to it. Then it continues searching and if it finds a smaller block that also is larger enough it stores that as an even better best fit pointer. Once finished looping it calls the first fit algorithm with a signal to start searching at the best fit pointer. This way the code is reused and the first fit algorithm will return a result at the first node it checks.

Free
Free checks the adjacent nodes to see if they are free as well or not. If there are adjacent free nodes it merges with them, meaning that contiguous free nodes are overwritten as one large free node. Note that after the merge the data bytes may contain junk. If there are no adjacent free nodes, the tag bytes are simply overwritten to zero.

Testing
The test file first generates an array of random malloc sizes.
Malloc function is called with those sizes sequentially using the best fit algorithm, printing each node as it is allocated and after they have all been allocated.
Free is called on all those mallocs, printing each node as it is freed and after they have all been freed.
The heap size and breakdown of the heap (#bytes allocated, free, metadata) is printed.
Repeat with first fit algorithm.
Output was analyzed to ensure proper functionality. Particularly that each malloc/free/heap breakdown call matches the printed nodes. Also important to check that the best fit is functioning correctly, i.e. the node used for the malloc should be the smallest one that can fit it.
