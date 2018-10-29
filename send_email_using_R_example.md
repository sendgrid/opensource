Create a SendGrid account so you can send emails if you do not have.

Go to the Sendgrid signup page to create your account. 
Enter the requisite details and select the 30-day free trial option which allows to send up to 40,000 emails.
Click the Create Account button and then complete the additional fields on the next page (your role, company size, etc.) to complete the account setup process.


Create a SendGrid API key

After your SendGrid account has been set up, you can now create a SendGrid API key. The API key is a code that will enable your R code to ask SendGrid to send emails. From your SendGrid account dashboard:

sendgrid settings

Select Settings > API Keys
Click the blue Create API Key button in the top right corner of your screen.
On the Create API Key screen, enter an API Key Name (for example, Displayr_APIKey).
Select the Full Access option.
Click the Create & View button to generate and display the API Key.
Click on the displayed API key to copy it.
Paste the key into a text editor, and save it to a text file on your hard-drive for safe-keeping. Note that for security reasons, SendGrid will not display the API key again.


Write the email body
The next step to automatically write and email reports is to write the email body. The message text for the email needs a string, stored in Displayr in an R Output. To create an R output, open Displayr and select Insert > Analysis > R Output. You then type in completely standard R code, referring to the data in your Displayr document.


Generate the automated email by creating an R output in Displayr

Add the following to the R CODE box which loads the R libraries needed to create and send the email.

	require(httr)
	require(jsonlite)
	require(flipTime)

This line ensures that the code is re-run every 24 hours and that the dashboard is updated to show the latest results.

	UpdateEvery(24, "hours", options = "snapshot")

Next line should be added with your SendGrid the API Key entered in the quotes.

	key = "your_api_key" #enter your API Key here

Specify the email parameters and construct the automated message

Next, the email parameters are specified and the email message is constructed. 
To do this, enter the recipient’s email address in the first line (to.email). 

The sender’s email address is entered in the second line and can be any email address you choose (from.email). You can also specify the email subject and message body here. 
Also, you could also replace body with a static message in the fourth line of below mentioned code (e.g., message.body= “Hello World.”).

	to.email = "recipient@domain.com"
	from.email = "sender@domain.com"
	subject = "Testing Sendgrid on Displayr"
	message.body = body
	 
	msg = sprintf('{"personalizations":
	        [{"to": [{"email": "%s"}]}],
	          "from": {"email": "%s"},
	          "subject": "%s",
	          "content": [{"type": "text/plain",
	          "value": "%s"}]}', to.email, from.email, subject, message.body)

The next line of code is the SendGrid API call which sends the email.

	pp = POST("https://api.sendgrid.com/v3/mail/send",
	        body = msg,
	        config = add_headers("Authorization" = sprintf("Bearer %s", key1),
	                        "Content-Type" = "application/json"),
	        verbose())

Check the Automatic check box next to the  Calculate button to run the code. You should receive an email at the recipient email address you specified in the code.

Customize formatting using HTML 

For example purpose, replace the body variable above with the following to generate an HTML-styled email.

	body = paste(“<p>Dear User,</p>”,
	“<p>Here are the latest results from your last programming test:</p>”,
	“<ul><li>The highest test score this month was <strong>”, max.value, “</strong> for <strong>”, max.label, “</strong>.</li>”,
	“<li>The lowest test score this month was <strong>”, min.value, “</strong> for <strong>”, min.label,”</strong></a>.</ul>”,
	“<p>Click <a href=’https://xxxxxxxxx.xxxxxxxxxx.xxxxxxxxxxx′>here</a> for the full report.</p>”,
	“<p>Kind regards,<br />User</p>”)