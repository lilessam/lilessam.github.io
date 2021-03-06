---
layout: post
title: How to make a zip password cracker with Python
excerpt: "In this quick tutorial I'm gonna show you how to make a zip file password cracker using our beloved Python !"
date: 2017-01-06
---
In this quick tutorial I'm gonna show you how to make a zip file password cracker using our beloved Python !
If you have a `zip file` downloaded from a website called `www.domain.com` for example. The password of the file might be `domain.com`, `www.domain.com` or even `http://www.domain.com`. So There are many guesses. And you'll not be okay if you try them all manually. So the idea of our program to type guessing words in a file and it will use all the odds to unzip the file.

First of all, we will include some modules like `optparse`, `zipfile`, `atexit`. And we will include `threading` method from `Thread` module.

{% highlight Python %}
import optparse
import zipfile
import atexit
from threading import Thread
{% endhighlight %}

Then we will make a tiny function that unzips the file using `zipfile` module to call it whenever we want.

{% highlight Python %}
def extract_it(zipfile, password):
    try:
        zipfile.extractall(pwd=password)
        print "[+] Password is: " + password + "\n" + "File is extracted successfully"
    except:
        pass
{% endhighlight %}

Now in our Main function we will get the user parameters using `optparse`
We need to get two parameters, one for our zip file and another for the passwords file.

{% highlight Python %}
def main():
    parser = optparse.OptionParser("usage %prog "+\
			"-z <zipfile> -d <dicctionary>")
    parser.add_option('-z', dest='zname', type='string',\
				help='Specify the archive file')
    parser.add_option('-d', dest='dname', type='string',\
				help='Specify passwords file')
    (options, arg) = parser.parse_args()
{% endhighlight %}

Then we will check the parameters if the user did enter it correctly, and if not we will show him how to use the program.

{% highlight Python %}
def main():
    parser = optparse.OptionParser("usage %prog "+\
			"-z <zipfile> -d <dicctionary>")
    parser.add_option('-z', dest='zname', type='string',\
				help='Specify the archive file')
    parser.add_option('-d', dest='dname', type='string',\
				help='Specify passwords file')
    (options, arg) = parser.parse_args()
    if (options.zname == None) | (options.dname == None):
    	print parser.usage
    	exit(0)
{% endhighlight %}

Now if the parameters are right we will loop through the passwords file to try every guess and try to unzip the file using `extract_it` function.

{% highlight Python %}
def main():
    parser = optparse.OptionParser("usage %prog "+\
			"-z <zipfile> -d <dicctionary>")
    parser.add_option('-z', dest='zname', type='string',\
				help='Specify the archive file')
    parser.add_option('-d', dest='dname', type='string',\
				help='Specify passwords file')
    (options, arg) = parser.parse_args()
    if (options.zname == None) | (options.dname == None):
    	print parser.usage
    	exit(0)
    else:
    	zname = options.zname
    	dname = options.dname
    zFile = zipfile.ZipFile(zname)
    passFile = open(dname)

    for line in passFile.readlines():
    	password = line.strip('\n')
    	t = Thread(target=extract_it, args=(zFile, password))
    	t.start()
{% endhighlight %}

At the end we will show the user a simple message using `atexit` module:

{% highlight Python %}
def exit_handler():
    print "That's all I could do :(";

atexit.register(exit_handler)
if __name__ == '__main__':
	main()
{% endhighlight %}

And this is how to use the application: `Python ZipCracker.py -z file.zip -d passwords.txt`

This application is available on this [__GitHub Repository__](https://github.com/lilessam/ZipCracker)
