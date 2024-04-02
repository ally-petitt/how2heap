# Output
```
Welcome to the House of Force

The idea of House of Force is to overwrite the top chunk and let the malloc return an arbitrary value.
The top chunk is a special chunk. Is the last in memory and is the chunk that will be resized when malloc asks for more space from the os.

In the end, we will use this to overwrite a variable at 0x55d0c3d8c020.
Its current value is: This is a string that we want to overwrite.

Let's allocate the first chunk, taking space from the wilderness.
The chunk of 256 bytes has been allocated at 0x55d0c4242250.

Now the heap is composed of two chunks: the one we allocated and the top chunk/wilderness.
Real size (aligned and all that jazz) of our allocated chunk is 280.

Now let's emulate a vulnerability that can overwrite the header of the Top Chunk

The top chunk starts at 0x55d0c4242360

Overwriting the top chunk size with a big value so we can ensure that the malloc will never call mmap.
Old size of top chunk 0x20ca1
New size of top chunk 0xffffffffffffffff

The size of the wilderness is now gigantic. We can allocate anything without malloc() calling mmap.
Next, we will allocate a chunk that will get us right up against the desired region (with an integer
overflow) and will then be able to allocate a chunk right over the desired region.

The value we want to write to at 0x55d0c3d8c020, and the top chunk is at 0x55d0c4242360, so accounting for the header size,
we will malloc 0xffffffffffb49ca0 bytes.
As expected, the new pointer is at the same place as the old top chunk: 0x55d0c4242360

Now, the next chunk we overwrite will point at our target buffer.
malloc(100) => 0x55d0c3d8c020!
Now, we can finally overwrite that value:
... old string: This is a string that we want to overwrite.
... doing strcpy overwrite with "YEAH!!!"...
... new string: YEAH!!!

```
