## Module 1
### Authentication Protocols:
- **user authentication** - process of determining whether some user or some application or process acting on behalf of a user is in fact who or what it declares itself to be.
- authentication is used so that a software is accessed by only authorized users
- **message authentication** - process of verifying if the contents of a message have not been altered and the source is authentic
#### NIST model for electronic user authentication
- NIST SP 800-63 defines a general model for user authentication 
![model diagram](/images/20250409215407.png)

- Three important concepts used in this model:
	1. **Digital Identity** - unique representation of a subject engaged in an online transaction
	2. **Identity Proofing** - Establish that a subject is who they claim to be, it done be collecting, validating and verifying info(authenticators) about a person
	3. **digital authentication** - the process of determining the validity of one or more authenticators used to claim a digital identity
- six entities defined in the figure:
	1. **CSP** - Credential service provider:
		- trusted entity that issues or registers subscriber authenticators
		- issues digital credentials to subscribers
	2. **verifier** 
		- verify the claimant's identity by verifying the claimant's possession and control of one or two authenticators using an authentication protocol
	3. **Relying Party**
		- an entity that relies on a subscriber's authenticators or a verifiers assertion of a claimant's identity
	4. **Applicant**
		- subject undergoing the process of enrollment and identity proofing
	5. **Claimant**
		- A subject whose identity is to be verified
	6. **Subscriber**
		- A party who has received a credential or authenticator from a CSP
##### Simple working

---

### Mutual Authentication
- enable communicating parties to satisfy themselves mutually about each other's identity and to exchange session keys
- problems with authenticated key exchange - confidentiality, timeliness
- to prevent a key from being exposed it must be communicated in encrypted form.
- timeliness is important to prevent replay attacks
- replay attacks can be prevented by using sequence numbers but it is very hard to keep track of sequence numbers
- two general approaches:
	- **timestamp**: party A accepts a message from B only if the timestamp send along with the message in A's judgement is close enough to the current time, both parties clocks need to be synchronized
	- **challenge/response**: if A is expecting a message from B then A will first send a nonce(challenge) to B then the message from B should contain the correct nonce value
- problems with timestamp approach:
	- should not be used for connection oriented applications
	- some protocol is needed to maintain the synchronization of processor clocks
	- it is hard to maintain synchronization due to unpredictable nature of networks
	- attacks are possible if clocks are out of sync
- problems with challenge approach:
	- unsuitable for connectionless type of application, because a handshake is needed before transmission
#### Mutual authentication using symmetric encryption
things to consider
- There is a trusted Key Distribution Center, Everyone shares a secret key with the KDC
- KDC is responsible for the generation and distribution of session keys using the secret keys

1. Needham and Schroeder - NEED78
	- ![[Pasted image 20250410224408.png]]
	- the purpose of the protocol is to distribute a session key K<sub>s</sub>
	- A sends a message to KDC saying that A wants to communicate with B and also sends a nonce
	- KDC reply with message that is encrypted with K<sub>a</sub> and it contain K<sub>s</sub>, ID<sub>B</sub>, nonce and an encrypted part(ticket) that will be forwarded to B
	- A forward the ticket to B
	- now B gets the K<sub>s</sub> from the ticket and sends A a new nonce encrypted with K<sub>s</sub>
	- A responds with a  message with the appropriate nonce
	- drawbacks:
		- if an opponent x gets an old session key and is able to replay step 3 and trick B into using an old session key
		- If message at step 4 is intercepted then it can impersonate A's response in step 5
2. Denning - DENN81,DENN82
	- modify NEED78 by adding timestamp to step 2 and step 3
	- ![[Pasted image 20250415074848.png]]
	- T is a timestamp and it assures that the session key has only just been generated
	- an opponent cannot trick B into using an old session key by replay of step 3 as it will detected by B as untimely
	- drawbacks:
		- suppress-replay attacks are possible: If the clocks are not synchronized and if the the senders clock is ahead of the receiver then attacker can replay the message later to cause unexpected results
	- we can prevent suppress-replay attacks by enforcing the parties to check their clocks regularly against the KDC's clock
	- we can also rely on handshaking using nonce to overcome the need for clock synchronization, nonce method is not vulnerable to suppress-replay as the nonce the recipient will choose is unpredictable
