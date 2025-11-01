









## When Not to Use Python: The Case for Bash Shell Scripting

Python is a great programming language, and you can do a lot of awesome stuff with it. For example, if you need to create applications that process complex data sets or to perform complex math operations, Python is a great choice. Python also allows you to create multi-threaded applications that run very efficiently. However, you might at times find that Python is more than you need, or more than you can easily learn. So, for jobs that don’t rise to the level of complexity for which Python would be ideal, which would include many administrative tasks on Linux, Unix, or Unix-like servers and devices, you might want to consider shell scripting instead. Let’s look at some specific reasons.

## Shells are universal, Python isn’t

To begin with, Python might not be installed on every workstation, server, IoT device, or container that you need to administer. It might be possible to install Python in some cases, but not always. On the other hand, every Linux, Unix, or Unix-like system that you’ll encounter has a shell already installed. The most common shell on Linux systems is bash. You’ll find Bourne Shell on BSD-type systems, and lightweight shells such as ash or dash on Linux for IoT devices. Now, let’s say that you have a Linux-based IoT device and you need to parse through its webserver logs.  The tools you’ll need to do this with shell scripting are already there, but Python likely isn’t. On a low-resource device, you might not even have enough storage space to install Python.

## Shell script portability versus Python portability

The difference between bash and the other shells that I mentioned is that bash has some advanced features that the other shells lack. If you know that you’ll only need to run your scripts in a bash environment, then you can definitely take advantage of the extra bash features. But, by avoiding the bash-specific features, you can create shell scripts that will run on a wide variety of shells, including bash, ash, dash, or Bourne Shell. Fortunately, that’s not as hard as it would seem. For example, you can create variable arrays in bash, but not in the other shells. If you need cross-shell portability but also need the benefits of using an array, you can easily create a construct that simulates an array, and that has the same functionality. It’s easy-peasy once you know how.

One portability problem with Python involves Python’s use of programming libraries that might or might not be installed on every device on which the Python script needs to run. In fact, you might have encountered this problem yourself if you’ve ever downloaded a Python script from GitHub. If you’re not a Python expert, it might take you a while to figure out how to install all of the required libraries. With shell scripting, you don’t need to worry about libraries, because shell scripts use the command-line utilities that already come installed on pretty much every Linux, Unix, or Unix-like system. Another problem is that scripts that were created for the old Python 2 aren’t always compatible with the new Python 3.

Next, let’s talk about something that’s especially important to me personally.

## The Shell Scripting Learning Curve versus the Python Learning Curve

If you’re a DevOps person, you’ve likely already mastered Python. But, if you’re more into systems administration, there’s a good chance that you haven’t  had much experience with Python. Fear not, because even if you’re lousy with learning programming languages, as I am, you can still learn how to do some awesome things with old-fashioned shell scripting. Even if you do know Python, you might find that certain jobs can be accomplished more quickly and easily with shell scripting than with Python. For example, here’s the script that I use to update and shut down the OpenMandriva workstation that I’m using right now:

```
#!/bin/bash
dnf -y distro-sync && shutdown now
```

All this shell script contains is just the commands that I would normally run from the command line. With shell scripting, no coding skill at all is required for this.

Working with text files is way easier with shell scripting. Let’s take this text file with a listing of classic automobiles:

```
plymouth        satellite       1970    154     600
plymouth        fury            1970    73      2500
plymouth        breeze          1996    116     4300
chevy           malibu          2000    60      3000
ford            mustang         1965    45      10000
volvo           s80             1998    102     9850
ford            thunderbird     2003    15      3500
chevy           malibu          1999    50      3500
bmw             325i            1985    115     450
bmw             325i            1985    60      1000
honda           accord          2001    30      6000
ford            taurus          2004    10      17000
toyota          rav4            2002    180     750
chevy           impala          1985    85      1550
ford            explorer        2003    25      9500
jeep            wrangler        2003    54      1600
edsel           corsair         1958    47      750
ford            galaxie         1964    128     60
```

The fields in this file represent the make, model, year, mileage in thousands of miles, and U.S. dollar value of each car. Now, let’s say we want to sort this file alphabetically and save the output to a new file. Here’s how you could do it with Python:

```
#!/usr/bin/python

def sort_file_content(in_path, out_path):
    lines = []

    with open(in_path) as in_f:
        for line in in_f:
            lines.append(line)

    lines.sort()

    with open(out_path, 'w') as out_f:
        for line in lines:
            out_f.writelines(line)

if __name__ == "__main__":
    input_file = "autos.txt"
    output_file = "sorted_autos.txt"
    sort_file_content(input_file, output_file)
```

