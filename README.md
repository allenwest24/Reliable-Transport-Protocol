# Simple-Transport-Porotocol

## High-Level Approach:
- Initial setup and "reconaissance" of the source code took us a day or two, and after we were comfortable with what this assignment was going to look like, we started in.
- Our first step was to review how TCP/UDP handled issues on the network for the following conditions:
  - bandwidth
  - latency
  - delay
  - drop
  - reorder
  - duplicate
- After we researched these methods and attended office hours a few times, we decided to structure our packet headers in a way where we could assign sequence numbers, offsets, and store what we have sent and what acks have been recieved on the server side. In this way, we thought we could tackle drops, reordering, and duplicate packets first!
- Before starting in on the packet format, however, we tackled the "Program Specifications" portion in the assignment description to make sure it was set to handle the command line inputs correctly. 
- TODO - Isabel: Discuss process for implementing packet ordering on the receiver side.

## Challenges Faced:
- The first major challenge we faced was in our initial attempts to use ./nettest and ./testall. It kept erroring out and through trial and error we figured out that our overall packet and data size was too large. We ended up having to decrease the DATA_SIZE constant in order to get it to work. We are considering at this point whether to abandon using json.
- Another major challenge was getting the last basic test case to pass with bandwidth of .1Mb/s and latency of 500ms. Since the size of hte packets were so large, we needed to figure out how to compress the data. We attempted to use the zlib library for compressing and decompressing, however we kept getting an error about incorrect header check. We asked a total of three teaching assistants, including the head teaching assistant, and no one was able to reproduce the error, or figure out why it was occuring. To handle large packet sizes, we pivoted towards using structs instead of JSON. We used the pack() and unpack() methods for the structs, which ideally would handle compression. This didn't seem to pass the last test case though, and introduced additional problems. 
- A main challenge was getting tests to pass using ./testall. The nettest and testall scripts seemed to be really buggy. We'd try the scripts using a seperate file with changes, and the tests woudl all pass. When we tried copying over the contents to the "main" files (3700send, 3700recv), the tests in testall would fail, even though the content of the two files were the exact same. The testing scripts were very difficult to work with.

## Testing:
- Early testing of the command line parsing was feeding in random amounts of arguments with random values to make sure we were only accepting command line syntax that would enable our programs to run smoothly. We tested number of arguments, types of arguments, and default values on the client-side port selection.
- In order to test the segmentation of packets, we used the following input-building command line call to fill a file we could then feed in through stdin.
  - python -c "print 'A' * 1501" > tester.txt
- In later testing where we were more concerned about testing we used the following script in order to generate an input that would be easier to detect if the order was correct.
  - python -c "print 'A' * 1000 + 'B' * 1000 + 'C' * 1000 + 'D' * 1000 + 'E' * 1000 + 'F' * 1000 + 'G' * 1000 + 'H' * 1000 + 'I' * 1000 + 'J' * 1000" > tester.txt