3. KEHN92  NEUM93a
	- ![[Pasted image 20250415080422.png]]
	- in step 3 A receives a ticket that can be forwarded to B
	- in step 3 A receives back the nonce it send to B which ensure that it is a timely message
	- in step 4 B receives the session key from the ticket and the nonce is encrypted using the session key signify the message is from A
	- The protocol leaves A in possession of a key that can be used for subsequent authentication to B, avoiding the need to contact the KDC repeatedly.
	- the following code show how a session is established with a pre existing key
	- ![[Pasted image 20250415110654.png]]
	- in step 1 B checks whether the ticket is still valid using the time stamp T<sub>b</sub>
	- the newly generated N'<sub>a</sub> ,N'<sub>b</sub> ensure that this is not a replay attack
	- here no clock synchronization is necessary as only B's clock is used
### Kerberos
- It is an authentication service that is used to authenticate users and servers that are in an open network
- we cannot trust a workstation to verify the identity of its user
- If a users are not authenticated then the following problems can occur:
	- if a user can gain access to other person's workstation then he can pretend to be that user from his workstation
	- user can change the network address and impersonate another user
	- can eavesdrop on message exchanges and disrupt operations
- Kerberos provides a centralized authentication server.
- Kerberos relies on symmetric encryption
#### Motivation to create Kerberos:
- how to enforce security on a distribute system
	- individual workstations verify the identity of users and servers enforce security policy based on user identification
	- client system authenticate with the server but trust the client system concerning the identity of its user
	- user need to prove his/her identity for each service invoked and the servers also need to prove their identity to clients
- Kerberos support the third mentioned method
- requirements of Kerberos
	- secure: a potential opponent should not be able to find Kerberos as a weak link
	- reliable: Kerberos should be reliable and should employ a back up system
	- transparent: user should not be aware that authentication is taking place beyond the requirement to enter a password
	- scalable: should support large number of clients and servers
- Kerberos works on a protocol based on NEED78
#### Kerberos version 4
##### A simple Authentication Dialogue:
- open network - any client can request service from any server on the network
- authentication is important to prevent impersonation
- if each server handle authentication then immense load on server - an alternative - use an authentication server that knows the passwords of all users and stores them in a centralized database
- authentication server also share unique key will each server 
- below given is the implementation:
- ![[Pasted image 20250415141332.png]]
	- C -> Client
	- AS -> Authentication Server
	- V -> Server
	- ID<sub>c</sub> -> ID of client 
	-  ID<sub>v</sub> ->ID of server
	- P<sub>c</sub> -> Password of client
	- K<sub>v</sub> -> secret key shared between AS and V
- **working**
	- When the user log onto a workstation and want to request a service from server V then the client module C collects the password from user and sends a message to AS including the ID of client, ID of server whose service is needed
	- AS verifies if the ID and password of client match and if the client is permitted to access the server if both conditions pass then a ticket is issued
	- ticket contain the ID of client who requested the service, the network ID from which the request was send and ID of server. All this is encrypted using K<sub>v</sub> (secret key between AS and V). It cannot be altered by anyone
	- Client forwards the ticket to V along with ID<sub>c</sub> 
	- Server V decrypt the ticket and check if ID<sub>c</sub> send along with the ticket matches what is within the ticket then the service is granted
	- security features:
		- the ticket is encrypted so cannot be modified
		- server's ID is included in the ticket which can be used to check if the ticket is decrypted properly
		- ID<sub>c</sub> is included in the ticket to signify to whom the ticket was issued to 
		- an opponent could capture the ticket in message(2) and ID<sub>c</sub> can be send to a message of the form of (3) from another workstation so AS includes the network address from which the original request was send (AD<sub>c</sub>) in the ticket to prevent attack
##### A more secure Authentication Dialogue:
- Problems form previous approach
	- from previous approach if a user want to access the same service multiple times or want to access different services the each time a new ticket needs to be made and user need to enter password each time
	- password is transmitted in plain text
- to solve this problem a new method and a new server is made
- TGS is introduced, it grants ticket to users who are authenticated by the authentication server
- ![[Pasted image 20250415151329.png]]
- ![[Pasted image 20250415151338.png]]
- **working**
	- First user request a ticket granting ticket to AS by sending it's ID (ID<sub>c</sub>) and ID of tgs to AS
	- AS generates a ticket granting ticket (Ticket<sub>tgs</sub>) and encrypt it with the password(K<sub>c</sub>) of the user which is know to AS
	- When the user enters the correct password he will get access to the Ticket<sub>tgs</sub> 
	- When the user wants to access a service then the client sends the ID of user, ID of server who's service is needed and also the Ticket<sub>tgs</sub> for authentication
	- TGS decrypt Ticket<sub>tgs</sub> and check the success of decryption by comparing ID<sub>tgs</sub> with the one present on the ticket, make sure the lifetime has not expired, compare user ID and network ID in the ticket to the current one, check if the user is permitted access to this server, then TGS issues a service-granting ticket
	- service granting ticket is encrypted using a key only know to TGS and the Server
	- The user forward the service-granting ticket and his user ID to the server, the server verifies and provides the service to the user
