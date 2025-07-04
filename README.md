# CLAI
This project is called CLAI which is just a play on CLI + AI.   

### Licensing
This project is provided with the Creative Commons BY License (CC-BY)
* https://creativecommons.org/share-your-work/cclicenses/
###   
Written by: David C. Brown
Written in: Python 3.9+ /w uv
When: 2025

## Reasoning
The reason I wrote this is I'm a Linux profession and I've been messing
around with AI and even N8N recently.  I occasionally need to double check
commands that I don't use very on the command line often and I use search 
engines and of late, AI.

1 + 1 = 2 

Well, lets goof around and add AI LLM on the Linux command line!  

# Project Requirements:
 1. Command line access to a *nix based OS.
 2. N8N Workflow Automation Software
 3. Access to an LLM API
 4. A webhook workflow in N8N
 5. Python 3.9+ with uv installed.
 6. An .env file or configured environmental variables.

## What this won't do.
This document will not teach you to setup N8N

The current workflow in n8n is as follows:
Webook Trigger -> AI Agent -> LLM Attached -> Prompt Memory -> Respond to Webhook

You can easily add other tools to this workflow and make this far more powerful using
N8N workflows.  You can trigger all kinds of wholesome goodness!

## How it Works
This is written in Python and utilizes uv for package management / environment management

It is a python script, but I've striped the .py off of it and use special execution so it can 
act like a cli utility, yet still utlize uv and accept the parameters without the need to call
uv on the cli.

To do this, you do not use python at the top of the script, instead you do the following:
```
#!/usr/bin/env -S uv run  --script
```
It also supports Markdown (Python Rich library) since LLMs are known to provide responses that can have markdown in them.

## Environmental Variables
It requires a few environmental variables.  You can either set them yourself, or use an environmental
config file.

Currently, this one is set to HOME/.env/.ask.env and you MUST update the "ask" script to point at your
env file.  I've utulized os.path.expanduser so that the path can use a ~ for home.

```
snip...
from dotenv import load_dotenv
from rich.console import Console
from rich.markdown import Markdown

CLAI_ENV = os.path.expanduser('~/.env/.ask.env')   <---- Update this to point to your env file.
snip...
```

This is an example of the env file.
Example:
```
# DEPLOYMENT TEST/PROD  This determines which WEBHOOK to use basedon the deployment's state
DEPLOYMENT="prod"
# Update the webhook URLs to match your n8n workflow
N8N_TEST_WEBHOOK="http://[N8N_HOST]/webhook-test/yadda-yadda"
N8N_PROD_WEBHOOK="https://[N8N_HOST]/webhook/yadda-yadda"
# Do not touch the JSON_PAYLOAD, it is a template.
JSON_PAYLOAD='{"sessionId": "", "action": "sendMessage", "chatInput": ""}'
# The session ID (uuid works) should be unquie as it is used to manage ai chat history.
SESSION_ID='CLI-AI'
#SESSION_ID='yadda-yadda-yadda-yadda-batman!'
```
I highly recommend if you use .env files like this, you protect them.
```
chmod 700 /path/to/.env
chown myuser:mygroup /path/to/.env
```
There are environmental variable vaults out there.  
I may make a env vault tool in the future.  

## Adding "ask" to your path
I personally keep a bin directory in my home directory ~/bin
I add this to my path and then just throw "ask" from this repo 
into that bin directory and ensure it's set to executable
```
chmod +x ~/bin/ask
```
# Running CLAI / ASK
```
$ ask What is the capital of Texas?
The capital of Texas is Austin.

$ ask How do you delete all file that are over 30 days old?
You can delete all files over 30 days old in a directory using the find command in Linux terminal:

 1 Open terminal.
 2 Navigate to the directory where you want to delete the files. Use cd directory-path. Replace "directory-path" with the path of your directory.
 3 Use the find command in this format: find . -type f -mtime +30 -exec rm {} \;

Here, find is the command to search for files in a directory hierarchy, . (a period) tells it to search in the current directory, -type f tells it to only look for files, -mtime +30 specifies that the files should be 30 days old or
older, and -exec rm {} \; tells it to execute the rm (remove) command on each file it finds that meets these criteria.

Please make sure to back up any important files before performing these kinds of operations. A single mistake can lead to permanent loss of data. You might want to use -exec echo rm {} \; in your initial run; it will show you what will
be deleted without actually deleting anything. Once you're sure, you can run the command without echo.

This is for systems like Unix, Linux, macOS, etc. It would be a different command for Windows.

$ ask What database is better, Oracle or PostgreSQL?
Whether Oracle or PostgreSQL is "better" largely depends on your specific needs as both databases have their own strengths.

Oracle:

 • Oracle is an object-relational database management system. It's produced and marketed by Oracle Corporation.
 • It's fully loaded with a variety of features and it is very efficient for large scale projects.
 • It offers extensive documentation and has a large community of users.
 • It provides comprehensive solutions for managing and manipulating the full cycle of data.
 • However, it's a commercial product and can be expensive depending on the scale of its usage.

PostgreSQL:

 • PostgreSQL is a powerful, open source object-relational database system.
 • Unlike Oracle, it is free to use. Its source code is available under an open source license, which allows use of PostgreSQL at no cost.
 • It's highly programmable, and therefore extendible, with custom procedures that are created using different programming languages.
 • It's well-suited to work with various types of data including text, images, sounds and video, and includes programming interfaces for C/C++, Java, Perl etc.
 • However, it may not be as scalable as Oracle for larger databases.

In conclusion, your choice depends on your requirements. If you are a startup that needs a free and cost-effective solution, PostgreSQL might be better for you. If you are a large corporation that requires extremely high scalability and
has a large budget, Oracle may be more suitable. Be sure to thoroughly evaluate your business requirements before making a choice.
```
