---
layout: post
title: Bulk email sending using Mutt via CLI
cover-img: /assets/img/bulk-mail.png
thumbnail-img: /assets/img/bulk-mail.png
# gh-repo: daattali/beautiful-jekyll
# gh-badge: [star, fork, follow]
tags: [terminal, cli, mail, tools]
comments: true
---
Recently I faced with the task: I needed to send thousands of e-mails with a survey link to all students in university.
The problem of mass mailing from a desktop client is that the whole students list is visible in the CC field.
I solved this problem through a simple command in terminal using mutt.
>  **Mutt** is a [text-based](https://en.wikipedia.org/wiki/Text_user_interface) [email client](https://en.wikipedia.org/wiki/Email_client) for [Unix-like](https://en.wikipedia.org/wiki/Unix-like) systems. It was originally written by Michael Elkins in 1995 and released under the [GNU General Public License](https://en.wikipedia.org/wiki/GNU_General_Public_License) version 2 or any later version.

Okay, lets get to work. First we need to create a configuration file:

`$ vi ~/.muttrc`

Now we need to configure the name and the address:

```conf
set realname=«John Smith»
set from=«jsmith@whitehouse.gov» 
set use_from=yes
```

Now create a body:

`$ vi ~/body`

Of course, the e-mail body is in rich-html:

    <!DOCTYPE html>
    <html>
    <head>
    <meta http-equiv=«Content-Type» content=«text/html; charset=utf-8»> </head> 
    <body> 
    <p><font color="#2E7BE4"><em><strong>Hello!</strong></em></font></p> <p align="justify">This is the email-body in HTML</p>
    <p><font color="#2E7BE4"><strong><i>Best wishes</i></strong><br></font>
    </p>
    </body>
    </html>

Now we need a recipients list:

`$ vi ~/list`

```conf
email@mail.com
email2@mail.com
...
```

Okay, now we are ready to send our e-mail:

`$ for I in `cat list`; do cat body | mutt -e "set content_type=text/html" -a "attachment.pdf" -s "E-Mail Subject" -- $I < body; echo $I; sleep 3; done`

Now mutt will send rich html e-mails every three seconds to recipient list we created.

<style>.bmc-button img{height: 34px !important;width: 35px !important;margin-bottom: 1px !important;box-shadow: none !important;border: none !important;vertical-align: middle !important;}.bmc-button{padding: 7px 15px 7px 10px !important;line-height: 35px !important;height:51px !important;text-decoration: none !important;display:inline-flex !important;color:#ffffff !important;background-color:#5F7FFF !important;border-radius: 5px !important;border: 1px solid transparent !important;padding: 7px 15px 7px 10px !important;font-size: 22px !important;letter-spacing: 0.6px !important;box-shadow: 0px 1px 2px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;margin: 0 auto !important;font-family:'Cookie', cursive !important;-webkit-box-sizing: border-box !important;box-sizing: border-box !important;}.bmc-button:hover, .bmc-button:active, .bmc-button:focus {-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;text-decoration: none !important;box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;opacity: 0.85 !important;color:#ffffff !important;}</style><link href="https://fonts.googleapis.com/css?family=Cookie" rel="stylesheet"><a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/kip0d"><img src="https://cdn.buymeacoffee.com/buttons/bmc-new-btn-logo.svg" alt="Buy me a coffee"><span style="margin-left:5px;font-size:28px !important;">Buy me a coffee</span></a>
