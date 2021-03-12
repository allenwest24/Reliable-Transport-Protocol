# Simple-Transport-Porotocol

## High-Level Approach:
- Initial setup and "reconaissance" of the soruce code took us a day or two, and after we were comfortable with what this assignment was going to look like, we started in.
- Our first step was to review how TCP/UDP handled issues on the network for the following conditions:
  - bandwidth
  - latency
  - delay
  - drop
  - reorder
  - duplicate
- After we researched these methods and attended office hours a few times, we decided to structure our packet headers in a way where we could assign sequence numbers, offsets, and store what we have sent and what acks have been recieved on the server side. In thsi way, we thought we could tackle drops, reordering, and duplicate packets first!
- Before starting in on the packet format, however, we tackled the "Program Specifications" portion in the assignment description to make sure it was set to handle the command line inputs correctly. 

## Challenges Faced:
- TBD

## Testing:
- Early testing of the command line parsing was feeding in random amounts of arguments with random values to make sure we were only accepting command line syntax that would enable our programs to run smoothly. We tested number of arguments, types of arguments, and default values on the client-side port selection.
- In order to test the segmentation of packets, we used the following input-building command line call to fill a file we could then feed in through stdin.
  - python -c "print 'A' * 1501" > tester.txt
