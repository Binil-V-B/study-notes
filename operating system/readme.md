## Module 1
- operating system act as an interface between user and computer
- software - set of programs
	- application software
	- system software
#### Functions of OS:
1. Memory Management
2. processor management
3. device management
4. file management
5. security and protection
6. job accounting
7. error detection
#### types of OS:
1. batch OS
		- users prepare the job and submit it to the computer operator
		- computer operator sort the program into batches
2. multiprogrammed system
		- increase CPU utilization by organizing jobs
		- os keep several jobs in memory simultaneously
		- when a process is waiting for something, the os switches to another process
		- **job scheduling** : several jobs are ready to run, the os chooses one to execute
3. multiprocessor system
		- more than one processor sharing the computer bus, clock, memory etc
		- more immune to failure because if one processor fails then there is another processor
4. Time sharing system
		- CPU executes multiple jobs by switching among them
		- uses can interact with each program while it executes
		- each user is given a time slice for executing his job, job continues until time slice ends.
5. Real time systems
		- processing must be done within the defined constraint or the system will fail
		- two types:
			- hard real time - 
			- soft real time - a critical real-time task get priority over other tasks and retain its priority until it is complete
6. Distributed OS
		- multiple central processors sever multiple users
		- user at one site can access resources at another site
#### Operating system services:
1. **Program execution**: system must be able to load, run and end the execution of a program
2. **IO operation**: programs need to have IO functionalities
3. **file system manipulation**: program need to create, read and write files
4. **communication**: processes can communicate with each other
5. **error detection**
6. **resource allocation**
7. **accounting**: keep track of resources and users
8. **protection**
#### System call
- interface to access services of an OS
- programs can execute in two ways:
	- user mode - program does not have direct access to memory
	- kernel mode - has direct access to memory
- **context switching** - switching program from user mode to kernel mode if the program want to access resources or perform a privileged operation
- programs use system calls to request services from the kernel of the OS
- types of system calls
	1. **process control**: end, abort, load, execute, wait
	2. **file manipulation**: create, delete, open, write, read
	3. **device manipulation**: 
	4. **information maintenance**: 
	5. **communication**:
#### Operating system structure
- the way in which components are interconnected into a kernel
1. simple structure: 
	- does not have a well defined structure
	- programs have direct access to hardware 
	- malicious programs can affect the computer
	- less complex design, very little space requirements
	- if application program crash whole system crash
2. layered approach:
	- each layer is built on top of lower layer
	- layer 0 - hardware, layer n - user interface
	- lower layers have routines that can be invoked by upper layers
	- layers help hide certain implementations and hardware from higher layers
3. Microkernels:
	- remove all non essential components from the kernel and implement them as system and user level programs
	- results is a smaller kernel - micro kernel
	- if a program want to access file then it informs the micro kernel by message passing and micro kernel communicates with the file server
	- provide more security as services are running as user rather than kernel processes
4. Modules:
	- core kernel only have core functionalities and other functionalities are present in the form of modules and will be loaded into kernel either at boot or at run time.
	- more flexible than layered modules can other modules directly through kernel
	- message passing is not needed like in micro kernel
#### System Boot
- process of loading the kernel is called booting the system
- bootstrap program in the rom is loaded into the memory
- bootstrap program searches the file system to find the OS kernel
- load the Kernel into memory and start its execution

## Module 2