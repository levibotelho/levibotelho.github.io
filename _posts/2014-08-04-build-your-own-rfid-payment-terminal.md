In my office we have a fridge and freezer stocked with drinks and ice cream. People can come by and for a small fee purchase one of the products that we have to offer. Formerly, our colleagues paid by using custom-engraved tokens that we sold for a small fee. This system worked well enough, but earlier this year I sat down with Benoît, a colleague of mine, to design a simple, flexible, and open source RFID-based payment system as a replacement.

Here is the result.

## How it's used

As is to be expected, everyone who wishes to use the system needs to be equipped with an RFID badge. While RFID badges are extremely cheap, we didn't actually need to give any out. Everyone in our company has a key fob that they use to get into the building in the morning. When building the system, we simply integrated an RFID reader that would read the fobs. Convenient!

This is what the payment terminal looks like.



Purchasing a product is really easy. Simply go up to the terminal, scroll through the list of available products using the two buttons below the screen, and scan your RFID badge to make the purchase. The terminal will respond with an onscreen message and musical melody indicating whether or not the purchase was accepted.

To add an element of fun, we decided to integrate a lottery into the system. By default, every purchase made has a 1:20 chance of being on the house. Should you happen to win your purchase, the terminal displays a special message and plays a small clip of "Disco Inferno" that sounds like a ringtone from a 1990s Nokia handset ;).

Administrative tasks, such as adding users or adding money to accounts, are handled using a web application that accompanies the system. Non-admin users can also use the site to check their account balance.

## How it works

The system architecture is best described with the help of a diagram.



As you can see, the core of the system consists of a SQL Server database that is abstracted by a transaction API. The transaction API is the only component which calls directly into the database. The two client components, the website and the RESTful API, call into the transaction API to perform their data-driven tasks.

The RESTful API encapsulates most of the logic that makes the payment terminal run. In fact, the payment terminal only really performs four different tasks:

1. Get the product catalogue from the server.
2. Display products to the user.
3. Send purchase requests
4. Present the response to a purchase request the user.

All communication with the RESTful API is done via HTTP with JSON-encoded payloads. Due to technological constraints on the terminal, communication between the client and server is not encrypted. Instead, message authenticity is validated using a hash value computed using the message contents, a timestamp, and a secret key that is shared between the terminal and server. It's not industrial-strength security, but it has proved to be sufficient for our needs. It's worth stating that the RESTful API cannot be used to credit accounts, so the worst anyone could do with it would be to repeatedly spoof purchase requests to empty somebody else's account. Not a great outcome, but the losses are limited, and in our casual environment we trust our users enough to assume that this will never happen.

One interesting facet of the terminal/server architecture is that the terminal is essentially stateless. When the terminal is powered on, it requests the list of available products from the server. Each product is represented by an ID and description. When a purchase is made, The terminal sends a badge ID and a product ID to the server and requests that a purchase be made. The server responds with a text message and melody (which is encoded alphanumerically), that the terminal displays and plays to the user. At no time does the terminal know the status of the transaction, how much money a user has on their account, or even how much a product costs. All this is handled entirely by the server, which means that it is extremely easy to modify the system without having to touch the terminal software.

### A word about the hardware

So far I've spent quite a bit of time discussing the system as a whole and the software that makes it run. The design and construction of the terminal, as well as the development of its embedded software, was done entirely by Benoît. He's written [an article on his blog that goes into detail about the terminal](http://blog.benoitblanchon.fr/rfid-payment-terminal/). If you're still reading this, then that article would probably interest you :).

## How *you* can use it

As mentioned up top, the entire RFID payment system is [100% open source and available on GitHub](https://github.com/drinks-wallet). The system is split into two projects: one which contains the server-side code, and the other which contains all the code for the terminal. Benoît's blog post, referenced above, contains instructions on how to actually build and wire-up the terminal.

While we built the system to serve our needs, hopefully by now you're convinced that due to the loosely-coupled nature of its components, the system is very moddable. Off the top of my head, here are a few things that you could do with it if you had the proper motivation.

### 1. Simplify the system for single-product purchases

If you wanted to implement a payment system to sell products at a single price point, then you could in principle get rid of the terminal's LCD screen and scroll buttons. This would both simplify the user interface and slim down the terminal's form factor.

### 2. Add multiple terminals

While our implementation of the system only has a single terminal, nothing says that it has to be that way! While there is no terminal identification mechanism coded into the system, if you don't care about what purchase came from what terminal, you could in principle build and deploy as many terminals as you wanted without making a single modification to the code! And if you did want to track terminals, it wouldn't be that hard to add a terminal ID to the communications protocol.

### 3. Upgrade the system for commercial use

We deployed our system back in February of this year, and so far it's been quite a success. However as we use the system in a casual environment, it lacks certain features which would be deemed necessary in order to use it in a real commercial situation. Things which come to mind include.

+ Add HTTPS support to the website, API, and terminal.
+ Add indexing to the database creation script.
+ Upgrade the terminal. The terminal works great as-is, but one could easily imagine a terminal with an upgraded screen which would allow users to see all available products at once without having to scroll through them one by one.

If you have any other ideas, or if you take this system and run with it to build something cool, let me know! Feel free to leave a comment or to contact me on Twitter @LeviBotelho. Same goes with questions/comments. I'm all ears :).

