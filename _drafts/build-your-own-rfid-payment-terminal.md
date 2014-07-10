## How it's used

Buying a drink with the system is really easy. All you need to do is go over to the terminal, scroll through the list of the available products using the two buttons under the screen, and when you've found the one you want, scan your RFID badge to purchase it. A message is displayed onscreen and a short musical melody is played to indicate whether or not your purchase was approved. With every purchase you have a 1:20 chance of winning your product. If you happen to win your drink, your account isn't debited and you're informed with a special message and a short clip of "Disco Inferno" played off the terminal speaker.

Administrative tasks such as registering new clients or adding money to accounts are handled using the web portal. Only certain administrative users have the right to perform these tasks, however all users can log into the site and consult their account balance.

## How it's designed

The drinks system is composed of three parts: the payment terminal, the web site, and an API. The terminal is built using an Arduino Ethernet and various other components, and both the API and website are built with ASP.NET (MVC/Web API) and C#.

We designed the system with extensibility in mind. As such, the vast majority of the program logic is housed on the server. As an example, to make a purchase the terminal sends a product ID and badge ID to the API. The API makes the necessary calls on the server to check if there is sufficient funds on the account, deduct the correct amount, and returns a string and a two-element string array back to the terminal. The string contains the musical melody, encoded using a simple alphanumeric protocol, and the string array contains the text to show on each of the two lines of the terminal. At no point does the terminal have any notion of a price, 

 The brains of the system are housed on a server, where the API and web server are located, and the payment terminal uses basic RESTful commands to communicate with the server via the API.

## How it works

## How you can use it

The code for the entire system (terminal, API and website) is available on GitHub. The terminal 
