# Snort Setup and Basic Configuation:

## Installing snort --> sudo apt install snort

Proceed to ping between a machine on the same network and your host machine to ensure a proper connection between the two. If you have a firewall such as ufw installed you may need to disable it before continuing.

We will be creaing a local configuration/rules file in a separate directory for simplicity.

Create a new directory, a .rules file, and another directory for your logs to be placed.

To utilize this directory with snort we will need to specify the specific config/rules file that we want to use and the location that we want to place our logs

```
snort -l ./logs -c rulefile.rules
```

### **Note** Depending on your ethernet configuation we may need to specify what connection we want to use. In my case I had to use eth1 as snort defaults to eth0.
```
snort -l ./logs -c fullstack.rules -i eth1
```
- **Note** This will not provide a continuous console window and will terminate very quickly. If you are utilizing this method be sure to be running a ping or scan towards your host machine before execution.

## Rules
### Running this command as is will not provide any alerts or output to the user. This is not useful so we will provide a rule for an alert for ICMP requests on our host machine.

**in your rules file. I will be using fullstack.rules, type the following**
```
alert icmp any any -> **YOUR HOST IP** any (msg: "icmp packet recieved"; sid:1000001;)
```
- **alert** is the type of feedback we are requesting snort to give us
- **icmp** is the type of data that is expected
- **any any** is the expected ip address in this case it will be any and the source port which will also be any
	- Similarly our host ip on listening on any port
- **msg** is the message we want back from our alert, perferebly something useful for future reference
- **sid** is our unique identifer where the number iself is not incredibly important, but necessary for the proper fucntion of our alert

### In this scenario we will be running a continuous interface with snort by adding a -A console to our command
```
snort -l ./logs -c fullstack.rules -i eth1 -A console
```
Now run a ping from our external to our host machine and we will see ICMP packets trigger our alert through the snort interface

- **Note** A log file should be provided in your local logs directory

These log files can either be analyzed with wireshark or running the command
```
snort -r logfile.log.123456
```

## After analyzing the results, we will continue to create more alerts for specific nmap scans
- **Note** You can place multiple rules in a single config/rules file. 
	- I will be placing 3 more rules in my fullstack.rules file. This will not disable previous rules but rather create new bounds for alerts to be triggered on our host machine.
```
alert tcp any any -> **YOUR HOST IP** 22 (msg: "Nmap NULL Scan"; flags:0; sid:1000002;)

alert tcp any any -> **YOUR HOST IP** 22 (msg: "Nmap FIN Scan"; flags:F; sid:1000003;)

alert udp any any -> **YOUR HOST IP** 53 (msg; "Nmap upd scan"; sid:1000004;)
```

- The largest change we are making with these rules is specifying what type of packet we are expecting being tcp or udp and specifying ports 
	- the flags specify for a tcp match, in this case where we have an F we are testing for FIN


