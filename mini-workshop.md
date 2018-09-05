Bro 90 Minute Crash Course
==

Goal
====
**The goal of 90 Minute Bro Crash Course is to understand the fundamental concepts inside of Bro, what drives Bro forward and how are resources consumed.**



[0. Introduction](#intro)

[1. Optional - Github Participation](#optional)

[2. Story Time..](#story)

[3. Log In To The Environment](#logon)

[4. Tour of the Containers](#tour)

[5. What is Bro](#what)

[6. Tour of the Bro Logs](#logtour)

[7. Exploring Logs with bro-cut](#explore)

[8. Protocol State Machine](#statemachine)

<a name="intro"></a>
0. Introduction
===
**Trainers:  Liam Randall**
   * Twitter / IRC: 	@Hectaman
   * Mail: 		liam.randall@gmail.com
   * Sponsor: The operating system for the cloud- [Critical Stack](https://www.CriticalStack.com) is the secure container orchestration platform for the enterprise.

<a name="optional"></a>
1 Optional
===


1. Github - Access to the Longer Class Format
  * There is normally a longer walkthrough to this class; hosted on github
  * IF you would like access to the longer 1 day format, please send an email with your github id to **liam.randall@gmail.com**
2. The next free public full day class will be held at [BSidesDC](http://bsidesdc.org/) October 26-28, 2018 in Washington, DC.

<a name="story"></a>
2. Story Time
===

1. At it's core Hunting and Incident Response involve two steps:
  * First, asking a question.
  * Second, answering it.
2. Bro is a stateful protocol analysis engine that creates events in response to network traffic (either live or recorded) that may be hooked to create logs, generate alerts or take actions.
3. Let us start by beginging with the end in mind- before we begin our mad dash through Bro, let's look set the stage by doing an example.  Let's do a quick walk through of the [2011 SANS Holiday Challenge](https://pen-testing.sans.org/holiday-challenge/2011).

<a name="logon"></a>
3. Log In To the Environment
===

0. We have a large class today, so we have created THREE identical training environments:
    * **t1.liamrandall.com**
    * **t2.liamrandall.com**
    * **t3.liamrandall.com**

1. You will be assigned to one of the environments depending on your seating position.  Please use your assigned server until instructed otherwise.

2. Let's get connected the Virtual training environment

    * Open a Terminal, Command Window or other SSH client, such as Putty.
    * logon to our training host using the following details:
      * Host: *t1.liamrandall.com*
      * User: **training**
      * Password shared in class
    * so from a linux or mac based terminal you might type:
      * ```ssh training@t1.liamrandall.com```
3. Walk through the wizard to create your Bro training container
      * **USE A THROW AWAY PASSWORD**
      * There are many like it, but this one is yours- remember your username / password
      * To access this in the future you will use the username / password combo created now
  4. This environment is yours to use; it will automatically be destroyed in a 48 hours.

**FAQ:**

*What is this?*

You are connected to a dynamically provisioned container.  A container a type of process isolation on Linux that allows for the enforcement of various types of isolation and resource limitations.  A container may be limited in what an how many resources it consumes (CPU, memory, block I/O, network, etc) and also *namespace* isolation that limits the processes view of the operating environment such as local process trees, networking, user IDs and mounted file systems.

More simply, you have been granted an entire virtual operating environment that has been *pre-configured* with Bro and all of the other resources you need to complete today's walk through.


<a name="tour"></a>
4. Tour of the Containers
===

Let's look around on the containers together and explore our environments.

1. Your home directory:
      * ```/home/training/```
2. Our class files (to be added in the next step)
      * ```/home/training/directory```
3. Bro installation directory:
      * ```/opt/bro```
4. Bro's Master Configuration:
      * ```/opt/bro/share/bro/site/local.bro```
5. Pcaps used in class
      * ```/exercies/TrafficSamples/```
      * ```/exercises/PCAPS_TRAFFIC_PATTERNS/```

<a name="what"></a>
5. What is Bro
===

Let's set the stage for what we are going to try and cover this week in with Bro.

1. Bro is a language first
2. Event-driven
3. Built-in variables like IP address and time interval are designed for network analysis
4. Built-in functions can be implemented in C++ for speed and integration with other tools
5. [Presentation: Bro Overview](https://github.com/NSMAssociates/2018/blob/master/presentations/2014-7-Critical-Stack-Bro-Overview.pdf)
6. Walk through of the laminated cheat sheets




<a name="logtour"></a>
6. A Tour of the Bro logs
===

Bro is typically run in one of two ways- either as a daemon attached to an interface(s) or at the command line with pcaps.  This week we wills spend the majority of our time replaying targeted traffic samples at the command line; this allows us to focus on specific scenarios.

[1. Configuration](#config)

[2. Replaying Traffic with Bro](#replay)

[3. Bro Compared to Wireshark](#wireshark)

[4. It's all about the base](#base)

[5. Enabling Additional Bro Scripts](#scripts)

[6. Policy](#policy)

[7. Exploring Logs with bro-cut](#explore)

<a name="config"></a>
**1. Configuration**

Let's start by inspecting the configuration on our containers so Bro knows what to consider ```local```.

1. Let's review Bro's main configuration file, using the editor of your choice:
    * ```less /opt/bro/share/bro/site/local.bro```
2. Let's see what is local (or verify that it is already there); at the bottom of the file:
    *
```
redef Site::local_nets += {
  192.168.0.0/16,
  10.0.0.0/8,
  172.16.0.0/12,
  100.64.0.0/10,
  127.0.0.0/8,
};
```

<a name="replay"></a>
**2. Replaying Traffic with Bro**

1. Start by moving to our test directory:
    * ```cd 2018/training/files-framework```
2. Run Bro against a PCAP:
    * ```bro -r /exercises/TrafficSamples/faf-exercise.pcap```
3. Look at the logs generated:
    * ```ls *.log```
4. Go through some of the logs (e.g. less -S files.log); intro to log guides

<a name="wireshark"></a>
**3. Bro compared to Wireshark**


**We'll do this as a class exercise**

1.  Open **Wireshark** from the desktop and open ```/exercises/TrafficSamples/faf-exercise.pcap``` in the window.
2. Right click on **Frame 1** and  go to **Converation Filter** and select **--> TCP**.
3. Let's look at the first line in your Bro ```http.log```; it will look something like this:
```
1258544215.203850       CjVLXK2sVHwin7o6cc      192.168.1.104   1258    77.67.44.206    80      1       GET...
```
4. In the second column you will see the ```uid```; this is the connection unique identifier.  This number is 96-bit integer, an implementation of the GUID and mathematically speaking [1] is probably unique; .  As a side bar, this used to be a 64-bit integer [2] until someone from a small nordic country complained about collisions.  If you're curious you can easily calculate the birthday problem [3] on various numbers of connections [4].
5. Let's take that ``uid`` (what ever **yours** is) and let's have a piece of yummy bro cake:
```
grep -rin CjVLXK2sVHwin7o6cc ./ | less -S
```
6. Explore each log line to understand the context?

**Questions**

1. How many packets are summarized by Bro?
2. How many layers of cake do you see for the first HTTP Connection?
3. What logical layers are represented in the logs generated for the first layer of traffic?
4. Generate a quick listing of all of the logs generated so they stay in your shell history buffer: ``` ls *.log```


   * [1] http://en.wikipedia.org/wiki/Globally_unique_identifier
   * [2] https://bro-tracker.atlassian.net/browse/BIT-1016
   * [3] http://en.wikipedia.org/wiki/Birthday_problem
   * [4] http://preshing.com/20110504/hash-collision-probabilities/

<a name="base"></a>
**4. It's all about the base**


Above when we ran bro we ran it in **base** mode.  This means that Bro loaded all of the built in scripts that you find in the directory ```/opt/bro/share/bro/base```

```
~/2018/training/files-framework >  ls /opt/bro/share/bro/base
bif  files  frameworks  init-bare.bro  init-default.bro  misc  protocols  utils
```

For the last 20 years, when people have thought about *what bro is* or *what bro does* they generally are thinking about **base**.  These are the default scripts that generate the most basic output of Bro.  Let's explore these three categories a bit to understand the **base** of Bro.

Review The following directories:
  * **protocols**
  * **frameworks**
  * **bif: Built In Functions**
  * **utils**
  * **files**


<a name="scripts"></a>
**5. Enabling Additional Bro Scripts**

1. Let's replay that traffic sample again this time asking Bro to enable some additional scripts:
    * ```bro -r /exercises/TrafficSamples/faf-exercise.pcap local```
2. Comparing your history, what additional logs are generated?
```
~/2018/training/files-framework > ls *.log
conn.log  files.log  ftp.log  http.log  packet_filter.log  smtp.log  ssl.log  x509.log
~/2018/training/files-framework > bro -r /exercises/TrafficSamples/faf-exercise.pcap local
~/2018/training/files-framework > ls *.log
conn.log   ftp.log   known_certs.log  known_services.log  notice.log         smtp.log      ssl.log
files.log  http.log  known_hosts.log  loaded_scripts.log  packet_filter.log  software.log  x509.log
~/2018/training/files-framework >
```
3. You should see an additional set of logs generated; not only that however if you were to look closely you would notice that the contents of some of the logs have changed.
4. When you tell Bro to also load ```local``` Bro knows to where to find this script- it knows where it lives.  Explore the configuration:
    * ```less -S /opt/bro/share/bro/site/local.bro```
    * The Training VMs [local.bro](https://github.com/NSMAssociates/2018/blob/master/misc/local.bro)
5. Please examine:
   * ```known_certs.log```
   * ```known_hosts.log```
   * ```notice.log```

<a name="policy"></a>
**6. Policy**

When examining ```local.bro``` in the previous step you may have noticed a lot of additional scripts being loaded.  By default Bro is shipped very policy neutral- base doesn't really tell you much beyond trying to log the ground truth of what happened in a given protocol.  There is a lot of additional analysis and context that can be gained by loading scripts as a matter of your organizations *policy*.  Hence the name for the collection of additional bro scripts, framework extensions and so forth: ```/opt/bro/share/bro/policy```.

If we look at the structure and layout for this folder, we'll find that it pretty directly mirrors what we found in the base directory:

1. **frameworks** : extensions and plugins to the frameworks defined in Bro.
2. **integration** : integration with both barnyard2 and the collective intel server (if)
3. **misc** : scripts with no better home.  *scan.bro*, *dump-events.bro*, *profiling.bro*, and more.
4. **protocols** : protocol specific scripts and extensions.
5. **tuning** : scripts that perform a variety of reconfiguration of Bro.


<a name="explore"></a>
7. Exploring Logs with bro-cut
==

Bro operates on your network traffic and variably extract more or less metadata as you configure it.  As mentioned above, by default Bro will load everything in the **base** direction as well as your **local.bro**.  It is important to remember, that these logs are not *reality*- they are a representation of the underlying reality.

In this exercise we're going to introduce a simple tool we can use to parse bro logs at the command line while demonstrating this point.

[1. Overview](#overview)

[2. Exercises](#exercises)

[3. Additional Examples](#morecowbell)

[4. Review](#review)


<a name="overview"></a>
**1. Overview**

**bro-cut** is a simple utility that can be used to investigate bro logs at the command line; understanding the default Bro ascii log format you can very easily parse and investigate bro logs.  As a tool, this is very useful when investigating a specific set of pcaps or a smaller network.  For larger networks though, hunting through TB of Bro logs, while another tool may be appropriate (Elastic Search, Splunk, etc) conceptually the thought process we study here still applies.

After introducing this tool, we are going to demonstrate a very important point about metadata generation, that-

**the configuration of your metadata extraction is as important as the extracted metadata.**

That seems like a trivial point, however we will follow this example with a few very relevant and practical demonstrations.


<a name="exercises"></a>
**2. Exercises**


1. Let's move into a different set of pcaps
    * ```cd ..training/http-auth```
2. Exercise:
    * ```bro -C -r /exercises/TrafficSamples/http-basic-auth-multiple-failures.pcap local```
3. ```bro-cut``` can be used like ```sed```, ```cut```, ```awk``` all at once- with the advantage that it abstracts away from the specific schema (column order) of the logs.
4. We can easily start to ask questions like: What is the count of the distinct status_code:
    * ```cat http.log | bro-cut status_code | sort | uniq -c | sort -n```
5. Or you could get a count of http sessions by username:
    * ```cat http.log | bro-cut username | sort | uniq -c | sort -n```
6. Can you generate a count of status_codes by username? What about usernames by status_code?
7. Now add in the distinct URI?  What do you think is going on in this pcap?
8. Let's add a little more information and context to this pcap; let's tell Bro to extract the passwords as well:
    * ```bro -C -r /exercises/TrafficSamples/http-basic-auth-multiple-failures.pcap local http-auth-passwords.bro```
9. Now let's explore the basic http.log again:
    * ```less -S http.log```
10. Do you see the populated ```password``` field?
11. Will the ```bro-cut``` summaries you generated earlier still work?
12. This seems kind of manual.. let's automate this a bit:
  * ```bro -C -r /exercises/TrafficSamples/http-basic-auth-multiple-failures.pcap local detect-http-basic-auth-bruteforcer.bro detect-http-basic-auth-server-bruteforced.bro ```
13. Now let's review the logs; what new log do we see?  What did we just automate?


<a name="morecowbell"></a>
**3. Additional Examples**

Together in class we're going to review the follow examples:

1. Heartbleed
2. Protocol manipulation- what would happen if I sent duplicate HTTP host headers?
   A. HOST: www.liamrandall.com
   B. HOST: www.CriticalStack.com
   C: HOST: www.google.com

**WARNING:**

We are going to introduce a subtle and complicated topic here- that *logs may be lies*; I am loathe to discuss this in too much detail, however it is important to remember that all of your tools have baked in assumptions about the content upon which they act.  This is not a Bro specific problem, this is endemic to all software.



<a name="review"></a>
**4. Review**

  1. When running bro from the command line you can change the configuration by telling bro to also load different configuration files.
  2. The logs that bro writes are not statically defined; they are the living dynamic output.
  3. There are many ways to manipulate bro logs.
  4. A set of bro logs is only relevant with the configuration used to generate it.

  <a name="statemachine"></a>
  8. Protocol State Machine
  ===

  Bro is different by design.

  From the ground up Bro simply approaches problems with a different point of view.  The Bro Platform provides an interface to network traffic through the idea of an event.  That is to say Bro will let you know when certain things happen on the network; there are connection specific events, protocol specific events, framework associated events, time based events, or even your own custom events.

  Some sample events are things like:
  * ```event bro_script_loaded(path: string, level:count)```
  * ```event new_connection(c: connection)```
  * ```event file_over_new_connection(f: fa_file, c: connection, is_orig: bool)```

  *Each* event can be hooked **zero** or more times based on any number of properties.  Let's try to understand this a little better by exploring **ALL** of the events that fire during a simple HTTP transfer.

**Contents**

[0. Overview](#overview)

[1. HTTP a Single Request](#http)

[2. More Bro](#morecowbell)

[3. Exercises](#stretch)

[4. A Dramatic Inerpretation of Network Traffic](#thetheater)

[5. Review](#review)

<a name="overview"></a>
**0. Overview**

In this exercise we are going to study the Bro internal event model- we want to explore the space between PCAP and Logs.  As an evented programming language these are the events that Bro *generates* as traffic is parsed by the protocol analyzers.  You can choose to handle an event, analyzing or storing state for later analysis or comparison.

<a name="http"></a>
**1. HTTP a Single Request**

Let's go ahead and walk through a protocol state machine for a single http web request.  Let's start by replaying a pcap through bro and logging all of the events as they fire.  We are going to go ahead and change to the  ```training/protocol-state-machine``` folder and we'll be using the file ```http-single-host-54.230.103.187.pcap``` for this demonstration.

Let's ask Bro tell us about the events that are firing when a pcap is run.

**Let's do this one together**
```
bro -C -r http-single-host-54.230.103.187.pcap  policy/misc/dump-events.bro  policy/misc/dump-events.bro | less -S
```

Whoah.  What was all that?


<a name="morecowbell"></a>
**2. More Bro**

Ok, so we can instruct Bro to print out a log of all of the events that it is generating internally.  Let's go ahead and use the ```training/protocol-state-machine/http-single-host-54.230.103.187.pcap``` which includes a single flow and inspect the protocol state machine.  If you have not already, let's go ahead and execute:

```
bro -C -r http-single-host-54.230.103.187.pcap  policy/misc/dump-events.bro > dump-events.log
```

This means, "hey Bro, ignore checksums, read in this pcap AND load the dump-events script and redirect the output of the console to dump-events.log".  You can now use ```less``` and ```less -S``` to browse the dump and log files.

One thing that I notice straight away- we didn't load ```local.bro```!  So none of the scripts, frameworks or framework extensions that would be loaded when Bro runs were not used in this case.

Let's ask Bro to read the traffic sample again except let's load ```local.bro``` and write the output to a different file so that we can compare the two files.


```
bro -C -r http-single-host-54.230.103.187.pcap local policy/misc/dump-events.bro > dump-events-local.log
```

<a name="stretch"></a>
**3. Exercises**


Now let's use the output of the ```dump-events.bro``` to try and answer the following questions.  They might not all be as straight forward as they may seem...

1. What file is being downloaded?
2. What user-agent is being used?  
3. In what event is this data first available to you in scriptland?
4. Figure out a way to compare the different events being launched in the two versions of your ```dump-events```.
5. Identify at least two events that fire when local is called vs. when local is not called.
6. Identify at least two events that are called the exact same number of times between the two scripts.

<a name="thetheater"></a>
**4. A Dramatic Inerpretation of Network Traffic**

Class Project.  I need two brave volunteers.

*Follow Along:*

1. Using a piece of paper, draw a flow chart of the Protocol State Machine for this transaction
2. Imagine what a PSM would look like for FTP?  IRC?  SSL/TLS?  How are they the same, how are they different?


<a name="review"></a>
**5. Review**

Dump events is a very powerful tool that can help you to understand network traffic.  It is important to remember that this interpretation is limited by the specific *implementation* of the protocol analyzer.