- **features**:
	- in step 2 the ticket is encrypted using the user's password and therefore it can only be accessed by the correct user - authentication of user
	- the ticket contains the ID of client and network address of client to prevent other users from impersonating using replay attacks
	- the ticket also has a timestamp and lifetime to prevent replayed attacks
##### Version 4 Authentication Dialogue
- problem to overcome:
	- if lifetime of ticket is small then user need to enter password more frequently
	- if lifetime of ticket is big then an actor can eavesdrop into the network capture the ticket-granting ticket or the service-granting ticket, wait for the user to logout then forge the legitimate user's network address and use it access any service available to that user
	- a service must be able to prove that the person using a ticket is the person to whom the ticket was issued to 
	- servers need to be authenticated to the user
- ![[Pasted image 20250415233003.png]]
- **working**:
	- client request authentication server for _Ticket<sub>tgs</sub>_ by sending ID of client, ID of tgs, and a timestamp
	- AS responds with a message that is encrypted using the password of the user, the message contains - ticket, timestamp, lifetime, ID<sub>tgs</sub> to confirm that the ticket is for TGS and also a session key (K<sub>c,tgs</sub>) 
	- the session key can only be accessed by the client as it is encrypted using the client's password. The session key is delivered to TGS by Ticket<sub>tgs</sub>, that ticket is encrypted by a key shared between AS and TGS.
	- In step 3 the client sends to TGS, the id of the service needed (ID<sub>v</sub>), Ticket<sub>tgs</sub>, and Authenticator
	- Authenticator contains ID and address of C and also timestamp, the authenticator is encrypted using the secret session key, authenticator is used to prove the client's identity
	- TGS decrypt the Ticket<sub>tgs</sub> to get the secret session key (K<sub>c,tgs</sub>) and uses it to decrypt the authenticator and check if the details in the authenticator match with the current client and can then confirm that the sender of the client is indeed the ticket's real owner. 
		- TGS can make this assumption because the ticket was created by AS which was encrypted by a key shared between AS and TGS and cannot be modified by a client, and the secret session key (K<sub>c,tgs</sub>) is also created by AS and encrypted by using the user's password so it is only know to the user. So if the user has the correct session key then we can confirm the user's identity
	- in step 4 TGS return a message to C encrypted using secret session key(K<sub>c,tgs</sub>) which contain the service granting ticket -Ticket<sub>v</sub> , new secret session key(K<sub>c,v</sub>), ID of server and timestamp
	- in step 5 the client sends the Ticket<sub>v</sub>, and authenticator to the server
	- the server decrypt the ticket to get K<sub>c,v</sub> and use that to decrypt the authenticator
	- step 6 - if mutual authentication is required then the server can send the value of timestamp in the authenticator incremented by 1 and encrypted using the session key, client can decrypt the message to recover the timestamp. because the message was encrypted using the session key the identity of the server is verified and the content of the message ensure that it is not a replay attack
	- Now C and V share a secret key that can be used to encrypt message or exchange new session keys

#### Kerberos Realms and Multiple Kerbers:
- a Kerberos environment consists of a Kerberos server, a number of client and a number of application servers
- only registered user and server can function in a Kerberos environment
- this Kerberos environment is called Kerberos realm
- **Kerberos realm is a set of managed nodes that share the same Kerberos database**
- Kerberos database should be kept safe and is accessible only be using Kerberos master password
- **Kerberos Principle** - a server or user know to the Kerberos system
- requirements of Kerberos
	1. Kerberos sever has user ID and password of all users in its database
	2. Kerberos share a secret key with each server
	3. Kerberos server in one realm share a secret key with Kerberos server in other realm
##### Inter realm communication
- Sometimes a user in one realm may need to access services in another realm 
- for this to work both the servers must trust each other
![[Pasted image 20250422211411.png]]
![[Pasted image 20250422211423.png]]
- working
	- the working is almost similar to Kerberos version 4
	- first client request ticket granting ticket (Ticket<sub>tgs</sub>) from AS
	- AS sends a message encrypted in the users password which contains the secret session key between tgs and c, Ticket<sub>tgs</sub>, and other details
	- C sends the ticket, authenticator and ID of the tgs in the other realm to TGS
	- TGS reply to C a new session key and also a ticket for communicating with TGS in the other realm (Ticket<sub>tgsrem</sub>)
	- C sends the Ticket<sub>tgsrem</sub> and authenticator to the TGS in the other realm - TGS<sub>rem</sub> 
	- TGS<sub>rem</sub> return service granting ticket(Ticket<sub>vrem</sub>) of the remote server(V<sub>rem</sub>) to the client
	- Client uses the Ticket<sub>vrem</sub> to communicate with the remote server
