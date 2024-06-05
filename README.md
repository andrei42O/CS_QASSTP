# Solutions to all the Lab Exercise  


## A1: Injection 
### Sql Injection
The user on accessing the lab is given with a login page,which challenges the user to login as admin.
The user now has to identify some mechanism to login as admin.
To test for sql injection ,the user can begin with a `'` or `"` based on the error generated he can confirm that it is an sql injection.
Search for common sql injections payloads in google and try using them to break the functionality of the backend code.

Since the challenge requires yout to login as admin, enter the `username` as  `admin` . The user now can use a very common sql injection paylaod to bypass the login validation
`anything' OR '1' ='1`.
Click login and now you will see that you have successfully logged into admins account.

#### Login :

![image](https://user-images.githubusercontent.com/61360833/118371215-27758800-b5c9-11eb-8591-212f448ddc13.png)

On Successful injection

![image](https://user-images.githubusercontent.com/61360833/118371252-5986ea00-b5c9-11eb-9efb-6beedd558f56.png)


## A2:Broken Authentication

The main aim of this lab is to login as admin, and to achieve this, exploit the lack of `rate limiting` feature in the otp verification flow. You can see that the otp is only of 3 digit(for demo purposes) and neither does the application have any captcha nor any restriction on number of tries for the otp.

Now to send the otp to admins mail you need to figure out the admins mail id. Luckily the admin has left his email id for the developers in the page source. Admin's email id is `admin@pygoat.com` Enter this email in the send otp input box and hit send,you can see that the page says that otp is sent to the email id of the admin. 

In order to exploit the lack of rate limiting , we can try to Brute-force the 3 digit otp.

#### Steps to Brute force: 

* Open Burpsuite and configure your browser to intercept the web trafic, but dont turn intercept on.
* Send the otp to the admins mail id with the help of send otp feature.
* In the enter the otp box enter a random 3 digit number.
* Before your press login , turn intercept on on Burp suite and then press log in
* Now you can see that the traffic is captured in Burpsuite.
* Now use the send to intruder feature and send this request to the intruder.
* Set the position of the payload to the otp= parameter.
* Go to the payloads session and choose the payload type to number list
* Fill the range to 100 to 999 with step 1.
* Now click attack and you can see that the burp suite tries different combinations of otp and collects it response.
* You can figure out if it has guessed the correct opt by seeing the difference in length of the response for each request.
* The correct otp will have a small response length .
* Using this otp you will be able to login into admins account.

#### Brute forcing using BurpSuite

After sending OTP to admins EmailID, start up, Start up `BurpSuite`, turn intercept on and send any randon OTP in send OTP box

![bau_6](https://user-images.githubusercontent.com/70275323/154610459-92ccc2be-236d-4cb2-acc7-af9bd68b49a6.png)

Once request is intercepted by burpsuite, click on `actions` and then `send to intruder`

![bau_1](https://user-images.githubusercontent.com/70275323/154610104-a831f913-4374-49cf-8f2b-dcbaae1e0533.png)

Your intruder screen should look something like this. Parameters enclosed by the `§` symbol will be brute forced. 

![bau_2](https://user-images.githubusercontent.com/70275323/154610112-39cc5fec-d407-4190-bd3f-9f4ee33f6c69.png)

Select payload type as Numbers, enter range as 100-999 and step as 1. Then start attack. 

![bau_3](https://user-images.githubusercontent.com/70275323/154610117-7505d2fc-f1a0-418e-b87a-b676e3ecd791.png)

When attack is going on, the correct OTP will have a different response length. In this case its smaller.

We see that OTP value 136 gives us a different lenght. 

![bau_4](https://user-images.githubusercontent.com/70275323/154610121-29a2abc5-b3b8-49e2-9028-a5850cbaf9d9.png)

It can also be found out by ordering by length

![bau_5](https://user-images.githubusercontent.com/70275323/154610124-2d8d267a-a198-47af-9baa-5b632617e186.png)


## A3:Broken Access Control

On accessing the lab the user is provided with a simple login in page which requires a username and password.

The credentials for the user Jack is `jack:jacktheripper`

Use the above info to log in.

The main aim of this lab is to login with admin privileges to get the secret key.

#### Exploiting the Broken Access

Every time a valid user logs in,the user session is set with a cookie called `admin`
When you notice the cookie value when logged in as jack it is set to `0`
Use BurpSuite to intercept the request change the value of the admin cookie from `0` to `1`
This should log you in as a admin user and display the `secret key`

#### Solving Using BurpSuite

We can log in as `jack:jacktheripper` and admin cookie is set to 0. This does not give us the secret key

![bac_1](https://user-images.githubusercontent.com/70275323/154612894-cb5e0dfa-f6d4-426a-8b3e-e92d300951fd.png)

To change admin cookie in request, we open up burpsuite and after logging in, refresh the page and change value of admin cookie to 1

![bac_2](https://user-images.githubusercontent.com/70275323/154612900-a2903776-ac60-4f40-9654-760869dc4eb7.png)

This results as being logged in as Admin

![bac_3](https://user-images.githubusercontent.com/70275323/154612904-917c1501-1a8a-49db-90d0-7879235e7eee.png)

#### Solving using Browser 

Once again, we log in as `jack:jacktheripper`

![ba_1](https://user-images.githubusercontent.com/70275323/154613752-e2fc76a1-669b-46b2-a622-62c13c513d5d.png)

In inspect section, change value of admin cookie to 1 and refresh the page. 

![ba_2](https://user-images.githubusercontent.com/70275323/154613760-6abaa18a-8f27-4c39-bb89-7c0dd320b5f6.png)

![ba_3](https://user-images.githubusercontent.com/70275323/154613762-16b4d637-0bfc-4196-8fdd-70458bd46994.png)


## A4:Cross Site Scripting

* Instead of giving a search term try giving a html tag, ``` <h4 >Hello </h4>.```
* Now you can see that the word Hello has been parsed as a Heading in the page.
* This shows that the page is able to render the user given html tags.
* In order to get an xss , the user needs to execute javascript code in the browser.
* This can be acheived by using a script tag and malicious javascript code.
* For now let's just use a basic javascript code to alert a text to prove that xss is possible .
`<script >alert(“xss”) </script >`
* Now when a search query is performed with the above payload you can see that the browser is able to render the script tag and execute the javascript , thus alerting “xss” with a pop up.

#### Solving XSS in Browser

Entering input with `h4` and `font color` tags to check for XSS

![xss_1](https://user-images.githubusercontent.com/70275323/154513165-672255a5-8c67-4bc3-924d-1848de072b3a.png)

Results page contains the word `TEXT` in Heading as well as Green color hence XSS Vulnerability is confirmed.

![xss_2](https://user-images.githubusercontent.com/70275323/154513170-53ec9273-f310-45fd-b30a-b09794604f3a.png)

Now you can go ahead and enter `<script >alert(“xss”) </script >` once XSS is confirmed.

To see results on screen, make sure your browser has JavaScript enabled.
