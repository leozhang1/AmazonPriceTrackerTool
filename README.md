## Thanks for using my script! It is fulfilling to know that I can make someone else's life easier with this automation script

## Note that it is generally good practice to run this script in a virtual environment although you don't have to. There are many ## resources you can find online as to why it is better to do so.

Directions:
__Run these in terminal (assuming you're already in the directory containing AmazonPriceAlertTracker.py)__
* ```python -m venv [VirtualEnvironmentName]``` (run this only once if you dont have one in the directory already)
* ```[VirtualEnvironmentName]\Scripts\activate.bat``` (execute this line)
* make sure you have all the dependencies you need in this virtual environment by typing ```pip install -r requirements.txt```
* ```python AmazonPriceAlertTracker.py```

## The secrets.py module that I have imported is just my own personal information that I don't want to show to the public!
## What I can show is the boilerplate, so you can add your own (just don't forget to add secrets.py to your gitignore so you don't share information such as your email password to the public)
secrets.py boilerplate
```
import collections
import smtplib, ssl
from email.message import EmailMessage

class Secrets:
    senderEmail=[The email you want to use to send to one or more other emails (should be a dummy email as you will have to "Allow less secure apps" for the senderEmail)]
    senderEmailPassword=[senderEmail's password]
    receiverEmails=[Should be a list of emails you want to send the message to]
    EmailCredentials = collections.namedtuple("EmailCredentials", ['password', 'sender', 'recipients'])

# region sendEmail(filePath:str, fileName:str, email:namedtuple, subject:str, msg:str)
    @staticmethod
    def sendEmail(filePath: str, fileName: str, email, subject:str, msg:str):
        '''
        sends an email out with the passed in message. Make sure the sender's email has "Less secure app access" turned on!!!
        '''
        # out-going email port
        port = 465  # For SSL
        smtp_server = "smtp.gmail.com"
        context = ssl.create_default_context()
        with smtplib.SMTP_SSL(smtp_server, port, context=context) as server:
            try:
                server.login(email.sender, email.password)
                for recipient in email.recipients:
                    # must create a new EmailMessage object for every recipient
                    message = EmailMessage()
                    message['From'] = email.sender
                    message['To'] = recipient
                    message['Subject'] = subject
                    message.set_content(msg)
                    if filePath: # if you don't have any files to attach to the email, then just pass in an empty string or None for the filePath parameter when calling this function
                        with open(filePath, 'rb') as f:
                            fileData = f.read()
                        message.add_attachment(fileData, maintype="application", subtype="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", filename=fileName)
                    server.send_message(message)
                print('email sent!')
            except Exception as e:
                print(e)
                print("could not login or send the mail.")

example:

Secrets.sendEmail('', '', Secrets.EmailCredentials(sender=Secrets.senderEmail, recipients=Secrets.receiverEmails,password=Secrets.senderEmailPassword), 'Price Drop from Amazon!', message)

The first two arguments are empty strings because there's no attachments for this script. We are just scraping data from the web, storing that data in a string called "message" and then sending that out via email
# endregion

For the scrapy tool,
cd into amazon_price_tracker/ and type: scrapy genspider AmazonProduct amazon.com (for creation of a new spider)

scrapy crawl AmazonProduct (just to display the information in the terminal)
scrapy crawl AmazonProduct -o results.csv for appending to the file
scrapy crawl AmazonProduct -O results.csv for overwriting any existing information in the file and adding current results to it



```

## The send emails function only accepts gmails according to how I programmed it. You can customize it to use other types of emails if you'd like.
## You should add this script to a task scheduler if you're on windows or add this to cron jobs if you're on mac/linux, so that it can check periodically for price reductions without having you to do so manually.

## Enjoy!