# N3XTT-AB, Private Message Application


### Server Communication Data - [ SocketDataTypes ]

The Following is a list of the data types that the server communicates with,
and what each communication object takes for parameters.


The following is for when a client sends a message to the **server**
| Type          | Index | Parameters                                            |
| ------------- | ----- | ----------------------------------------------------- |
| UUID          | 0     | {}                                                    |
| NAME          | 1     | { name: string }                                      |
| CONNECT       | 2     | { name: string, uuid: string }                        |
| ---           | ---   | ---                                                   |
| MESSAGE       | 3     | { receiver: string, message: string }                 |
| ---           | ---   | ---                                                   |
| ERROR         | -1    | **Not Yet Implemented**                               |



The following is for when the server sends a message to a **client**
| Type          | Index | Parameters                                            |
| ------------- | ----- | ----------------------------------------------------- |
| UUID          | 0     | { uuid: string }                                      |
| NAME          | 1     | { name: string, uuid: string }                        |
| CONNECT       | 2     | **Not Yet Implemented**                               |
| ---           | ---   | ---                                                   |
| MESSAGE       | 3     | { sender: string, uuid: string, message: string }     |
| ---           | ---   | ---                                                   |
| ERROR         | -1    | { cause: string }                                     |



##### Examples of communication

Client to Server - Message Example
```ts
{
    receiver: "<client uuid>",
    message: "Hello World!"
}
```

Server to Client - Message Example
```ts
{
    sender: "<name>", // Both sender and UUID will have a value to identify the sender
    uuid: "<sender uuid>",

    message: "Hello World!"
}
```



In order to send a message to the server an ```auth string``` is required and a ```data type```,
an example of a full message sent to the server.
```ts
{
    type: <data type index>,
    auth: "<auth string>",

    // This is an example of what data could look like
    data: {

        receiver: "<uuid>",
        message: "Hello World!"

    },
}
```



### Connect to the server

There are some steps to connect to the server, and to send messages to the server.
```md
- Get public certificate from server - HTTP, Port 19925
- Create a SSL secured Socket - TCP, Port 19918

- If the client does not have a UUID, send a UUID request to the server **( See the tables above )**
- Send a connection message to the server **( See the tables above )**

- Now you are able to send messages to the server
```

Each message, event the HTTP request needs an ```auth``` parameter.
Example of getting the certificate from the server would be to send the following HTTP
```bash
http://<IP>/?auth=<AUTH>
```

And to send a message to the server use the same auth for all the messages.
```ts
{
    type: <data type index>,
    auth: <AUTH>,

    data: { ... }
}
```










<br><br><br><br><br><br><br><br><br><br>

# Private_Messenger
RAM-based messaging app

Project concept:
A messaging app where one can generate an account, like mullvad VPN does and immedietly get access to the messaging service. It will generate a random account login and ID, and then you get to choose a nickname/name for the user.

<img src="./android_ui.png" alt="Boomerang Art" width="250" height="500" style="border-radius: 25px;" />



## Project description
### Example:
You create an account by pressing a "start account", which generates an account login for you to keep for yourself. An ID for the account which you can share to anyone to find other people, like a street address so to message one you add their ID to your contacts. And lastly a nickname so people know who they're messaging, example gadan with an ID of "AC312K" or Xtarii with an ID of "BK523A". This data will be stored in an XML file locally on the users device, nowhere else. So if the user loses their device or deletes the app/XML file it permanently removes everything of the user and all connections and messages to it.

### Server:
There will be a "middle man" server where you send your message encrypted to with a request of which ID you wanna send the message to. It works by sending your message to the servers RAM, then the server will look for the ID it was requested to send to and send the message to that device. If that device with the ID you requested to send to is not available it will store your message in the servers RAM for 1 week / 7 days and ping every 1 minute the first day, and every 5 minutes the following days to look for the ID to go online again. If it finds the ID it will send the message to that device. But if the 7 days has passed it will delete the message from the servers RAM and send an error message back to the original sender to notify the message has not been sent.

### The app:
The app will constantly be running in the operating systems background when you start up the device. So as long as your device is powered on you can recieve notifications when a user sends you a message and read them when you open the app, because it sends the message from the server to your device. When you turn off the device it will clear the RAM deleting all messages recieved, but still keep the ones sent as they're stored locally on your device.

This makes it possible in the future of a later version for a user to read the history of a conversation by requesting a peer-to-peer connection to get the messages from both sides. So that user 1 who has their own messages stored on their device can share their messages to fullfil the history of user 2's stored messages so they can look back in time for important messages they may have forgotten. Of course all messages recieved by user 1 through peer-to-peer will be stored in user 2's RAM. So when user 2 closes the app it deletes the history recieved from user 1's messages and you would need to request it again to see the complete history.

Summarized, your local device ONLY stores information on your account ID, nickname and messages you have sent, not recieved. Meaning if a user deletes the app it will permanently delete all messages said by the user and all connections to the user as the ID is gone.

## Ideas/Concepts

### Boomerang
När ett meddelande skickas till en klient så förfrågar den alltid för en peer-to-peer anslutning med meddelandet. Så om båda klienter är online och det skickas ett meddelande så frågar den, den andras klient för peer-to-peer och den andras klient godkänner det automatiskt. Så framtida meddelanden behöver inte servern för kommunication, tills peer-to-peer anslutningen bryts. På så sätt kan det optimera användningen på serverns processor extremt mycket. Det enda servern är värdefull till då är att spara meddelanden i RAM om användaren är inaktiv tills användaren blir online igen och skickar det meddelande i RAM. När båda är online och skickar uppföljningsfrågor eller bara meddelanden generellt till varandra så kommer det inte behövas någon mellanhands server för det. Borde gå att lösa för klienten genom att implementera en variabel som är true eller false beroende på om den är peer-to-peer ansluten eller inte. Så varje gång man skickar ett meddelande så kollar den en if ansluten peer to peer, skicka där. om inte, skicka genom servern?

<img src="./boomerang_concept.png" alt="Boomerang Art" width="500" height="500" style="border-radius: 25px;" />



### Random chat
Med en ny funktion till servern som gör att om två UUID’s letar efter någon parkopplas dom i en chatt och man kan diskutera vad man vill med en helt random främling. Alternativt inkludera ”tags” som typ lifehacks, programmering, sport, etc. Då matchar den din tag med den andra UUID som har samma tag, typ som en sökmotor

Kan lösas genom att kalla på funktion 5 för exempel. Då sätter servern ditt UUID i en letande lista, efter säg 10 sekunder så väljer den ett annat UUID i listan och servern svarar tillbaka med den personens UUID och nickname. Likvärdig för den andra personen. 

Finns det ingen annan i listan och du är den enda som letar och 10 sekunder har gått returnerar servern med ett meddelande att ingen annan är aktivt letande just nu.

Alltså, om jag skickar:
{typ 5, tag=programmering}
så returnerar servern med:
{UUID=”främlingens UUID”, nickname=”främlingens nickname”}

Då kan man implementera en ny funktion i appen som liknar Omegle, men för att förhindra folk att bara överge dig kan man sätta en 20 minuter cooldown i klienten, så folk bara inte hoppar över dig direkt utan är fast med dig tills 20 minuter har gått. Då får man se om man vill fortsätta prata eller inte. För det kan knappast vara tråkigare att bara vänta 20 minuter istället för att faktiskt diskutera tills 20 minuter gått.

Varför denna funktion? Lärorikt. Du kan lära dig prata med människor och kanske skaffa någon med likvärdiga tankar att prata med, helt anonymt utan att det händer något ifall man gör misstag
