# Hacking Defense Note

https://www.hacksplaining.com/lessons


## SQL Injection
### Harm
Extract sensitive information, like Social Security numbers, or credit card details.
Enumerate the authentication details of users registered on a website, so these logins can be used in attacks on other sites.
Delete data or drop tables, corrupting the database, and making the website unusable.
Inject further malicious code to be executed when users visit the site.

#### Parameterized Statements
Make sure that the parameters passed into SQL statements are treated in a safe manner.
#### Object Relational Mapping
ORM makes the translation of SQL result sets into code objects more seamless. Using an ORM does not automatically make you immune to SQL injection, however. 
As a general rule of thumb: if you find yourself writing SQL statements by concatenating strings, think very carefully about what you are doing.

#### Escaping Inputs
Programming languages have standard ways to describe strings containing quotes within them – SQL is no different in this respect. Typically, doubling up the quote character – replacing ' with '' – means “treat this quote as part of the string, not the end of the string”.

#### Sanitizing Inputs
Client-side validation (i.e. in JavaScript) is useful for giving the user immediate feedback when filling out a form, but is no defense against a serious hacker. Most hack attempts are performed using scripts, rather than the browser itself.


## Cross-Site Scripting (XSS)
Spreading worms on social media sites. Facebook, Twitter and YouTube have all been successfully attacked in this way.
Session hijacking. Malicious JavaScript may be able to send the session ID to a remote site under the hacker’s control, allowing the hacker to impersonate that user by hijacking a session in progress.
Identity theft. If the user enters confidential information such as credit card numbers into a compromised website, these details can be stolen using malicious JavaScript.
Denial of service attacks and website vandalism.
Theft of sensitive data, like passwords.
Financial fraud on banking sites.

#### Escape Dynamic Content
you should escape all dynamic content coming from a data store, so the browser knows it is to be treated as the contents of HTML tags, as opposed to raw HTML.

Escaping dynamic content generally consists of replacing significant characters with the HTML entity encoding:
```HTML
"	&#34
#	&#35
&	&#38
'	&#39
(	&#40
)	&#41
/	&#47
;	&#59
<	&#60
>	&#62
```



#### Whitelist Values
instead of asking a user to type in their country of residence, have them select from a drop-down list.

#### Implement a Content-Security Policy
allow the author of a web-page to control where JavaScript (and other resources) can be loaded and executed from.
The content security policy can also be set in a <meta> tag in the <head> element of the page:
```HTML
<meta http-equiv="Content-Security-Policy" content="script-src 'self' https://apis.google.com">

```
This approach will protect your users very effectively!

#### Sanitize HTML

Some sites have a legitimate need to store and render raw HTML, especially now that [contentEditable](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Content_Editable) has become part of the [HTML5 standard](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/HTML5). If
your site stores and renders rich content, you need to use a HTML sanitization library to ensure malicious users cannot inject scripts in their HTML submissions.



## Command Execution

