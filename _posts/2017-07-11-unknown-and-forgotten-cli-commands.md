---
layout: post
title: Unknown and forgotten CLI commands
cover-img: /assets/img/cli.png
thumbnail-img: /assets/img/cli.png
# gh-repo: daattali/beautiful-jekyll
# gh-badge: [star, fork, follow]
tags: [terminal, cli, command line, linux]
comments: true
---
Each developer should master his/her skills of working with the command line.

Being physically near a computer is not always possible, so you have to connect remotely. Indeed, GUI may well handle this, but it often works slower than a terminal (after all, this is just a text exchange).

Regardless of whether you are a beginner or a guru, I’m sure you‘ll find useful information among following tips and recommendations.

### 1. **man**

Let’s start with the simple, man command, which means manual. If you want to read about another command, just type:

    man [*command*]

You can read the man manual:

    man man

“I already knew that!” you’ll say. However, I would like to draw your attention to some of its features.

You can read about the ASCII table, type the following:

    man ascii

![](https://cdn-images-1.medium.com/max/2000/1*B6fCK-bR5kSnRpzWxyo28g.png)

What is bigger: pico- or femto- ?

    man units

![](https://cdn-images-1.medium.com/max/2000/1*rUtfJiB7zTR4N4f0AMgNMQ.png)

### 2. **cd -**

If you accidentally changed the directory, you can just return to the last one by typing:

    cd -

![](https://cdn-images-1.medium.com/max/2000/1*tRenufRQHuxljOZ-HPv_ag.png)

### 3. sudo !!

![](https://cdn-images-1.medium.com/max/2000/1*XlDjwO5LuWdhAnHk5Dhiww.png)

Sudo is a very important command in Unix systems. It executes a command with administrator rights.

If you typed a command without sudo, and then it turned out that it is necessary, type simply:

    sudo !!

And it will start with administrator rights.

### 4. **[space] command**

Experienced users may know that the history of commands is stored in the ~ / .bash_history file.
If you don’t want to write the command to history file, just type a space before the command.

    [space] [command]




### 5. jot

The jot utility is used to print out increasing, decreasing, random, or redundant data, usually numbers, one per line.

    jot [number of digits] [starting from]

If you specify one argument, jot will generate numbers from 1 to the value of the argument.

![](https://cdn-images-1.medium.com/max/2000/1*OVN7W943a_voIimbfq3BeA.png)

### 6. df

Simply shows the free space on a drive

![](https://cdn-images-1.medium.com/max/2000/1*67z2ra0k_gdbAfhXnWWFIg.png)

### 7. pkill

Pkill (or process kill) terminates the running process. This command is especially useful when the application does not respond:

    pkill [application_name]

Be careful, when you launching this command on a remote machine — you can lose important data.

### 8. cal

Displays a calendar and the date of easter:

![](https://cdn-images-1.medium.com/max/2000/1*SbCHelLR796vIBBOqopG7A.png)

### 9. w

The w command shows who has logged on to the system, along with other useful information such as the operating time or CPU load.

### 10. yes

Funny command that prints the string several times:

    yes [string]

Use it to confuse your friends. Attention, the only way to stop it is CTRL + C (or close the terminal).

### 11. nl

Nl numbers the lines. Most useful when using it as an argument:

![](https://cdn-images-1.medium.com/max/2000/1*_-lXAHl2dUlR1H5vtefKxg.png)

### 12. Ctrl+R Autocomplete

An easy way to explore your command history is to press Ctrl+R and type the beginning of the wanted command. Cli will autocomplete your request.

![](https://cdn-images-1.medium.com/max/2000/1*paB8vS7ZWKh5KXiyoUF8PA.png)

I entered the `lsof` command to get the PID of the applications that running at ports 35729 and 9005. As you can see, the terminal autocompleted my request. Truly a killer feature.

Thank you for attention. You can share useful commands and cli tricks in discussion.

<style>.bmc-button img{height: 34px !important;width: 35px !important;margin-bottom: 1px !important;box-shadow: none !important;border: none !important;vertical-align: middle !important;}.bmc-button{padding: 7px 15px 7px 10px !important;line-height: 35px !important;height:51px !important;text-decoration: none !important;display:inline-flex !important;color:#ffffff !important;background-color:#5F7FFF !important;border-radius: 5px !important;border: 1px solid transparent !important;padding: 7px 15px 7px 10px !important;font-size: 22px !important;letter-spacing: 0.6px !important;box-shadow: 0px 1px 2px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;margin: 0 auto !important;font-family:'Cookie', cursive !important;-webkit-box-sizing: border-box !important;box-sizing: border-box !important;}.bmc-button:hover, .bmc-button:active, .bmc-button:focus {-webkit-box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;text-decoration: none !important;box-shadow: 0px 1px 2px 2px rgba(190, 190, 190, 0.5) !important;opacity: 0.85 !important;color:#ffffff !important;}</style><link href="https://fonts.googleapis.com/css?family=Cookie" rel="stylesheet"><a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/kip0d"><img src="https://cdn.buymeacoffee.com/buttons/bmc-new-btn-logo.svg" alt="Buy me a coffee"><span style="margin-left:5px;font-size:28px !important;">Buy me a coffee</span></a>

