I recently recieved the following message in my inbox.

Now this message is supposedly from EDF, my electric company. It's informing me that I need to submit my electrical meter reading online in order to adjust my monthly payments to match my electrical consumption.

Now straight away something struck me as very odd, which was that the mail did not come from an @edf.com email address, but an @suivi-client-edf.com (roughly equivalent to @edf-customer-service.com) address.

So already this email looks suspicious, but hey, you never know. My first course of action was to sign into my account on the EDF website and go to the page where you report your meter reading. This is what I'm greeted with.



> Your contract does not allow you to report your meter reading.

Okay, so this is definitely a phishing attempt. I forward the mail to EDF's "suspcious email" address and include a small note explaining the situation. I receive the following automated response.

> Dear Sir, Madam,
> 
> Thank you for reporting this fraudulent email. This appears to be a phishing attempt.
> 
> We will immediately take appropriate action.
> 
> Understand that EDF does not work in this manner, and that all online payment is done through your secure account on our website.
> 
> Sincerely,
> 
> Your EDF customer care representative 

Okay, fair enough. But then I take another look at the email and notice that there are some personal details at the bottom, including my name, client number, account number, and the type of electricity contract that I have. I figure that the numbers/contract name might be chosen randomly and just there to give an air of authenticity to the mail, but when I verify them against my electricity bill I see that *they are correct*.

My thoughts immediately jump to a data breach. Has EDF recently fallen victim to a data breach? Did I throw away an electrical bill without tearing it up into small enough pieces? But still, why use such valuable information to get me to fill in a form?

I shoot an email off to EDF explaining the situation, indicating that I believe that my personal data has been compromised, and asking what I can do to protect my information given the circumstances (change account numbers? open a new account?)

However while waiting for their response curiousity got the better of me and I decided to follow the links in the message. The first link, which is supposed to indicate how to read your meter sends me straight to the EDF home page. Strange, but okay. I click the second, and here is where it sends me.


Instead of being at edf.com, where I would expect to be if I thought the mail was legitimate, we're at mailperformance.com. And as if that wasn't suspicious enough, the phishers didn't even bother to serve the form where I'm going to be sumitting my personal information over HTTPS to make it seem half secure! Talk about lazy...

So now things are pretty clear. Somehow, a certain malevolent actor got a hold of my details from the electrical company one way or another, and are probably using this form to either verify my identity or establish trust to attempt to defraud me in some way in the near future. In any case, the next step is to try to change as much as I can with the electrical company to make the stolen data as useless as possible. They might not be able to open a bank account in my name with my EDF account number, but electrical bills are a common way of proving one's identity in France and the idea that my details are in the hands of apparent criminals is disturbing nonetheless.

And then I get one more idea: try and track down the @suivi-client-edf.com domain. A quick Google search leads me to a page about phishing attacks involving EDF from a reputable "Consumer Reports" style magazine.

> Legitimate EDF email addresses end with one of the the following : @edf.fr, @edf.com, **@suivi-client-edf.com**, @relation-client-edf.com, @newsletter-edf.com, @communication-edf.com, @bleu-ciel-edf.com ou @info-edf.com.

**The email was legitimate.**

## Lessons in security

### 1. Behave in a way that customers expect you to

The fact that EDF apparently has not one, not two, but eight domains from which they send legitimate emails makes it extremely difficult to tell whether a given email is legitimate or not. If I receive an email from @customer-care-edf.com, am I to assume that it is legitimate? Few users will take the time or effort to search the net to find out. The smart ones will delete the mail, and the uninformed ones will simply assume it's real. Both cases are undesirable when the user is incorrect.

### 2. Make all workflows reproducible without clicking links in emails.

This isn't just about UX, it's about encouraging users to employ good security practices. Forcing users to click links in suspicious emails is asking for trouble...

### 3. Use HTTPS FFS!

Why do I even need to say this?

### 4. Take security issues seriously. Automated responses and untrained staff don't cut it.

The automated response indicating that my mail appeared to be a phishing email was clearly unhelpful because the mail was legitimate. Additionally, I asked several basic questions about the mail (such as "is it real?") in my message reporting the suspicious email, but got nothing in reply. Ideally, customer service representatives with training in handling cases of fraud and online attacks should respond to mails such as mine with comprehensive, and most importantly *accurate* information. I understand the economics of such a proposal; customer care representatives cost money. But so do security breaches.

By the way I ended up getting a response to my desperate second email asking what could be done to protect myself given that my data had been supposedly breached.

> Hello M. BOTELHO, 
> 
> Never transmit your personal data outside of your EDF client portal.
> 
> Sincerely,
> 
> Your EDF customer care representative

### 5. Don't screw up rules 1 - 4

Perhaps the biggest problem with this whole situation is that because as the email was legitimate, EDF customers who report their meter readings through the sketchy online form will end up receiving positive feedback. Given all that has happened here, I don't believe it a stretch to say that it would not be difficult to craft a phishing attempt that would look more real than the real thing. By breaking rules 1-4, you are rewarding users for bad behaviour and putting them directly at risk of phishing attacks in the future.