**Remote code execution** is a major security lapse, and the last step along the road to complete system takeover. After gaining access, an attacker will attempt to escalate their privileges on the server, install malicious scripts, or make your server part of a [botnet](https://www.hacksplaining.com/glossary/botnets) to be used at a later date.

**Command injection vulnerabilities often occur in older, legacy code, such as CGI scripts.**

#### Try to Avoid Command Line Calls Altogether

**Use APIs wherever possible –** only use shell commands where absolutely necessary. 

#### Escape Inputs Correctly

**Injection vulnerabilities occur when untrusted input is not sanitized correctly.** If you use shell commands, be sure to scrub input values for potentially malicious characters:

| ;    |
| ---- |
| &    |
| \|   |
| `    |

Even better, restrict input by testing it against a regular expression of known safe characters. (For example, alphanumeric characters.)



#### Restrict the Permitted Commands

#### Perform Thorough Code Reviews

#### Run with Restricted Permissions





## Clickjacking

- **Harvest login credentials**, by rendering a fake login box on top of the real one.
- **Trick users into turning on their web-cam or microphone**, by rendering invisible elements over the Adobe Flash settings page.
- **Spread [worms](https://www.hacksplaining.com/glossary/worms) on social media sites** like Twitter and MySpace.
- **Promote online scams** by tricking people into clicking on things they otherwise would not.
- **Spread malware** by diverting users to malicious download links.

#### X-Frame-Options

The `X-Frame-Options` [HTTP header](https://www.hacksplaining.com/glossary/http) can be used to indicate whether or not a browser should be allowed to render a page in a `<frame>`, `<iframe>` or `<object>` tag. It was designed specifically to help protect against clickjacking.

There are three permitted values for the header:

| DENY             | The page cannot be displayed in a frame, regardless of the site attempting to do so. |
| :--------------- | ------------------------------------------------------------ |
| SAMEORIGIN       | The page can only be displayed in a frame on the same origin as the page itself. |
| ALLOW-FROM *uri* | The page can only be displayed in a frame on the specified origins. |

#### Content Security Policy

#### Frame-Killing

In older browsers, the most common way to protect users against clickjacking was to include a frame-killing JavaScript snippet in pages to prevent them being included in foreign iframes.

Frame-killing offers a large degree of protection against clickjacking, but it can be error-prone. Be sure to set appropriate HTTP headers as the first recourse in protecting your site.



## Cross-Site Request Forgery (CSRF)

[
Steal confidential data.](http://www.darkreading.com/attacks-breaches/hacker-steals-data-on-18m-auction-customers-in-south-korea/d/d-id/1129325?)

[Spread worms on social media.](https://news.softpedia.com/news/CSRF-Worm-Released-on-Twitter-158085.shtml)

[Install malware on mobile phones.](https://www.zdnet.com/article/bug-allowed-full-takeover-of-samsung-user-accounts/)



#### REST

[Representation State Transfer (REST)](https://www.hacksplaining.com/glossary/rest) is a series of design principles that assign certain types of action (view, create, delete, update) to different [HTTP verbs](https://www.hacksplaining.com/glossary/http). Following REST-ful designs will keep your code clean and help your site scale. Moreover, REST insists that GET requests are used only to *view* resources. Keeping your GET requests side-effect free will limit the harm that can be done by maliciously crafted URLs–an attacker will have to work much harder to generate harmful POST requests.

#### Anti-Forgery Tokens

This is called an **anti-forgery** token. Each time your server renders a page that performs sensitive actions, it should write out an anti-forgery token in a hidden HTML form field. This token must be included with form submissions, or AJAX calls. The server should validate the token when it is returned in subsequent requests, and reject any calls with missing or invalid tokens.

#### Ensure Cookies are sent with the SameSite Cookie Attribute

A value of `Strict` will mean than *any* request initiated by a third-party domain to *your* domain will have any cookies stripped by the browser. This is the most secure setting, since it prevents malicious sites attempting to perform harmful actions under a user’s session.

A value of `Lax` permits GET request from a third-party domain to *your* domain to have cookies attached - but *only* GET requests. With this setting a user will not have to sign in again to your site if the follow a link from another site (say, Google search results). This makes for a friendlier user-experience - but make sure your GET requests are side-effect free!

#### Include Addition Authentication for Sensitive Actions



## Reflected XSS

- **Search results** - does the search criteria get displayed back to the user? Is it written out in the page title? Are you *sure* it is being escaped properly?
- **Error pages** - if you have error messages that complain about invalid inputs, does the input get escaped properly when it is displayed back to the user? Does your 404 page mention the path being searched for?
- **Form submissions** - if a page POSTs data, does any part of the data being submitted by the form get displayed back to the user? What if the form submission is rejected – does the error page allow injection of malicious code? Does an erroneously submitted form get pre-populated with the values previously submitted?

#### Escape Dynamic Content

#### Whitelist Values

#### Implement a Content-Security Policy



## File Upload Vulnerabilities

**Sophisticated hackers typically exploit a combination of vulnerabilities when attacking your site – uploading malicious code to a server is step one in the hacker playbook.** The next step is finding a way to execute the malicious code.

Even [big companies](https://threatpost.com/ebay-fixes-file-upload-and-patch-disclosure-bugs/111898) fall foul to this vulnerability, particularly if they are running complex, legacy code bases.

#### Segregate Your Uploads

File uploads are generally intended to be inert. Unless you are building a very particular type of website, you are typically expecting images, videos, or document files, rather than executable code. If this is the case, making sure uploaded files are kept separate from application code is a key security consideration.

Consider using [cloud-based storage](http://aws.amazon.com/s3/) or a [content management system](https://www.hacksplaining.com/glossary/content-management-systems) to store uploaded files. Alternatively, if you are sure of your ability to scale your backend, you could write uploaded files to your database. Both of these approaches prevent accidental execution of an executable file.

#### Ensure Upload Files Cannot Be Executed

#### Rename Files on Upload

#### Validate File Formats and Extensions

#### Validate the Content-Type Header

#### Use a Virus Scanner



## Open Redirects

Redirects are a useful function to have when building a website. If a user attempts to access a resource before they are logged in, it is conventional to redirect them to the login page, put the original URL in a query parameter, and after they have logged in, automatically redirect them to their original destination. This type of functionality shows you are putting thought into the user experience, and is to be encouraged. However, you need to be sure anywhere you do redirects, they are done safely – otherwise you are putting your users in harm’s way by enabling [phishing attacks](https://www.hacksplaining.com/glossary/phishing).

#### Disallow Offsite Redirects

Make sure all redirect URLs are **relative paths** – i.e. they start with a single `/` character. (Note that URLs starting with `//` will be interpreted by the browser as a protocol agnostic, absolute URL – so they should be rejected too.)

If you *do* need to perform external redirects, consider whitelisting the individual sites that you permit redirects to.



#### Check the Referrer When Doing Redirects

Redirects to URLs passed in query parameters should only be triggered by pages on your site.

As a second layer of defense, check that the `Referer` in the HTTP request matches your domain whenever you perform a redirect.

## Unencrypted Communication

Insecure Wi-Fi hotspots, [as illustrated in our exercise](https://www.hacksplaining.com/exercises/unencrypted-communication), are just one way enterprising hackers have found to take advantage of unencrypted communication. They may also try to sniff traffic within your network, and if they get access, inspect traffic going through compromised edge devices.

#### **Buy a certificate, install it, and configure your web server to use it.**

It’s really as simple as that. Web servers are typically able to serve the same content over HTTP (on port 80) and HTTPS (on port 443). Any non-trivial website should use HTTPS. [Facebook](https://www.facebook.com/notes/facebook-engineering/secure-browsing-by-default/10151590414803920/) and [Twitter](https://blog.twitter.com/2011/making-twitter-more-secure-https) use HTTPS by default, and this a good example to follow.

**But make sure you know how to force your web server to elevate to a secure connection, and do so whenever a user is authenticating or establishing a session.**

A common way of enforcing this is to make sure that cookies are set to `secure` – that way, sessions can *only* be established over HTTPS.



## User Enumeration

#### Login

- Make sure to return a **generic** “No such username or password” message when a login failure occurs.
- Make sure the **HTTP response**, and the **time taken** to respond are no different when a username does not exist, and an incorrect password is entered.

#### Password Reset

- Make sure your “forgotten password” page does not reveal usernames.
- If your password reset process involves sending an email, have the user enter their email address. Then send an email with a password reset link if the account exists.

 #### Registration

- Avoid having your site tell people that a supplied username is already taken.
- If your usernames are email addresses, send a password reset email if a user tries to sign-up with an existing address.
- If usernames are *not* email addresses, protect your sign-up page with a CAPTCHA.

#### Profile Pages

- If your users have profile pages, make sure they are only visible to other users who are already logged in.
- If you hide a profile page, ensure a hidden profile is indistinguishable from a non-existent profile.

 

 

## Password Mismanagement

#### Use Third-Party Authentication if Possible

#### Ensure Password Complexity

#### Allow Password Resets via Email

#### Confirm Old Password On Reset

#### Prevent Brute-Forcing

#### Store Passwords With A Strong Hash, Salted

**Passwords should always be stored as salted hashes.**

#### Timeout Sessions After Inactivity, and Provide a Logout Function

#### Use HTTPS for Secure Communication



## Email Spoofing

- **Steal their credentials** by sending “phishing” messages.
- **Trick them into falling for online scams** by abusing the trust they have in your site.
- **Spread malware** by sharing malicious attachments.

#### SPF

- **Implement the Sender Policy Framework (SPF):** publish a [DNS record](https://www.cloudflare.com/learning/dns/dns-records/) to explicitly state which servers are allowed to send email from your domain.
- **Implement Domain Key Identified Mail (DKIM):** use a [digital signature](https://www.hacksplaining.com/glossary/digital_signatures) to prove that outgoing email was legitimately sent from your domain, and that it wasn’t modified in transit.

#### DMARC

There is also an emerging umbrella standard called **DMARC** (“Domain-based Message Authentication, Reporting & Conformance”) that you should be aware of. [Read more about DMARC here](https://dmarc.org/overview/).

### configuration

mplementing SPF and DKIM requires publishing new DNS records and making configuration changes to your technology stack - consult the documentation for your email sending service or software for details. Here are the relevant documentation links for some of the more common methods of sending email.

#### Transactional Email Services

#### Email Marketing Services

#### Mail Transfer Agents





## Malvertising

- **Malicious downloads,** including ransomware. “Drive-by” downloads don’t even require the user to click on an advert - simply viewing the page may be enough to deliver the payload. Malware is usually delivered through vulnerable versions of Flash or Adobe Acrobat.
- **Redirects to phishing sites** that attempt to steal a user’s credentials.
- **Scareware** - adverts designed to trick a user into downloading unnecessary and potentially dangerous software, such as fake antivirus protection.
- **Browser lockers** - malware that locks up the browser, often posing as a security alert.



### Protection

- **Working with reputable ad networks.** Choose networks that are certified by e.g. Google. If you are evaluating a new ad network, see if they have any existing big-name clients. Avoid advertising networks that use deceptive practices pop-ups and pop-under windows.
- **Performing due diligence on agencies and advertisers.** Restrict your advertising to relevant market segments, and if your ad networks permits it, consider individually whitelisting advertisers.
- **Implementing a content security policy.** Implementing a [Content-Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) will help control what domains can host content used in your web-pages. Unfortunately, many advertising toolkits (e.g. Google Adsense) cannot be restricted in this fashion - so you may have to create a “soft” whitelist using the `Content-Security-Policy-Report-Only` header, and monitor unexpected domains.
- **Using client-side error reporting tools.** Tools for recording errors in the browser - like [Sentry](https://sentry.io/for/javascript/), [TrackJS](https://trackjs.com/), [Rollbar](https://rollbar.com/) and [Airbrake](https://airbrake.io/) - will help you detect unexpected and anomalous behavior that could indicate a malvertising infection.
- **Logging out-going URLs.** Capturing click-strings for adverts will help with forensic analysis in the case of a malvertising outbreak.



## Logging And Monitoring

https://www.hacksplaining.com/prevention/logging-and-monitoring

## Buffer Overflows

| Insecure Function | Secure Alternative |
| :---------------- | :----------------- |
| `gets()`          | `fgets()`          |
| `strcpy()`        | `strncpy()`        |
| `strcat()`        | `strncat()`        |
| `sprintf()`       | `snprintf()`       |

It’s pretty rare for web-developers to write low-level code in languages like C or C++, so the biggest risk of buffer overflows for must of us in the applications we use.

##### Web Servers

Most websites are deployed using a *web server* to serve static content. (This is distinct from the *application server* that executes dynamic content.) The three most common web-servers are:

- Apache HTTP Server
- Microsoft Internet Information Services (IIS)
- Nginx

Each of these has been found to be vulnerable to buffer overflows at different times. Web-server vendors are very quick to patch vulnerabilities, so the key to keeping yourself secure is deploying security patches as soon as they become available.

##### Operating Systems and Language Runtimes

Buffer overflow attacks have been launched against websites by taking advantage of vulnerabilities in operating systems and language runtimes. The [Heartbleed](http://heartbleed.com/) attack took advantage of a serious vulnerability in the OpenSSL cryptographic software library that Linux-based web-servers use to encrypt SSL/TLS traffic. Similarly, security researchers have discovered vulnerabilities in various functions in the PHP runtime which allow attackers to launch buffer overflow attacks remotely by crafting malicious input.

##### Remediation

To avoid being exposed to buffer overflow vulnerabilities in the applications you use, you need to keep them up-to-date with the latest security patches. These are the key things to need to do:

- **Automate your build and deployment process.** You need to know which versions of each application your are running on each server. This means writing deployment scripts for web-servers and language runtimes, and retaining copies of deployment logs.
- **Keep on top of security bulletins.** Make sure your team is on the lookout for security announcements for the applications you use. Sign up for mailing lists, join forums, and follow software vendors on social media.
- **Deploy security patches as soon as they become available!** Hackers will find ways to take advantage of security vulnerabilities as soon as they are made public, so make sure you are not amongst the target audience.


























