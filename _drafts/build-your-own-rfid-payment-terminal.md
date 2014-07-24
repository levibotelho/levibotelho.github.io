## How it's used

Every user is equipped with an RFID badge. In our company we already have key fobs that we use to get into the building in the morning, so we simply integrated a RFID reader into the terminal which operates on the same frequency.

To purchase a product, the user simply selects a product using the two buttons below the LCD display, and when they've found one they like, scan their badge to make the purchase. The system responds with an onscreen message and musical melody to indicate whether the purchase was accepted or not. In addition to this, we also added a lottery whereby one out of every twenty purchases is on the house. When you win, you're treated to a special message onscreen and a small audio clip of "Disco Inferno" that sounds like it's playing on a Nokia from the 1990s.

Administrative tasks are handled on the website which accompanies the system. This includes user creation, password changes, and account reloads. Non admins can also use the website to consult their account balance.

## How it works

The entire system is backed by a SQL Server database which holds all of the data that makes the platform run. The database is fronted by a transactional service layer which wraps all of the database calls. This layer makes use of Entity Framework for O/RM.

Atop the server software stack sits an API and the web application. The web application, is used to perform administrative tasks, and runs on ASP.NET MVC. The API is implemented using ASP.NET Web API. The API exposes two RESTful endpoints which serve to handle all logic between the client and the server.

We designed the system to be embarassingly server-centric. In fact, the terminal really only knows how to do three things.

1. Request a list of products and display them onscreen.
2. Send a "buy" request to the API.
3. Display a message and play a melody returned from the server.

That last point is important. The terminal has absolutely no knowledge of users, balances, or transactions. When a user purchases a product, it simply tells the server "the user with ID ______ wants the product with the ID _____". The API handles absolutely everything else. This makes the system extremely flexible and 

 The terminal itself contains very little logic. The server end of the web app runs on ASP.NET MVC, and the client-side experience is augmented  The API handles all communication between the terminal and the server 

To add an element of fun to the system, we also integrated a lottery whereby one out of every twenty purchases is free. 

Buying a drink with the system is really easy. Just select a product using the buttons under the screen, and when you've found the one you want scan your RFID badge to purchase it. In our company all employees have RFID badges to get into the building in the morning. We simply integrated a reader which operates on the same frequency as those which open the company doors.

Scanning your badge causes the read

 All you need to do is go over to the terminal, scroll through the list of the available products using the two buttons under the screen, and when you've found the one you want, scan your RFID badge to purchase it. A message is displayed onscreen and a short musical melody is played to indicate whether or not your purchase was approved. With every purchase you have a 1:20 chance of winning your product. If you happen to win, your account isn't debited and you're informed with a special message and a short clip of "Disco Inferno" played off the terminal speaker.

Administrative tasks such as registering new clients or adding money to accounts are handled using the web portal. Only certain administrative users have the right to perform these tasks, however all users can log into the site and consult their account balance.

## How it's designed

The drinks system is composed of three parts: the payment terminal, the web site, and an API. The terminal is built using an Arduino Ethernet and various other components, and both the API and website are built with ASP.NET (MVC/Web API) and C#.

We designed the system with extensibility in mind. As such, the vast majority of the program logic is housed on the server. As an example, to make a purchase the terminal sends a product ID and badge ID to the API. The API makes the necessary calls on the server to check if there is sufficient funds on the account, deduct the correct amount, and returns a string and a two-element string array back to the terminal. The string contains the musical melody, encoded using a simple alphanumeric protocol, and the string array contains the text to show on each of the two lines of the terminal. At no point does the terminal have any notion of a price, 

 The brains of the system are housed on a server, where the API and web server are located, and the payment terminal uses basic RESTful commands to communicate with the server via the API.

## How it works

## How you can use it

The code for the entire system (terminal, API and website) is available on GitHub. The terminal 
