## Module 1

### Definition:
- collection of independent entities that cooperate to solve a problem that cannot be solved individually 
- collection of computers that do not share common memory or a common physical clock, that communicate by message passing over a communication network.
- a collection of independent computers that appear to the user as a single coherent computer
- Features:
	1. No common physical clock
	2. No shared memory
	3. Geographical separation
	4. autonomy and heterogeneity
### Relation to computer system components:
- each computer in a distributed system has processor and memory, each device is connected to other using a communication network such as WAN or LAN.
- distributed system has a layered approach, each system has a middleware called distributed software that drives the distributed system.
![[image 20250406122722.png]]
- functions/protocols used in middleware layer 
	- CORBA - Common Object Request Broker Architecture
	- RPC - Remote Procedure Call
	- DCOM - Distributed Component Object Model
- MPI - Message Passing Interface - used as an interface for various communication functions
### Motivation
1. **Inherently distributed computation**
		In some applications such as banking the parties that are involved in a transaction may be geographically distant then distributed computing is necessary
2. **Resource Sharing**
		same hardware resource such as a printer can be used by multiple people there by reducing cost
3. **Access to geographically remote data and resources**
		data can be stored at a secure central location and others can request for accessing the data. Supercomputers are located at a location and can be accessed by logging in remotely
4. **Enhanced reliability**
	- distributed resources are not likely to crash/malfunction at the same time under normal circumstances
	- aspects of reliability:
		- availability - resource should be accessible at all times
		- integrity - value/state of data should be correct as it may be accessed by multiple users
		- fault-tolerance - ability to recover from system failures
5. **Increased performance / cost ratio**
	- a single task can be broken down and be executed parallelly across multiple devices
6. **scalability**
	- processors are connected by WAN so we can easily add more processors without bottleneck on the communication network
7. **modularity and incremental expandability**
	- different types of processors can be added as long as they are running the same middleware algorithms
	- processors can be easily replaced with other processors
### Primitives for distributed communication
- *send()* - message send primitive - has two parameters
	- destination to which data is to be send
	- buffer in the user space containing the data to be sent
- *receive()* - message receive primitive - has two parameters
	- source from which that data is to be received
	- user buffer into which data is to be received
- two ways to send data
	- buffered option - copies the data from the user buffer to the kernel buffer, then the data is copied onto the network
	- unbuffered option - data gets copied directly from user buffer to the network
- only one way to receive data - buffered option - data might have already arrived before the receive primitive was invoked and needs a place to be stored in the kernel

- **synchronous primitives**
	- both send() and receive() handshake with each other
	- send() is complete only after the corresponding receive() primitive has been invoked and the receive operation has been completed.
	- receive() is complete when the data to be received is copied on to the receiver's user buffer
- **Asynchronous primitives**
	- send() is said to be asynchronous if control returns back to the invoking function after the data is copied out of the user-specified buffer
	- it is not checked whether the data was received or not
- **Blocking primitives**
	- control returns back to the invoking process after the processing for the primitive is complete
- **Non-Blocking primitives**
	- control returns back to the invoking process immediately after invocation, even if the operation is not complete
	- eg: non-blocking send - control returns to the process before the data is copied out of the user buffer
	- eg: non-blocking receive - control returns to the process even before the data may have arrived from the sender
#### how wait() and handle is used:
-  a return parameter on the primitive call returns a system generated *handle* that can be used to check the status  of completion of the call
- the process that invoked the non blocking primitive can check the status of the completion of the primitive in two ways
- periodically (loop) checking if the handle has been flagged or posted
- it can issue of *wait()* with the handle as a parameter, *wait()* is always blocking(read blocking primitive mentioned above)
- in good programming practices after a process issues a non-blocking primitive it does other actions and at last a *wait()* call is issued at end
![[Pasted image 20250408154935.png]]
- when *wait()* is issued and if processing for the primitive is complete then *wait* returns immediately else if the process is not complete then *wait* blocks and waits for a wake up signal
- when the process is complete then the communication subsystem software sets the value of handle and wakes up any process with *wait* blocked with that handle, this is called posting the completion of the operation
- send primitive has four versions
	- synchronous blocking
	- synchronous non-blocking
	- asynchronous blocking
	- asynchronous non-blocking
- receive primitive has two versions
	- blocking synchronous 
	- non-blocking synchronous
#### Blocking synchronous send
- data gets copied from user buffer to kernel buffer, then transmitted through the network
- after data is copied to the receiver's buffer and receive call is complete an acknowledgement is send back to the sender
- control is returned back to the process that invoked send and send is complete
- ![[Pasted image 20250409141919.png]]
#### Non-blocking synchronous send
- control returns back to the invoking process as soon as the data is copied out from the user buffer to the kernel buffer
- a parameter of the non-blocking call also gets set with the handle of a location which can be used to check the completion of the send operation
- the location gets poster after an acknowledgement returns from the receiver
- the process can check for completion using the handle by checking in a loop or it can invoke a blocking wait operation on the returned handle
- ![[Pasted image 20250409143359.png]]
#### Blocking asynchronous send
-  the user process that invokes the send is blocked until the data is copied from the user's buffer to the kernel buffer
- if send is unbuffered then send is blocked until the data is copied from the user's buffer to the network
- ![[Pasted image 20250409145422.png]]
#### Non-Blocking asynchronous send
- the user process that invoked the send is blocked until the transfer from the user's buffer to the kernel buffer is initiated (if unbuffered then from user's buffer to networrk)
- a parameter in the non-blocking call also gets set with the handle of a location which can be later checked using wait for completion of asynchronous send
- asynchronous send is complete when data is copied out of user's buffer
- checking is necessary if buffer needs to be reused
- ![[Pasted image 20250409150228.png]]
#### Blocking Receive
- receive call blocks until the data arrives and is written in the specified user buffer then control returns to the process
- ![[Pasted image 20250409141919.png]]
#### Non-blocking Receive
- 