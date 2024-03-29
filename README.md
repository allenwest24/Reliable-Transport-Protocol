# Reliable-Transport-Porotocol

## Description:
We designed a simple transport protocol that provides reliable datagram service. This protocol ensures data is delivered in order, without duplicates, missing data, or errors. Since the local area networks at Northeastern are far too reliable to be interesting, we utilized scripts to set up a vagrant image to emulate an unreliable network.

## High-Level Approach:
- Our first step was to review how TCP/UDP handled issues on the network for the following conditions:
  - bandwidth
  - latency
  - delay
  - drop
  - reorder
  - duplicate
- After we researched these methods and attended office hours a few times, we decided to structure our packet headers in a way where we could assign sequence numbers, offsets, and store what we have sent and what acks have been recieved on the server side. In this way, we thought we could tackle drops, reordering, and duplicate packets first!
- In order to address packet re-ordering on the recv side of things, we used a dict sturcture with the key value of the squence number. This also eliminated duplicate packets because the same key would result in a single entry instead of multiple for the same sequence number. Then, when printing out the packet to sys.stdout, we reordered the stroed packets recvd.
- We fixed a bug we were having with hanging programs because it was possible that if packets were dropping, the recv would not get the EOF message. To resolve this we added a custpom ack to ensure this was recieved. 
- We also tried to increase efficiency by two techniques successfully, but due to problems we will discuss in the challenges portion could not include them in the final solution of 3700send. These two enhancements were switching from JSON to structs (as seen in the struct-alt-send and struct-alt-recv) as well as implementing the sliding window to send multiuple packets at once (as seen in the sliding-window-alternate file.)

## Challenges Faced:
- Another major challenge was getting the last basic test case to pass with bandwidth of .1Mb/s and latency of 500ms. Since the size of hte packets were so large, we needed to figure out how to compress the data. We attempted to use the zlib library for compressing and decompressing, however we kept getting an error about incorrect header check. We asked a total of three teaching assistants, including the head teaching assistant, and no one was able to reproduce the error, or figure out why it was occuring. To handle large packet sizes, we pivoted towards using structs instead of JSON. We used the pack() and unpack() methods for the structs, which ideally would handle compression. This didn't seem to pass the last test case though, and introduced additional problems. 
- A main challenge was getting tests to pass using ./testall. The nettest and testall scripts seemed to be really buggy. We'd try the scripts using a seperate file with changes, and the tests woudl all pass. When we tried copying over the contents to the "main" files (3700send, 3700recv), the tests in testall would fail, even though the content of the two files were the exact same. The testing scripts were very difficult to work with. 
- The following three bullets were designs we were able to get to work, but not when renamed to 3700send or 3700recv.
  - SLIDING WINDOW: With sliding window we would send a certain number of packets before checking to see if we had recieved any acks back. This saved .2 seconds every time we didn't recieve an ack back. This was implemented and worked, but not when renamed to 3700send.
  - COMPRESSION: The goal here was to send less, and decompress on the recv side. We implemented this well but it would not work with testall. Many different TAs could not figure the problem out.
  - STRUCT USAGE: Instead of using JSON we used python structs to format our messages, and decreased the size of the header to 10 bytes. This allowed us to drastically increase our message size from 500 to 1400+. This, again, worked and increased performance by a lot, but once implemented with the designated file name and testall provided did not function. TAs could not assist here either.

## Testing:
- Early testing of the command line parsing was feeding in random amounts of arguments with random values to make sure we were only accepting command line syntax that would enable our programs to run smoothly. We tested number of arguments, types of arguments, and default values on the client-side port selection.
- In order to test the segmentation of packets, we used the following input-building command line call to fill a file we could then feed in through stdin.
  - python -c "print 'A' * 1501" > tester.txt
- In later testing where we were more concerned about testing we used the following script in order to generate an input that would be easier to detect if the order was correct.
  - python -c "print 'A' * 1000 + 'B' * 1000 + 'C' * 1000 + 'D' * 1000 + 'E' * 1000 + 'F' * 1000 + 'G' * 1000 + 'H' * 1000 + 'I' * 1000 + 'J' * 1000" > tester.txt
- In addition to the custom tests we wrote, we also made heavy use of netsim and nettest to ensure we were getting the proper results or trying to simulate the tests in testall with netsim and running the corresponding size input with nettest.

