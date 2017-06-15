_The purpose of this document is to be an introductory guide to Security, Anonymity and Privacy - we don't claim that after reading and applying all the suggestions we provide that you will be unhackable, undoxable and unarrestable. This is a more general guide to staying safe online and keeping your sensitive information protected. We cannot cover all possible situations, threat models or outcomes._

## What does Anonymity and Privacy really mean?
__Privacy__ is the ability to selectively reveal yourself to the world. It allows you to be honest with people about the things you wish them to know without revealing what you don't want them to know or revealing it to other parties.

__Anonymity__ is the inability of an onlooker to distinguish you from any other person in a group. It allows you to act, speak and read without fear of personal retribution.

## Taking on new identities
You can choose to take on a new long-term identity and build up a reputation, or use multiple throw-away ones, or both. Each has their own advantages and disadvantages but it is essential that you protect your *PII* (Personally Identifying Information).

PII can take many forms. It is any bit of information that might distinguish you from any other person. This can range from obvious things like where you live, to the less obvious things like what music you like to listen to, what operating system and software you run, [how you type](https://en.wikipedia.org/wiki/Stylometry) or information and events that only you would know about.

Every time you reveal a bit of information about yourself you reduce the set of people in the world that you could possibly be. If you leak enough bits of information you will eventually be identifiable.

It is important that any new identities that you might use are not linked to other identities. The ability to link these identities together could allow someone who knows some bits about each identity to connect the bits together to gain a lot of knowledge about the person behind them.

To help avoid mistakes try to create strong cues to yourself about the persona which you are acting under. This could be as simple as using a specifically colored theme, desktop environment or operating system to visually distinguish your day-to-day computing activities with those of your pseudonymous persona as a constant reminder of which identity you are and _never_ mix the two.

Besides accidental leaks others may try to force them. Law enforcement agents, informants and other malicious actors will often deploy common confidence tricks to try and draw bits of identifying information out of you. As a general rule communications should be kept to a minimum _anyone_ asking for information from you that's not directly pertinent to your activities should be avoided.

Be incredibly wary if anyone offers to pay you for services or information, payments are difficult to anonymize and often constitutes a whole new, and serious, type of crime for them to charge you with: _"that's fed tactics"_

Some recommended further reading:

* [CrimethInc's 10 point checklist for informants](https://crimethinc.com/2013/07/06/is-she-an-informant-a-ten-point-checklist).
* [CrimethInc on Security Culture](https://crimethinc.com/2004/11/01/what-is-security-culture).

### Generating Nicknames

Tails uses the "language confluxer" script to generate a psueonym to be used with Pidgin on start up. It generates randomized names base on some sample inputs.

I have published [NickGenerator](https://github.com/epidemics-scepticism/NickGenerator) which will generate the same random psuedonyms that Tails users are assigned to aide in the creation of new pseudonymos identifiers.

    $ git clone https://github.com/epidemics-scepticism/NickGenerator.git
    $ ./NickGenerator/Generator.py
    Ulyssabera	

## Protecting your location
The first port of call to ensure your anonymity is to obfuscate your current location from services that you interact with online. Most of us will be using the internet from connectivity that we pay for or are linked to in some way. When you connect to something on the internet, your IP Address is used so that the thing on the other end of the line knows where to send data back to. Think of this like a phone number, your IP Address is tied to you, tied to your billing info and tied to your home address. We need to change the number you appear to be calling from.

### Tor
A common tool people use for hiding their location is a program called *Tor*. Tor sends traffic through a series of intermediary servers, called relays. By segmenting the connection across three relays it stops any *single* relay from learning both the true origin and destination of any connection.

>You -> Relay A -> Relay B -> Relay C -> Internet

Relay A: Sees you and Relay B but *not* where you're connecting to.
Relay B: Sees Relay A and Relay C but *not* you *or* where you're connecting to.
Relay C: Sees Relay B and where you're connecting to but *not* you.

Tor isn't perfect, a lot of websites [block tor exit nodes](https://trac.torproject.org/projects/tor/wiki/org/doc/ListOfServicesBlockingTor) and exit nodes can [see sensitive data sent over them unencrypted](https://www.eff.org/pages/tor-and-https). While Tor can hide where you are connecting from, there are correlation attacks that can be performed to link your anonymous internet activity to your real life identity. A [real world example _(See: page 26 through 43)_](https://www.scribd.com/document/84134934/Hammond-Jeremy-Complaint) of this was documented as being used against [Jeremy Hammond](https://freejeremy.net/):

* Anarchaos uses tor to connect to a chat room to hide his true location.
* One day Anarchaos reveals some PII to another user, Sabu.
* Unknown to Anarchaos at the time, Sabu is actually an FBI Informant. He passes on this PII to his handlers.
* The FBI uses the PII to narrow down their list of suspects to a man named Jeremy, they decide to surveil him.
* Sabu is instructed to tell his FBI handlers when Anarchaos is online. The FBI matches up times when Anarchaos is online to times when Jeremy is home. Using this evidence, they raid Jeremy's house and he is arrested.

More technically sophisticated correlation attacks are possible too, as the Tor Project points out in their [FAQ](https://www.torproject.org/docs/faq.html.en#AttacksOnOnionRouting).

So while Tor is a very robust tool with good anonymity properties it can be used incorrectly. For a document outlining some do's don'ts and common mistakes, see [this post](/blog/misconception).

### VPNs
VPNs provide a means of hiding the source of a connection. VPNs create an encrypted tunnel between you and the VPN providers network, this then allows you to make connections from the providers network, hiding your traffic from a local observer (e.g. your ISP or someone trying to spy on your use of an open wifi network). VPNs have some advantages over Tor:

* They allow you to send full IP packets, this allows them to carry more types of data than Tor can and perform activities Tor can't.
* They have lower latency than the Tor network, since the connection is only going over a single host often making the connection less slow.
* Most operating systems support VPNs out the box - including mobile devices.

However unlike Tor, VPNs do not hide your location from the provider. This means you must place full trust in your VPN provider not to reveal your location to your adversaries, many providers will claim that they do not log but there is no way to verify that. In some cases, while the VPN provider really did not keep logs, their upstream ISP did and these were [used against their users](https://www.bestvpn.com/blog/8383/earthvpn-user-arrested-cops-find-logs/). Another method used was to determine the inbound and outbound IP addresses for a VPN provider (by simply creating an account with them) then match the connection times from a suspect to the VPN provider against connection times to the victim from the VPN provider, as seen in the ["PoodleCorp" criminal complaint _(See: Page 41 and onward)_](https://www.justice.gov/usao-ndil/file/900826/download).

OpenVPN is open source VPN software that is widely used and should be preferred if a provider offers it. Try to avoid any services that suggest using a PSK (_pre-shared key_) during configuration, as they often share the key between many or even all their users, [anyone with the key is able to decrypt any other users traffic](https://gist.github.com/kennwhite/1f3bc4d889b02b35d8aa).

Other solutions worth considering are [Algo](https://blog.trailofbits.com/2016/12/12/meet-algo-the-vpn-that-works/) or [Streisand](https://github.com/jlund/streisand) but both of these are intended as solutions that a user creates for themselves, which involves maintaining your own server infrastructure which is problematic for anonymity and a barrier to entry for non-technical users.

All the failure cases for Tor apply equally to VPNs except worse.

### Don't fuck it up!

In all cases, you should make provisions to stop these services from "failing open". If you have a VPN service and the VPN connection breaks during usage you want to ensure that your computer cannot re-establish the connections that you were making over the VPN on your local internet connection. This should be approached through the creation of a Packet Filter (_"Firewall"_) rule-set to restrict outbound connections. An example of this is [VPN Firewall](https://github.com/adrelanos/VPN-Firewall), it also cites alternative implementations of the same idea.

### Proxies
HTTP or SOCKS proxies should _never_ be used on their own. _Sometimes_ they can be useful if you connect to them over some other technology first or as a means to pivot into internal network resources from another device but they provide no meaningful protections and will only expose your traffic to more risk of interception or manipulation than it ever needs to be.

## Securing your box
Once you have created a barrier between your internet activity and your location, the next step is to ensure that your computer cannot be used against you. This is not the be all and end all of OS hardening, so we'll outline some best practices and leave the rest as an exercise for the reader.

### Choosing an Operating System
Operating System discussions often descend into tribal mania, that said an open source operating system is going to be simpler to properly secure because they are designed to be flexible but this can also work against you. If you are using an OS which only has a few thousand users this can be used as PII to fingerprint and track you. Software can often reveal details of the OS that you are using, so there is a balance to be struck between security, privacy, usability and anonymity.

Windows is by far the most popular OS around and something many of us have experience of using. Modern versions of Windows are reasonably secure and it's popularity will help you blend in but it is also less flexible and far less private or anonymous. A lot of the suggestions given in this guide will work on Windows (Tor for example) but for some of the more exotic tasks a Linux based OS is required.

Windows is a controversial topic in the community. Windows 10 has telemetry (usage tracking which reports back to Microsoft), advertising and other trackers built into it however it is by far the most secure Windows version available. It is bad for privacy and anonymity but good for security.

There are also specially built privacy, security and anonymity focused OSs. These include features designed to make it as hard as possible to determine the users location or track them across the internet. Tails, Whonix, Qubes and the currently experiment Subgraph are good examples of these.

#### Tails
Tails is a live-boot operating system based on Debian GNU/Linux. It makes efforts to avoid storing information about what it is used for on the computer that it is used on. It uses the Linux `iptables` packet filter to restrict internet bound traffic to use only the Tor network, anything that is not sent out through the Tor network would be rejected, with the exception of outbound connections to local area network resources. The entire operating system runs in memory, this gives it very good anti-forensic properties. When the operating system is shutdown or restarted, it overwrites all data in RAM, removing evidence of anything that it was used for (protecting against _"cold boot"_ attacks). It is the easiest to use but the hardest to customize, in fact it actively resists customization. It offers encrypted persistent storage after installation, which can be used to store general data and documents or configuration files for some of the applications that come with it.

For more information see [Tails' own extensive documentation](https://tails.boum.org/doc/index.en.html).

Pros:

* Easy to use and well documented
* Strong anti-forensic properties

Cons:

* Lacks some of the hardening of the other options (Currently)
* Doesn't suit all use cases and harder to extensively customize

#### Whonix
Whonix is a solution consisting of two [virtual machines](#virtualmachine) that work together, it is also based on Debian GNU/Linux. The virtual machines are called the _Gateway_ and the _Desktop_. The Desktop is a fully-featured Linux desktop environment with applications pre-configured to use Tor through the Gateway. The Gateway acts purely as a packet filtering router, it's job is to ensure that no traffic leaves the Desktop and reaches the internet without going through the Tor network. To be able to make a direct connection to the internet from the Desktop, the Gateway would first have to be remotely exploited from the Desktop. This means that even if the Desktop is hacked, there would still be considerable difficulty in deanonymizing the true location user of the Desktop. Alternatively, it may be possible for an attacker with an exploit for the virtual machine hypervisor to instead reach directly from the Desktop system to host and to the internet. Both of these methods are considerably more difficult than just compromising a web browser and would require chaining multiple exploits together.

For more information on Whonix, [see their own documentation](https://www.whonix.org/).

Pros:

* Easy to use, once you get past setting up the virtual machines
* Good protection against accidental and malicious network traffic leaks

Cons:

* Depends on a trustworthy host operating system
* Documentation is more technically intimidating

#### Qubes
Qubes is a more generic [virtual machine](#virtualmachine) solution. It uses the Xen hypervisor to create and manage multiple virtual machines. The idea is similar to that described in Whonix, but it takes it further. You can create an entire virtual machine for each task, for example one for running your mail client, another for browsing your bank and another for browsing youtube. The distinct machines operate as containers, if someone were to send you a malicious email and exploit your mail client, they would not be able to attack the virtual machine where you are performing your online banking. This carries with it the same caveat, as with Whonix, that an attacker who can exploit the hypervisor can escape the container. The virtual machines for Whonix can also be run under Qubes and developers on both sides have put in effort to make them compliment each other. Qubes has many other advantageous security features, some more experimental than others.

For more information on Qubes, [see their own documentation](https://www.qubes-os.org/).

Pros:

* Effective isolation of distinct tasks
* Flexible and customizable

Cons:

* Limited hardware support (see website)
* By far the most technically intimidating

#### Subgraph
Subgraph is another Debian GNU/Linux based offering. Rather than virtual machines it create sandboxed containers to limit compromise. It uses a set of patches applied to the Linux kernel called _grsecurity_ and _PaX_. These patches make it harder still to exploit both the kernel itself and applications running on the system. While the containment provided by namespaces isn't as "clean" as that of Qubes the user experience is far more seamless, like a normal desktop environment. Subgraph is currently in Alpha testing and so may not be ready for day-to-day use, but is something that more advanced users may wish to experiment with.

For more information on Subgraph, [see their own documentation](https://subgraph.com/sgos/index.en.html).

Pros:

* Hardened against exploitation
* Customizable and configurable

Cons:

* Currently in alpha (testing)
* Bugs can be confusingly unintuitive to non-technical users

#### A note on virtual machines
Note that a virtual machine is only as trustworthy as the host that it is running on. If you (for example) run Whonix under Windows 10 then you will only ever have the privacy of Windows 10. Since Whonix would be running _under_ Windows, Windows sees everything that Whonix can and more.

#### A note on choice
All of the above operating systems are good choices, and likely an improvement on Windows 10, OSX or Ubuntu for privacy and anonymity. There are incredible amounts of infighting in the infosec community about which is the "purest" option. Work within your means, none has _perfect_ security and all have advantages over the others. Other good operating system options are also available but not documented here.

### Encrypting your HDD
It's good practice to encrypt your hard drive to stop any prying eyes should it be seized, this is known as Full Disk Encryption. There are several tools which can be used for this, but it is important to ensure you only use open source software. Open source software can be vetted by the public and shown to have no known bugs or backdoors in - this isn't foolproof of course, but it is a hell of a lot better than a closed source binary blob.

When encrypting storage media you should always pick a [strong passphrase](#passwords) to protect it with since this type of encryption is most likely to be subject to an "offline" attack which would allow an attacker to make far more rapid guesses at the passphrase than if it were an online login form. You should also always try to encrypt by default, since removing data after the fact is a much more difficult task than simply forgetting a passphrase.

Most modern Linux distributions offer full disk encryption during the installation process. This encrypts the entire hard drive that it is installed on and most modern Linux desktop environments offer disk management tools which will allow you to encrypt your storage devices (like USB sticks or other hard drives) once encrypted these storage devices will be compatible with other Linux distributions. Under [Gnome the utility is called Disks](https://tails.boum.org/doc/encryption_and_privacy/encrypted_volumes/index.en.html) and under [KDE it is called Partition Manager](https://we.riseup.net/riseuphelp+en/disk-encryption-kde).

You can also manually perform this task from the command line using the `cryptsetup` command. This is an interface to the same set of tools used by the aforementioned KDE and Gnome disk management utilities and will result in an encrypted disk that is compatible with other Linux distributions.

###### &lt;One day this will contain VeraCrypt stuff&gt;

#### Beware encrypted "containers"
VeraCrypt and other tools offer encrypted containers, these are files that contain encrypted data. These can be problematic if they're opened outside of a secure environment. Any applications you use to process data stored _inside_ of a containers may store information about the contents _outside_ of the container, leaking information to later forensic investigation.

### Anti-Virus

A quick note on anti-virus: Don't use it if possible.

This is likely controversial but I'll try to convince you that anti-virus is harmful for anonymous and private use.

The short version is that when you use anti-virus as a consumer, you are a sensor in a large "threat intelligence" network. Much like how free apps on the Google Play and Apple iTunes stores offer themselves as free in order to harvest data for advertising, to both display products to you and to gather information on you to better sell you products. With free and consumer anti-virus products what they gather from you is information about your activity and what you see and do online that they consider suspicious or malicious which is then repackaged and sold at a premium as "Threat Intelligence".

For example, lets look at the Privacy Policy for MalwareBytes Anti-Malware:

>**7. Privacy Policy.**

>By entering into this Agreement you agree to the terms of Malwarebytes' privacy policy, which can be found at https://www.malwarebytes.com/privacy/ ... **you agree that Malwarebytes may track certain data it obtains from your Device, including data about** any malicious software, exploits or other threats flagged by the Software (including but not limited to potential sources of such threats, such as **payload files, file format and recent URL's visited**), data about your license, data about what version of the Software you are using and what operating conditions it runs under and **data concerning your geographic location**. ... **We may share data relating to malicious software, exploits or other threats flagged by the Software with third parties**

If we look at who they may decide to share this data with, we find this section:

>**Governmental; Law Enforcement**

>We may disclose **PII to government agencies, law enforcement officials, and private parties** as we, in our **sole discretion**, believe necessary: (1) to satisfy or comply with any applicable law, regulation or legal process; (2) to respond to lawful requests, including subpoenas, warrants or court orders; (3) to protect our property, rights and safety and the rights, property and safety of third parties or the public in general; and (4) **to prevent or stop activity we consider to be illegal or unethical.**

They say that if they think you are doing somthing "unethical", they can choose at their "sole discretion" to hand over your data to government or law enforcement agencies (although doesn't even suggest they'd be your own government or law enforcement or who's laws or ethics you'd have to break).

Consider also Trend Micro, who have a [Memorandum of Understanding](http://www.trendmicro.co.uk/newsroom/pr/trend-micro-and-national-crime-agency-sign-cyber-crime-busting-partnership/index.html) with the UKs NCA (nee SOCA):

>The NCA will not only be able to draw on the expertise of Trend Micro’s global, 1,200-strong TrendLabs team, but the power of the **cloud-based Smart Protection Network, which analyses over 15TB of threat data**, identifying and blocking more than **250 million threats each day**.

This "Threat Data" is likely similar to the data collected by MalwareBytes.

Another consideration is that (against certain types of attackers) anti-virus can act as the means of compromise or privilege escalation. Tavis Ormandy, a researcher with Google's Project Zero, has found and documented severe vulnerabilities in (or introduced into other products by) anti-virus software from Sophos, Symantec, Kaspersky and others. Other researchers have consistently found similar bugs in security software. In fact a [DoD report on it's own systems](https://twitter.com/dotMudge/status/642758829697056768) suggests that 1/3rd of vulnerabilities in their systems came from "security" software.

Instead opt for a more secure system to operate inside. This means using access control mechanisms, like [AppArmor](http://wiki.apparmor.net/), [grsecurity/PaX](https://grsecurity.net/) or [SELinux](https://selinuxproject.org/) on Linux, App sandboxing on OSX and [EMET](https://support.microsoft.com/en-us/help/2458544/the-enhanced-mitigation-experience-toolkit) on Windows. Keep your software up-to-date, verify software that you install and be wary of opening files from unknown or untrusted sources. Disable or don't install superfluous features to reduce the attack surface and attacker can reach. Windows users should also consider using [Hardentools](https://github.com/securitywithoutborders/hardentools) from [Security Without Borders](https://securitywithoutborders.org/)

### Resisting Forensic Analysis

While of course _Plan A_ should be avoid having your systems reviewed by forensic analysts not planning for this potential eventuality is reckless. Good data-hygiene is an important thing to practice.

So you've got a secure operating system, you've protected your location. Now it's time to look at the digital footprints you leave as you operate your own device. The best way to avoid leaving forensic evidence on your device is to avoid storing it in the first place. This is more difficult than it sounds. Modern operating systems have caches everywhere and often log user activity to better predict the users needs, like the [zeitgeist daemon on Ubuntu](http://www.forensicswiki.org/wiki/Zeitgeist) for example.

For this reason you may wish to prefer using a "live" media distribution to prevent any information the operating system does happen to collect from being stored on a physical disk. [Tails](#tails) has some of the strongest anti-forensic properties of any of the secure operating systems we covered but it may not suit the needs of all users. If it does fit your requirements and anti-forensics is an important part of your protection then you _should_ use it, at the very least for the activities that do require anti-forensics.

Some top tips:

* Avoid setting up swap space under Linux
    * Most modern computers have sufficient RAM to not require it, so it's a liability that can just be avoided entirely.

* Consider backing specific directories with a `tmpfs` filesystem
    * Some obvious locations would be places like `/var/log/` and `/tmp` (Surprise! Not all `/tmp`'s are `tmpfs` backed). Since `tmpfs` is stored in volatile memory data written to these directories won't be writen to disk.

* From userspace, try [unburden-home-dir](https://github.com/xtaran/unburden-home-dir).
    * Similar to mounting a `tmpfs`, this allows you to symlink directories and files to a location in a pre-existing `tmpfs` backed location, commonly `/tmpfs` or `/dev/shm/`. This can be used to keep browser caches and similar from ever touching disk.

The reason we want to avoid storing data to disk in the first place is that simply deleting a file does not remove the data the file contained from your disk, instead it simply removes a reference to the datas location on disk and marks the area where the data _stil is_ as free to use again. Repeatedly overwriting the content of the file may work but can often be insufficient, the file may have been copied around the disk multiple times during processes like defragmentation meaning that deleting where it is _now_ won't help remove copies of where it was _previously_.

Tools that claim to erase data and clear caches often do not do so fully, for more see national security journalist and computer forensic expert witness Duncan Campbell's [Computer Forensics for Journalists](https://www.youtube.com/watch?v=ecWMPmB3O50).

In case of emergency, **do not** try to use such tools, instead __*wipe the disk!*__ You do not want to rely on half-measures if you have to ensure that sensitive data is erased.

Thankfully, modern ATA based hard drives (HDD and SSD) have a special function called "Secure Erase". The erasure is done by the disk firmware itself, this has a few advantages:

* It's faster than tools like DBAN
* It catches "bad blocks", which DBAN misses
* It doesn't wear out SSDs like DBAN

To use Secure Erase from Linux (this should be your prefered platform to use it from) follow these steps:

1. If you haven't set a password on your drive, set the password with the command `hdparm --user-master u --security-set-pass badpassword /dev/sdx`, this sets the drive password for "/dev/sdx" to "badpassword". Change the "x" to the drive letter you wish to wipe.
2. Issue the command `hdparm --user-master u --security-erase badpassword /dev/sdx`, again replacing the "x" in "/dev/sdx" with your drive letter. **Wait for the command to complete**. This should be very quick on SSDs but could be in the region of 30 minutes on large, older HDDs.

Once the security erase command has been issued, the password set in step 1 will have been unset. There is no requirement to remember this password beyond these steps.

Now to affix your bracers as well as your belt: Run DBAN anyway. The reason for this is there have been documented cases of Secure Erasing failing with certain hard drive firmwares (bugs). Secure Erase *should* give a good baseline, DBAN is a lot slower to complete and misses some spots. Both, used together, should provide adequate erasure but run Secure Erase first to get to the good baseline ASAP.

Important points to take away:

* Never store it if you can avoid doing so: _No logs, no masters!_
* If you have to store it, encrypt it: _Encrypt all the things!_
* If you have to get rid of it, use a scorched earth policy: *Wipe the disk with __extreme prejudice__!*

### Secure Messaging

#### One-to-One chat

Secure messaging is a problematic topic, the de-facto standard for messaging is XMPP (or _Jabber_, as it used to be known). XMPP is advantageous because it is [_"federated"_](#federation) which can help avoid the issue of lots of interesting users ending up on one service provider. It's often not as user friendly as other "secure messaging" options but it does have some properties that others can't offer.

XMPP addresses are formatted like email addresses: `user@host`, the `host` part of the address is your provider and `user` is a username that you choose.

There are many providers to pick from and you should operate on the principle that they are ultimately untrustworthy, regardless of how trustworthy they really are. There are a few lists of public XMPP servers, for example [here](https://list.jabber.at/) and [here](https://xmpp.net/directory.php). Pick one that is well established or run by a well known group or organization, to reduce the risk that the service will simply vanish one day. You should then have your new XMPP address through which people can contact you.

Note that both the sender and receivers server can see the messages exchanged between users, this is something that you want to avoid. So you want to use a method to encrypt your communications *end-to-end*.

OTR is the de-facto standard used with XMPP. It's a well established protocol and comes integrated into many modern XMPP clients and if it's not then it is often available as a plugin. Since it's so widely deployed, it will be the most compatible with other XMPP users.

The most important part of using OTR will be getting to grips with "fingerprints". A fingerprint is a representation of a public key, canonically represented as 40 hexidecimal digits (e.g. `b6f8009607eab4873053aa0d320b1cde37919f0c`). Your client will likely keep track of and verify fingerprints but it is important that you manually verify them before marking them as good.

The best kind of verification is to verify them in person, but given that this is a guide for anons this likely isn't feasible. Instead you should seek to share them between each other in a few different media and ensuring that they all match. Sharing them through some pre-existing trust mechanism, like PGP, is also a good choice.

OTR also providers a mechanism called SMP (Or the Socialist Millionaires Protocol) which uses a clever cryptographic trick that allows users to ask a question of another user and check the answer, without revealing the answer. This can be used to verify the identity of another user if there is some secret information that each shares. If only your expected contact could know the answer to the question and they're able to complete the SMP exchange then you know it is them you are communicating with.

Some newer protocols are becoming more popular, applications like Signal and WhatsApp provide good confidentiality and authenticity (secrecy and assurance that you're talking to who you think you are) but not anonymity or privacy. They are directly tied to a phone number which is often tied to a physical location (cell towers), a billing address and method (IMSI) and a physical device (IMEI). If you can do this properly, they can provide good protection but the potential for mistakes to be catastrophic makes XMPP+OTR a more robust choice.

Some XMPP clients not support OMEMO which is a good choice f it's available, but it is not widely deployed and OTR will provide better compatibility for now.

Another new contender in the anonymous messaging space is [Ricochet](https://ricochet.im/). Ricochet makes use of Tor's [Onion Services](https://www.torproject.org/docs/hidden-services.html.en) to provide an end-to-end communication channel in a decentralized manner. Each user has an address (16 alphanumeric characters) and they can contract other users by their addresses. Since each user is their own server and client, they speak directly to each other over an onion service, it is end-to-end encrypted by default. It is less widely used but if it is available it should be strongly considered as an option as it greatly reduces the available metadata accessible to any observer.

#### Group Chat

Group chats can be difficult, by their very nature they're only ever as strong as their weakest point. That said, if you can have end-to-end encrypted group chat (as oposed to IRC where the server can see the content of the messages) is preferable, this forces targeted surveillance by way of compromising end points or people instead of giving them a freebie through incidental or passive collection.

Signal supports encrypted group chats with good secrecy but it isstill linked to a phone number. An alternative is [Riot](https://about.riot.im/). Riot is a client for the [Matrix](https://matrix.org), a decentralized and federated communications network. Riot suports end-to-end encrypted group chats, as well as one-to-one encrypted chats.

Similar to XMPP each user has an address in the format of `@user:host`. e.g. `@anonymousalice:example.com` would be `anonymousalice` on the `example.com` server.

You can create a new room with it and invite users to join. When a room is first created it is _not encrypted by default_, you'll want to change this in the rooms settings _(cog wheel at the top of the room tab)_. Tick encryption and also change the history options while there to "Members only (Since joined)" to further reduce information revealed to new people joining.

Each "device" under an account has it's own identity keys, you'll need to verify identities of people and each device they might use to connect separately. To view your own identity and public keys, go to your settings (cog on the bottom left next to where you can start new chats from) and scroll to the "Cryptography" section. If possible verify these values with your contact, and check theirs through some preexisting channel.

While the communications _content_ is protected things like the room name, topic, participants and times of messages are exposed. Ensure that these don't contain sensitive or revealing information. Keep them bland and uninteresting.

For Tails users who (like me) wish to install Riot without installing additional software or being root I have created a [simple installation script](https://github.com/epidemics-scepticism/misc/blob/master/get-riot.sh) to install it to `~/Persistent`.

## Appendix

### Passwords
The well known [xkcd comic](https://xkcd.com/936/) makes a strong point about information theory, the seeming complexity of a password doesn't accurately represent how hard it is to guess. While 'correct horse battery staple' may seem simple, the shortest path for an attacker trying to guess it is to guess all possible common words in combination. When combining words into a passphrases in this way the set of possible combinations grows exponentially as the number of randomly chosen words grows linearly.

For example, imagine you had a set of 2048 common words to pick from. If you picked one word, there are 2048 possible outcomes. If you picked a second word, you'd have 2048 outcomes for _every single one_ of the first 2048 possible outcomes: 2048 * 2048, or 2048 ^ 2. If you picked three, you'd have 2048 outcomes for each of the squared outcomes, 2048 * 2048 * 2048, or 2048 ^ 3. So, if you picked 4 of these common words you'd have 2048 ^ 4 (17 592 186 044 416) possible combinations. Some simple math tells us that log2(2048 ^ 4) is 44 (bits), and so the attacker needs to make 2 ^ 43 guesses, on average, to correctly guess the passphrase.

I'd suggest you aim for something stronger than the 44 bits in the XKCD comic. [NASA recommend](https://www.nas.nasa.gov/hecc/support/kb/using-gpg-to-encrypt-your-data_242.html) picking 5 random words of 5-10 letters in size, a similar method known as [Diceware](https://en.wikipedia.org/wiki/Diceware) allows you to control how strong the passphrase is.

I have created an example implementation of such a password generator, available [here](https://github.com/epidemics-scepticism/pypassword-generator).

You'll want to pick two of these passphrases and commit them to memory. The first passphrase will be used to [decrypt your hard drive](#encryptingyourhdd). The second passphrase will be the master password to your [password manager](#passwordmanagement), which will generate and store the rest of your passwords for your other accounts.

### Password Management
[KeePassX](https://www.keepassx.org/) is an open source password management tool available for Linux, Windows and OSX. It stores the passwords in an encrypted file on your computer and has tools for generating truely random passwords.

Using randomly generated passwords is important not just to keep your accounts safe but also to keep them distinct from each other. We know, thanks to Edward Snowden, that one way the NSA can link accounts together is through re-use of the same or similar passwords through tools like [X-Keyscore](https://theintercept.com/2015/07/01/nsas-google-worlds-private-communications/) and we can assume other attackers or people with access to dumps of compromised accounts could perform the same kind of attack. Hence if your online identities share common features in their passwords they could be linked together or worse, to your real identity.

### Compartmentalization
Try to keep your personal use of computing devices distinct from your anonymous use by the biggest possible margin you can afford (roughly: physical > virtual > sandbox). This reduces the possible damage that mistakes can inflict.

### Virtual Machine
A virtual machine is, in very simple terms, a computer that is emulated inside of your computer. The emulated computer is known as a _guest_, which is run on the _host_ (your actual computer) and they are managed by a _hypervizor_.

### Sandbox
A sandbox is a limited environment in which untrusted code is run. There are a few approaches that can be taken to limit the environment. Commonly reducing access to calls to the kernel is used to mitigate possible attempts to escape the sandbox. The visibility of it's access to resources like filesystem or network interfaces can be reduced through [_namespaces_] under the Linux kernel. Reducing the privileges of the process, it's access to kernel functionality and access to filesystem, network, device and other processes' resources helps contain any malicious functionality.

### Federation
Federated services are decentralized and consist of a group of users or service providers who talk to each other directly to exchange data by speaking a common protocol or set of protocols allowing users on one server to communicate with users on another server. An obvious example of this is email or other common examples are XMPP, Diaspora and GNU Social. This allows users to have greater control over who has what data on them and it increases the number of places that interesting data might be, making it more costly to surveil users _(if done right)_.