Here’s how you’d do it with a shell script:

```
#!/bin/bash
sort autos.txt > sorted_autos.txt
```

Either way, we get the same results, which look like this:

```
bmw             325i            1985    115     450
bmw             325i            1985    60      1000
chevy           impala          1985    85      1550
chevy           malibu          1999    50      3500
chevy           malibu          2000    60      3000
edsel           corsair         1958    47      750
ford            explorer        2003    25      9500
ford            galaxie         1964    128     60
ford            mustang         1965    45      10000
ford            taurus          2004    10      17000
ford            thunderbird     2003    15      3500
honda           accord          2001    30      6000
jeep            wrangler        2003    54      1600
plymouth        breeze          1996    116     4300
plymouth        fury            1970    73      2500
plymouth        satellite       1970    154     600
toyota          rav4            2002    180     750
volvo           s80             1998    102     9850
```

I think you see that doing this with shell scripting is way faster and easier.

Finally, let’s see how shell scripting can help us with cloud operations.

## Shell scripting for cloud operations

Let’s say that you have a web server that’s running on either a VPS or a remote IoT device, and you want a list of IP Addresses of clients that have accessed it, along with status codes and number of bytes transferred. Here’s a Python script that you might use for that:

```
#!/usr/bin/python

import sys
from dataclasses import dataclass

@dataclass(frozen = True)
class LogEntry:
    ip_address : str
    n_bytes : int
    status_code : int

def main(args):
    file_path = args[0]
    entries = parse_log_file(file_path)
    for e in entries:
        print(e)

def parse_log_file(file_path):
    try:
        with open(file_path) as log_file:
            return [parse_log_line(line) for line in log_file]
    except OSError:
        abort(f'File not found: {file_path}')

def parse_log_line(line):
    try:
        xs = line.split()
        return LogEntry(xs[0], int(xs[9]), int(xs[8]))
    except IndexError:
        abort(f'Invalid log file format: {file_path}')

def abort(msg):
    print(msg, file = sys.stderr)
    exit(1)

if __name__ == '__main__':
    main(sys.argv[1:])
```

Here’s a bash script that does the same thing:

```
#!/bin/bash

echo "ip address, status code, number of bytes"
cut -d" " -f 1,10,9 /var/log/httpd/access_log
```

That’s right. A simple, two-line shell script can take the place of that entire Python script. At any rate, the output of the shell script will look something like this:

```
ip address, status code, number of bytes
192.168.0.20 403 5760
192.168.0.20 403 199
192.168.0.20 200 4194
192.168.0.20 200 5714
192.168.0.20 404 196
192.168.0.18 403 5760
192.168.0.18 403 199
192.168.0.18 200 4194
192.168.0.18 200 5714
192.168.0.18 404 196
```

You can also create shell scripts to automate management of your cloud services. For example, here’s a script that can start or stop an EC2 instance on Amazon Web Services:

```
#!/bin/bash
read -p "Enter the EC2 instance ID: " INSTANCE_ID

read -p "Do you want to start or stop the instance? (start/stop): " ACTION

if [[ "$ACTION" == "start" ]]; then
  echo "Starting instance $INSTANCE_ID..."
  aws ec2 start-instances --instance-ids $INSTANCE_ID
elif [[ "$ACTION" == "stop" ]]; then
  echo "Stopping instance $INSTANCE_ID..."
  aws ec2 stop-instances --instance-ids $INSTANCE_ID
else
  echo "Oops! Please type 'start' or 'stop'."
fi
```

When you run the script, just type in the instance ID the for the first prompt, and then type either “start” or “stop” at the second prompt. This is a lot easier than typing the entire aws command every time you need to start or stop an instance. You can automate most any other aws task in the same manner.




[Buy The Ultilmate Linux Shell Scripting Guide now, via my Amazon link!](https://www.amazon.com/Ultimate-Linux-Shell-Scripting-Guide/dp/1835463576?&linkCode=ll1&tag=civicsandpolitic&linkId=4eb34b1ccafd9edcf33aa8f61e4cbb31&language=en_US&ref_=as_li_ss_tl)


## Conclusion

To be sure, shell scripting has its limitations. For large, complex programs that require high performance, Python, or perhaps even a compiled language such as C, would be much better. But as I’ve just demonstrated, there are many times when bash scripting is definitely a much better choice.