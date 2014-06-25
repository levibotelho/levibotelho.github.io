wAt my current job we have a fridge and a freezer stocked with soft drinks and ice cream. We buy the items in large quantities and then people can come by and purchase one for a few cents more than we paid. Our team buys the items in bulk and w We buy the items in bulk, and then resell them individually just above cost.

To avoid having a box full of money sitting in the office, we originally managed payment by selling these tokens for a set price.

One token for a drink or small ice cream, or two for a large ice cream.

The system worked reasonably well, but a few months ago we decided to do one better. During our lunch breaks, Benoîot, who is a colleague of mine, and I sat down and designed an RFID-based payment system.

The system is composed of two halves.

1. An RFID terminal - The RFID module in the terminal is capable of reading the key fobs that we use to get into out building in the morning. This means that every employee at our company is already equipped with the hardware required to use the system.
2. A web server - The web server hosts both a website and a RESTful API. The site allows admins to create, edit, and add money to accounts. The RESTful API provides a means for the terminal to communicate with the system.

Benoît, who is well versed in Arduino, took on the responsibility of designing the terminal. I wrote the web server.

## Feature set
Before getting into all the gory details of how everything is implemented, I'll first take a moment to mention how the system works on a high-level.

Basically, everybody who wishes to use the system starts off with an RFID badge. As mentioned, this can be in the form of a key fob used to gain access to the building, or a wallet-sized card that can be given to those who don't happen to have a key fob. To actually give a person access to the system, an administrator uses the website to create an account and associate an RFID number with it. To get the number of the badge, the user simply needs to scan it using the terminal. The server will see that the badge is unrecognised, and return an "Invalid Badge" message, accompanied by the badge number. 

Once a person has an account, they can fill it by paying an administrator who collects the money and adds the appropriate amount to their account.

To purchase a product, the person goes to the payment terminal, scrolls through the catalogue, and then scans their badge. They are then informed with an onscreen message and musical melody whether or not their transaction was accepted. If the transaction is accepted, then the appropriate funds are removed from the person's account and they are free to take their drink. If not, the reason for the refusal is displayed onscreen.

To add an element of fun to the whole system, we also implemented a lottery, whereby one out of every twenty purchases is on us. If someone wins their purchase, the appropriate message is displayed, and a version of "Disco Inferno", reminiscent of a 1990s ringtone is played by the terminal ;).

## The terminal
This is what the finished terminal looks like.



In a nutshell, it does three things.

1. Displays a product and its price.
2. Lets the user scroll through the product catalogue.
3. Notifies the user when a transaction has been acknowledged with a sound and text message. Both the sound and the text are sent to the terminal by the server. It's important to note that in this context, the terminal only provides an HMI to the user. It has no knowledge of the status of the transaction.

This article focuses on my half of the project, meaning the web server, underlying infrastructure, and the protocol we designed to link the terminal and the server together. I'll spend a bit of time talking about the terminal's features in order to better motivate my explanations of how the server works, but if you're interested in how Benoît went about designing and writing the software which powers the terminal, you should check out [the article he wrote on the subject](http://blog.benoitblanchon.fr/rfid-payment-terminal/) for more details.

## The API
Perhaps the most important part of the system is the API which links the server and the payment terminal together. The API is divided into two halves: data synchronization, and transaction management.

The data synchronization part of the API provides the terminal with a catalogue of products to display to the user. It also tells the terminal what time it is, which is important for securing communication between the terminal and the API, as we'll see in a moment.

The terminal makes a GET request to the data synchronization endpoint on startup, and then once every ten minutes thereafter. This means that if the product catalogue is updated, it never takes more than ten minutes for those updates to be propagated to the terminal. Another reason for frequent updates is to keep the terminal clock in sync with the server's. Again, we'll soon see why this is important.

The transaction management API exposes a single endpoint which allows the terminal to submit a "buy" request to the server. The server then responds with a text message and a melody. The text message is sent as a two-element string array, with each element representing one line on the two-line display embedded in the terminal. The melody is encoded using pairs of the letters A through G + S, in both upper and lowercase (representing pitch) along with the letter S which is used to denote a pause between two notes. A number after each letter signifies how long the note is to be.

## The protocol
On a technical level, communication between the terminal and the server is done using HTTP, following a standard RESTful protocol, handled by ASP.NET Web API on the server side, and by using an Arduino Ethernet on the terminal side. Messages are serialized using JSON. Handling JSON in Web API is a snap, however Benoît ended up writing his own JSON parser for Arduino to handle the work on the terminal. [The code for the parser is available in its own separate project on GitHub](https://github.com/bblanchon/ArduinoJsonParser).

One challenge we faced when designing the system was defining a protocol for securing messages between the terminal and the server. The protocol had to be reasonably robust, but at the same time very lightweight, as memory on the terminal was extremely limited. We ended up settling on something reminiscent of a lightweight form of OAuth. Every message contains both a timestamp and a hash, which serve to validate the freshness and the provenance of the communication. While the protocol isn't by any means sufficient for a full-scale commercial application, it proved to be sufficient for our needs, and the fact that the API doens't expose any methods that allow users to credit their account works in our favour.

## The site

The 