- problems:
	- this approach does not scale well, if there are N realms then there needs to be N(N-1)/2 secure key exchanges.
#### Kerberos Version 5
- Provide many improvements over version 4
##### Differences Between version 4 and 5:
- Environmental Shortcomings
	1. **Encryption system dependence** - Version 4 use DES, but has export restrictions, Version 5 make use of AES
	2. **Internet protocol dependence** - version 4 requires use of IP addresses, other addresses are not allowed, version 5 addresses are tagged with address type and length, allows any network address type
	3. **Message byte ordering** - version 4 employs byte ordering of its own choosing, version 5 message structure defined using Abstract Syntax Notation one and Basic Encoding Rules
	4. **Ticket lifetime** - version 4 lifetime encoded as an 8-bit quantity in units of five minutes, max lifetime = 2<sup>8</sup> x 5 = 1280minutes, In version 5 we can specify explicit start and end time
	5. **Authentication forwarding** - version 4 does not allow a client to access a server and have that server access another server on behalf of the client, version 5 allows this
	6. **Interrealm authentication** - in version 4 N realms require N<sup>2</sup> Kerberos-to-Kerberos but version 5 support a fewer relationship method
- Technical Deficiencies
	1. **Double Encryption** - The tickets in version 4 are encrypted twice using the secret key of the target server and again with a secret key know to the client, the second encryption is completely wasteful
	2. **PCBC encryption** - encryption on version 4 uses non standard mode of DES know as Propagating Cipher Block Chaining, it is vulnerable, Version 5 provides integrity mechanisms allowing the standard CBC mode to be used for encryption. checksum or hash code is attached to the message prior to encryption using CBC.
	3. **Session keys** - in version 4 a session key is included in the ticket that is used by the client to encrypt the authenticator, that session key is also used to protect messages during that session, so there exist a risk where an opponent can replay a message from a previous session to the client or server. In version 5 a subsession key is used for communication. when client does a new access a new subsession key is used.
	4. **Password attacks** - both version 4 and 5 are susceptible to password attacks. Message from the As is encrypted by the password of the user, an opponent can capture the message and try guessing the password and can find the password. version 5 provides preauthentication which makes password attacks more difficult but still there is a possibility 
##### Version 5 authentication dialogue
![[Pasted image 20250423092833.png]]
- new elements added on top of version 4:
	- Realm - Indicate realm of user
	- Options - request which flags need to be set in returned ticket
	- Times - used to set the time settings of the ticket such as
		- from - start of validity of ticket
		- till - when the ticket should expire
		- rtime - upto to what time a renew of ticket can be requested
	- Nonce - a random value to ensure that the message is not a replay
- working:
	- in message(1) client request AS for ticket-granting ticket, it is like version 4 but there are additional contents in the message 
	- in message(2) client receives Ticket<sub>tgs</sub>, ID of client and a block encrypted by the users password
	- the encrypted block contains secret session key between tgs and c, Times and Nonce from message(1), Realm of TGS and ID of tgs
	- The ticket received in message(2) includes the session key, identifying information for the client, the requested time values, and flags that reflect the status of this ticket and the requested options.
	- Like in Version 4 in message(3) C sends to TGS, Ticket<sub>tgs</sub>, ID of server whose service is needed, authenticator and additionally sends Time, Nonce and options
	- Authenticator used here is slightly different from the one in version4
	- Message(4) is like message(2) , TGS returns to client a new session key for communicating with server K<sub>c,v</sub>, Ticket<sub>v</sub> and some info encrypted in the secret key shared between client and TGS K<sub>c,tgs</sub>
	- in message(5) client sends Options, Ticket<sub>v</sub> and authenticator to server, in the options client can request for mutual authentication
	- The authenticator used here also has two new fields
		- subkey - client can specify a session key to protect this session, if left blank then K<sub>c,v</sub> will be used
		- sequence number - a starting sequence number to be used by server for messages send to the client during this session, used to detect replay attacks
	- message(6) is send only if in message(5) client set the option for mutual authentication
	- the server sends the same timestamp received from the authenticator in message(5), subkey and sequence number to the client
	- if subkey is present in message(6) it will override the subkey in message(5) and sequence number is an options field that specifies the starting sequence number to be used by the client
#### X.509 Certificates



