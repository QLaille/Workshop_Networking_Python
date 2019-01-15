# Networking in Python 23

## 0. Socket

Socket programming is connecting two computers on a network to communicate with each other.
It uses IP addresses and Ports.

## 1. Client / Server

For the sake of this workshop, work in pairs: one builds the server, one builds the client.

### A. Client

Start by importing the socket library and creating a socket object.

```
import socket

my_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```
The first parameter is AF_INET and the second one is SOCK_STREAM. AF_INET refers to the address family ipv4. The SOCK_STREAM means connection oriented TCP protocol (it ensures every packet is safely transmitted).

Then you decide with your partner which port to use and get its ip.
Several known protocols use low values (HTTP, SMPP, SMTP...), so pick a big number with at least 4 digits
(4242 for instance).

```
port = 4242

# replace this IP with the one of your mate (you can type 'ip addr show' in your terminal to get it)
ip = '10.101.53.203'
```

Finally, you connect to the distant socket

```
my_socket.connect((ip, port))
```

Finally, receive data (in bytes) sent by the server and print it.

```
print my_socket.recv(1024)
```
Then, implement a loop to chat with the server, by writing and receiving to/from this socket.
You may want to use those functions:

```
#Close the socket when you are done with it
my_socket.close()

#Get the address to which the socket is connected
my_socket.getpeername()

#Write to the distant socket
message = 'hello world!'
my_socket.send(message)

```

### B. Server

Start by importing the socket library and creating a socket object.

```
import socket

my_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```
The first parameter is AF_INET and the second one is SOCK_STREAM. AF_INET refers to the address family ipv4. The SOCK_STREAM means connection oriented TCP protocol (it ensures every packet is safely transmitted).

Then you decide with your partner which port to use.
Several known protocols use low values (HTTP, SMPP, SMTP...), so pick a big number with at least 4 digits
(4242 for instance).

```
port = 4242

# replace this IP with the one of your mate (you can type 'ip addr show' in your terminal to get it)
ip = '10.101.53.203'
```
As a server, you need to accept all incoming connections for potential clients, instead of providing an IP, we will pass an empty string.

```
my_socket.bind(('', port))
```

You then make the socket listen to X connections

```
x = 5
my_socket.listen(x)
```

A server needs to always be available, you now want to wait for new connections and send them greeting messages.

```
while True:
  connection, addr = my_socket.accept()
  
  print('Got connection from ' + addr)
  connection.send("Welcome to the server!")
  
  connection.close()
```

Then, implement a loop to chat with the client, by writing and receiving to/from its socket.
You may want to use those functions:

```
#Close the socket when you are done with it
my_socket.close()

#Get the address to which the socket is connected
my_socket.getpeername()

#Print 1024 bytes from the distant socket
print my_socket.recv(1024)

```

## 2. Secure your sockets

You can see that for now you are reading a certain amount of bytes to receive a message from the server/client.
This can be exploited, and it is called **buffer overflow**.

If you want an example, someone attacked a database using a Scarlett Johansson's picture to mine Monero (a cryptocurrency)
:https://www.imperva.com/blog/deep-dive-database-attacks-scarlett-johanssons-picture-used-for-crypto-mining-on-postgre-database/


You may want to use and implement a protocol to avoid those exploits.

To stay simple, we will implement a protocol using 2 keywords of 10 characters total, with a separator and an end of line.

Example: `HELLO:THERE\n`

Implement a simple protocol between the client and the server (the client write something and the server returns something according to it):

The client ask for for value or a + b:

`CALC:3+4\n`

The server returns:
`CALC:7\n`

The client ping the server:
`PING:TEST\n`

The server answers:
`PONG:TEST\n`

## 3. Going further

If you want to see what's available to you in Socket programming, you can still refer to the Python Documentation
of Low Level Networking: https://docs.python.org/3/library/socket.html#socket.socket.recv

A lot of applications on the web relies on Request For Comments (or RFC for short), which are documentations for their protocols. For example, [this is the RFC of the IRC protocol (an old chat method) works](https://tools.ietf.org/html/rfc1459).
