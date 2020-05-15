<img style="text-align:center" src="https://raw.githubusercontent.com/monty-nietzsche/ruotadellafortuna/master/images/logo.png" alt="Create databaseManager" width="400"/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

![Java](https://img.shields.io/badge/java-v8.0-blue.svg)
![Group work](https://img.shields.io/badge/group%20work-yes-brightgreen.svg)
![Works online](https://img.shields.io/badge/works%20online-no-red.svg)
![RMI](https://img.shields.io/badge/rmi%20technology-yes-brightgreen.svg)
![Contributions welcome](https://img.shields.io/badge/contributions-welcome-orange.svg)


# Publishing ruotaDellaFortuna on Amazon Web Services

## Creating the main server and binding it to the registry

### Security files 
So that the transfer of the remote references happens without any problems, we need to create security policy files to give
permissions for remote referencing. We opened an empty text file and wrote the following:
```ruby
grant {
    permission java.security.AllPermission;
};
```
and saved under the name 'security.policy'. 

Now, we let Java know what the security policy is through: 
```ruby
System.setProperty("java.security.policy","file:/C:/Users/user/Documents/security.policy");
```

I have chosen to put my security file under Documents. Of course, you can put it anywhere you'd want.

### Create the registry on the server machine

The registry is created on the server machine by invoking the method `LocateRegistry.createRegistry()`. The default port is 1099.
```ruby
LocateRegistry.createRegistry(port)
```

### Create the stub (remote reference to your object)

We do not let  our object 'Server' extend UnicastRemoteObject(), just that the interface of the remote object should extend Remote.
UnicastRemoteObject exports the remote object to a random port and we would like to control which port the remote object is
exported to. We do this for reasons that we will detail later in the document.

```ruby
	Public Class serverInterface extends Remote() {
		...
	}
```

After creating the registry, we export the server object i.e. we create a remote reference to it. We call it `stubserver`.

```ruby
	ServerInterface stubServer = (ServerInterface) UnicastRemoteObject.exportObject(server, 1099);
```

The reference contains two identifications of the server object i.e. the host and the port. Printing the `stubServer` in the console gives:

```ruby
	Proxy[ServerInterface,RemoteObjectInvocationHandler[UnicastRef [liveRef: [endpoint:[192.168.1.99:1099](local),objID:	[-6b787b94:172184911d0:-7fff, -5728301903043910429]]]]]
```

Here the host is the (local) IP address of the server machine `192.168.1.99` and the port is the port at which the object server is accessible `1099`. 


**The host might not be very important in the case of the stubserver because it will not travel between machines but for other objects
we will see it is very important**. To see this, when a remote reference of an object, say 'Match' is sent from the server to the client as a    return value. Once the remote reference stubMatch is received on the client, any invocation of a method of stubMatch will be transferred to the original object 'Match'. How does 'stubMatch' traces back the original object? It is through __the host__ and __the port__. 

### Binding the server stub to the registry
After the object server is exported, i.e., the remote reference 'stubServer' is created, it is time to bind it to the registry and give a name that the client will use to look it up. The name we have chosen it "SERVER".
```ruby
	register.rebind("SERVER", stubServer);
```

### Full Code

The full code for the creation and binding of the server is:
```ruby
	System.setProperty("java.security.policy","file:/C:/Users/user/Documents/security.policy");
    	
    	if (System.getSecurityManager() == null) {
		System.setSecurityManager(new SecurityManager());
    	}

    	ServerInterface stubServer = (ServerInterface) UnicastRemoteObject.exportObject(server, 1099);
	register = LocateRegistry.createRegistry(1099);
	register.rebind("SERVER", stubServer);
```

## Accessing the SERVER from a client machine

### Security files 

The client, too, needs to have security policy file. We use the same file above 'security.policy'.
```
	if (System.getSecurityManager() == null) {
		System.setSecurityManager(new SecurityManager());
	}
	System.setProperty("java.security.policy","file:/C:/Users/user/Documents/security.policy");
```

__PS__: You might not need to have these policies if you run your program locally (on localhost). However, there are very important
if you run it over the internet.

### Locate the registry on the server machine

So that the client finds the registry on the server machine, it needs the public IP address of the server machine. If the server machine has a __public IP address__ ,then it is the address that we are looking for. If, however, the server machine accesses Internet through the router, then its IP address is different from its public IP address. To set your server when you are behind a router, please see below 'RMI and port forwarding'. 

Assume that the server has a public IP address `3.17.23.19`. We then need to search for the remote registry at that host address and at the port `1099`. We do that using the command `registry.getregistry()`.

```ruby
	host = '3.17.23.19';
	registry = LocateRegistry.getRegistry(host, 1099);
```

### Find the remote reference stubServer

Once we have found the registry, we just need to look up for the server remote reference `stubServer`, for which we have given the name "SERVER' above. 
```ruby
	server = (ServerInterface) registry.lookup("SERVER");
```
__Note__ that we look for the name `SERVER` on the registry. It is the name that we have given to the reference `stubServer` when we bound it to the registry. Recall:
```ruby
	register.rebind("SERVER", stubServer);
```

__Important__:Now, the client can interact with the server side using the methods of the object server. It is important to remind the reader that the client has an idea about the methods of the remote object 'server' because it has the interface of the object server. 

### Full Code
The full code on the client side:
```ruby
	System.setProperty("java.security.policy","file:/Users/MG/Desktop/security.sec");
	if (System.getSecurityManager() == null) {
		System.setSecurityManager(new SecurityManager());
	}
	host = '3.17.23.19';
	registry = LocateRegistry.getRegistry(host, 1099);
	server = (ServerInterface) registry.lookup("SERVER");
```
## Sending the callback remote reference (Client listener)

The update of the user graphical interface relies on the object `ClientManager`, which is an object created by the client.
A remote reference of local `ClientManager` object is, then, sent to the server, which uses it to invoke methods in the original object
stored in the client machine. For example, the server might want to inform players that a player has left the game. When the event
occurs, the server objects can call a method on the proxy objects of the `ClientManager` object for each player in the match. It does so
by calling `stubClient.Notifyleaver()`. The method will then be automatically transferred to the original `ClientManager` object,
which will show a notification on the client machine. So that the clientstub can trace its way back to the original object, it
needs to have two pieces of information `host` and `port`. You can see here an example of clientstub.
```ruby
Proxy[ClientManagerInterface,RemoteObjectInvocationHandler[UnicastRef [liveRef: [endpoint:[85.230.100.168:2020](remote),objID:[1efd7e30:172140d00e2:-7ffe, 6986137974138417350]]]]]
```

In the endpoint, you can see `endpoint:[85.230.100.168:2020](remote)`. The `85.230.100.168` is the public IP address (**that can be used to access the machine from the Internet and not an local IP given by the router**) and `2020` is the port at which the original object 
'ClientManager' lies. 

The host is again the public IP of the client machine and the port is any port we choose, provided that the server machine accepts TCP transmission on it (the port is open). Since the client machine has a unique public IP address under one session, we can set the host address to be used by all remote reference sent by the client machine; we do this by setting the variable `java.rmi.server.hostname` equal to the public IP address. Once this is done, all exported remote references will have the same IP address as their `host` property. 
```ruby
	System.setProperty("java.rmi.server.hostname", "85.230.100.168");
```

This has to be set before (preferably long before) attempting to export the object. Once the rmi server hostname is fixed; we create the clientstub, i.e., the remote reference to the local object 'ClientManager' in the client machine on the port `2020`.
```ruby
	client = new ClientManager();
	stubclient = (ClientManagerInterface)UnicastRemoteObject.exportObject(client, 2020);
```

Now, the stubclient is ready to be sent to the server as an argument of a method call of the object 'server'. Once the `stubclient` is on the server machine, it can be called and the stub will forward all method calls to the original object. For example, we sent the `stubClient` to the server by calling the method `server.creatematch(stubClient)`. 

++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Sending a remote reference from the server to the client
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

It is very similar to sending the callback remote reference. Before exporting any local object, we need to specify the host value that
the remote reference will have. We do this by setting the server hostname for the RMI to the public IP address of the server machine:

System.setProperty("java.rmi.server.hostname", "85.230.100.168");

One remote reference that the server needs to transfer to the client machines is the remote reference to the object match. The object 'match' runs all aspects of the game itself; but it is at the same time shared by all players that play the game (as well as observers). To be sure that all players modify the state of the same match (the same 'Match' object); they all shall receive a remote reference to the same object 'Match'. The export port can be of your choosing. The creation of match stub is as follows:

	match = new Match();
	stubMatch = (Match)UnicastRemoteObject.exportObject(match, 999);

While the remote references from the client to the server are sent as method arguements, the remote references send from the server to the client as return values. 

For example; a simplification of our code is:

Client Side:
	client = new ClientManager();
	stubclient = (ClientManagerInterface)UnicastRemoteObject.exportObject(client, 2020);
	server.createMatch(stubclient);

Server Side:
	public Match createMatch(clientManagerInterface client) {
		match = new Match();
		match.addplayer(client);
		stubMatch = (Match)UnicastRemoteObject.exportObject(match, 999);
		return stubMatch;
	}

We can see here, how the client and the server have exchanged remote references. The match will be responsible in managing the game while the client will be responsible for updating the user interface. 
	
.....................................................................................................................................................................................
Playing the Game Online
......................................................................................................................................................................................

+ Access of the server on Amazon Web Services
-----------------------------------------------------
Both the server and the database are hosted at Amazon Web services. The IP address of the server as well as the credentials
of the database are included in the runnable JAR client so you should not worry about them. Since the server machine on AWS has a public IP address, sending the object 'match' as a 'matchstub' shall not be any problem. However, the 'clientstub' sent from the client machine to the server could be problematic if the client machine accesses Internet behind a router. For this, the client user should make a port forwarding. The application 'ruota' detects if the client user is behind a router and gives him all details needed to make the port forwarding on the router. 


+ Access of Game from behind a router.
-----------------------------------------------------
Many machines access Internet through a router so they are assigned IP by the router. This IP, however, does not allow them to receive traffic from the internet. For example, if a machine gets an IP address from the router '168.192.1.100', then trying to access this address from the Internet will fail. For example, if you try to host the game server on this machine, and you give that IP address to your clients so that they connect to the registry; they will get a timeout error because the client will not be able to find the machine having that address. The address is local. There is, however, a public address that might lead to your machine, which is the public IP address of the router. However, if you give that address instead to your clients, they will too have a timeout error since the client machines will be trying to find the registry on the router. The router would not know what to do with the request at the port 1099... unless we tell him what to do. This is called port forwarding. We will basically tell the router to direct all traffic coming to the public address at the port 1099 to your machine identified by its local IP address. Most routers have port forwarding capabilities so connect to your router and make the proper port forwarding. In this case, you will be able to receive client requests as a server at the port 1099; or receive server callbacks as a client at the port 2020. 

The current version of our RuotaDellaFortuna client instructs the client user which port to forward in order to be able to play the game published online. It does it this way:
 
- First find public IP (use code from amazon to find public IP)
- Find Local IP using InetAddress 
- Compare both to see if there is a router.
- Assign a port to the exported callback stub clientManager randomly from 2000 and 2099.
- Show the port to Client and say that the client should do port forwarding on the router in order to be able to access the game.
All calls to the port 2000 should be forwarded to the local IP address at the port 2000.

Assume that the public IP address is '12.12.33.232' and the local IP address '192.168.1.190'. The client would realize that it is behind a router.
(0) The client picks a port between 2000 and 2099 say '2200'.
port = rnd():
(1) It will set first it RMI server hostname to the public IP address:
System.setProperty("java.rmi.server.hostname", "85.230.100.168");
(2) It exports the clientstub at the port 2200
client = new ClientManager();
stubclient = (ClientManagerInterface)UnicastRemoteObject.exportObject(client, port);

It will then show the client user a message so it makes the following port forwarding:
<service name><PortRange><LocalIP><Port><Protocol>
<Ruota Client><2200><192.168.1.190><2200><TCP>

If the user makes the aforementioned port forwarding modification, (s)he should be able to access the game at the Amazon Web Services server. 
