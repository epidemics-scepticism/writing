An attempt to document commonly believed misconceptions about Tor.

Content will appear here once I have gained sufficient vitriol...

# Disclaimer
## Know your adversary
Not all of the situations or recommendations covered in this document apply to all adversaries and inversely following all of the recommendations in this document will not protect you from all adversaries.

This is a set of general recommendations, created as counter-points to common misconceptions about how best to use Tor that I've observed in the wild, talking to users and trying to support and assist users.

You should consider who or what it is you're trying to defend against and what they're capable of. Understanding an adversary's technical capabilities can be difficult to discern for users and is often specific to the user making it difficult, if not impossible, to create sound advice for the general use case. I've tried, where possible, to illustrate what kind of capabilities an adversary would need to exploit the various potential pitfalls but I can't tell you if your adversary actually has that capability.

# VPNs.
## Is a VPN more secure because Exits are untrustworthy?
### What does a VPN see?
* Who you are.
* Who you're talking to.
* What you and the other party are discussing.

### What does an Exit see?
* Who _someone_ is talking to.
* What _someone_ and the other party are discussing.

#### So what does this mean?
Well, the Exit can do everything the VPN can do to the traffic but it can't do it to you consistently or in an easily targeted manner. You are less targetable on Tor. Your VPN can target you, and do so consistently. The VPN also knows who you are.

### But what about...?
- The VPN can be trusted, I know because _magic_! So it won't tamper with my connection.

   The VPN isn't the only party in the connection. Your connection to the destination goes through a series of other connecting ISPs routers. Even if you trust your VPN and it turned out you were right, any single router on that connection can tamper with the connection, just like the Exit. Even if the router can't _directly_ tamper with the connection, visibility of the connection is enough for them to perform a "Man-on-the-Side" attack, by sending a response to your TCP connection opening before the other side has had a chance to response, they can inject content into the stream with Mallory outside of the connection and a complicit Eve in the connection.

- I connect to my VPN over Tor and pay for it anonymously.

   This has serious technical drawbacks that I'll cover later on, but now you have a VPN that doesn't know who you are but you better hope it stays that way forever and you never even make 1 mistake. If you slip up and send identifying information over the VPN, you just contaminated _everything_ you did on that VPN account, linking your identity to your VPN account. You really better have some good redundancies in place to both avoid it happening in the first place and ensure that when it does eventually happen, it is not catastrophic.

- I connect to Tor over my VPN.

   This is probably fine. It doesn't improve the anonymity provided by Tor but it might create more plausible cover for a local adversary watching your connection but it may also put your connection directly into an adversary controlled network and it's protections likely wouldn't stand up to close scrutiny. Careful observation of traffic flow patterns may reveal the kind of traffic that is being sent across the VPN.

## Indistinguishability
### TCP and IP stack metadata
VPNs function fundamentally differently from Tor. A VPN works by encapsulating a full IP packet. This means that when the packet is reconstructed at the other end of the VPNs connection and sent out, the IP and TCP metadata is indicative of your local system.

By comparison, Tor encapsulates only the stream data of a TCP connection. This means that when it is reconstructed into a TCP stream at the other side, the IP and TCP metadata comes from the exit, and is indistinguishable from all others users using that exit.

This metadata does not constitute a unique fingerprint but it can be used to distinguish between operating systems and even version of operating systems. For an example of a practical application of this, see [lcamtuf's p0f](http://lcamtuf.coredump.cx/p0f3/).

Imagine the situation where I can see traffic going into and out of a VPN server, lets say it has multiple users and essentially the inbound and outbound traffic is being mixed in ways that I can't distinguish (although more sophisticated analysis would certainly work, i.e. timing), instead I look at the inbound and outbound packets TCP/IP metadata, the packets coming in are generated on your local system and specific to characteristics of your operating system and setup, the packets that the VPN sends out are _also_ generated on your local system and will carry the same characteristics. As such I can split the set of users going into and out of the mix.

## Bottleneck or Wateringhole
The set of VPN providers who are "trusted" _(note: not the same as trustworthy)_ by the wider community is quite small. This means that lots of _"interesting"_ traffic passes through choke points which reduces the places on the internet you'd need to be able to view to target them all individually. By spreading FUD and paranoia you can get your targets to come to you.

VPNs provide _confidentiality_, they simply encrypt packets generated on your system and send them off to be reconstructed and sent from a remote location, and responding packets are, conversely, sent back down the encrypted connection. This does little to mask the nature of the underlying traffic. Many of the attacks on Tor look at traffic flow patterns and traffic volumes and since VPNs do not try to hide this information the attacks that work on Tor will work on Tor over a VPN, except now both your ISP _and_ the VPN provider is in a position to perform them, you've only increased the set of positions that an attacker can take to perform such attacks.

## Logging
VPN providers log, I've seen examples of VPN providers who log everything that their websites sales pitch explicitly states they _don't_ log (who connected from where and when, etc). Even _if_ the VPN provider _doesn't_ log it's likely that their upstream ISP is logging and [in the past this has been sufficient](https://www.bestvpn.com/blog/8383/earthvpn-user-arrested-cops-find-logs/) to deanonymize users.

Sometimes [they're doing exactly the opposit of what they promised](https://cdt.org/press/cdt-files-complaint-with-the-ftc-on-hotspot-shield-vpn/).

## Exceptions
VPNs _under certain circumstances_ may provide a plausible "cover" for your Tor traffic _but_ it's not very good cover and wouldn't stand up to further scrutiny. It exposes you to more risk so unless it is necessary _and_ the cover would be plausible in your case _(e.g. people commonly use VPNs to watch netflix in your country/area and such activity is generally given a pass)_ then do not use one.

Mo Hops Mo Problems.

# Is Tor broken or backdoored?
## Broken
### Not universally, at least
It's [acknowledged](https://www.torproject.org/docs/faq.html.en#AttacksOnOnionRouting) that Tor cannot protect you if the adversary can view both ends of your connection, over time they will be able to determine the circuit belongs to you. As such an adversary who can see a large fraction of the network will likely be able to tell, with some level of certainty _(less than 100%)_, which circuits belong to which user.

In at least [2011 the NSA](https://edwardsnowden.com/wp-content/uploads/2013/10/tor-stinks-presentation.pdf), in a slide deck, indicated that they couldn't target a user for deanonymization and that with manual analysis they could deanonymize a "very small fraction" of Tor users.

I would be incredibly surprised if the NSA, and other military intelligence organizations, had not made significant improvements in this process during the last 5-6 years, given it's rise in popularity. I don't think they are yet at the level where they can deanonymize all Tor users all of the time, but we can really only speculate.

For most people military intelligence isn't their direct adversary, most people adversaries are certainly not capable of that kind of attack.

### Eve is cheaper than Mallory
*"Anything that forces Eve to become Mallory is already a win."* - halvarflake

We see that powerful, well funded and well connected adversaries _(read: FBI)_ are still resorting to acting as Mallory to score wins. If they could act as Eve, they would do so because it is cheaper, more predictable and yields consistent results.

The FBI is dropping cases against suspects rather than reveal the details of their vector for acting as Mallory. This suggests they have a strong requirement to continue acting as Mallory and are concerned with losing that ability. (Exodus Intel no longer sells to them - ["Brown said Exodus does not work with that agency any more because of this case."](https://motherboard.vice.com/en_us/article/ceo-of-company-behind-tor-browser-exploit)).

This suggests they do not have the potential to act as Eve, at least not in a meaningful way. Instead they must compromise end points rather than the Tor protocol for the time being.

## Backdoored
### Open Source
Tor's code is open source, it has been reviewed repeatedly and has been the subject of many academic papers looking for weaknesses in the protocol. I'm not aware of any evidence-based (read: sane) accusal of a backdoor in Tor software.

### Reproducible builds
Tor Browser is build reproducibly, this means that the software that Tor distributes can be checked to ensure that when the publicly auditable source code is built under specific, inspectable and reproducible conditions it results in *exactly* the same binary file that the Tor Project distributes. This ensures the source code maps to the binary file and there is no slight of hand on their part to distribute backdoored binaries.

# ExitNodes, ExcludeNodes and GeoIP.
## GeoIP is bullshit.
### How does it know?
It doesn't.

### Why does it think it knows?
GeoIP works by looking at who the IP belongs to, often this can be as simple as just parsing the address on a whois record. Whois records are not authoritative _(Whois is not very resistant to attacks. You can lie on it and it's a plaintext protocol)_. If you look at the Tor relay [BC630CBBB518BE7E9F4E09712AB0269E9DC7D626](https://atlas.torproject.org/#details/BC630CBBB518BE7E9F4E09712AB0269E9DC7D626) you'll notice that according to GeoIP it's in Liberia. It's whois record states `country:        LR`. This is where the evidence for it's being in Liberia ends and in fact you can show evidence that it is _not_ in Liberia with ease and trivially discover it's likely location.

For more examples of similar fakery by VPN providers (to pretend they have servers in different countries) and other questionable entities, read Benjojo's blog post: [A surprising amount of people want to be in North Korea](https://blog.benjojo.co.uk/post/north-korea-dprk-bgp-geoip-fruad).

### IPs aren't tied to a location.
Another thing you will notice is that often databases take a while to be updated. Blocks of IP addresses can change hands between companies. One day they're being used in France, the next they're in the Mexico. I ran a relay that for a period of months was incorrectly reported as being on the wrong side of the planet. This is not a rare occurrence.

### Reduced circuit paths.
When you try to exclude certain nodes from your circuits you reduce the number of possible pathways that you can take through the Tor network. This makes your circuits different, and more limited, from the set of all other Tor users. There are cases where an adversary could fingerprint a user based on any policies they have set. For example, if an adversary could inject content into a page that caused you to load resources from a large range of first party domains on Tor Browser, you would use a different circuit for every resource. An adversary who could see those resource fetches could then see where you do and do not exit from. At any given time there is a certain statistical probability that you would exit from a node in the US, a probability for Germany, a probability for France and so on. They could see if your distribution of chosen exits matched this statistical likelihood and see which were missing. This fingerprint would persist and would put you into a smaller set of users than the general set of Tor Browser users.

### Circuits pass through intermediaries.
If you wanted to exclude a certain country from your choice for some perceived good reason, this will not work. Circuits will happily be built on networks that traverse resources within these countries. The paths that circuits take between relays is entirely outside of your control and can be malicious redirected through BGP hijacking or similar methods.

### I suspect this relay is doing bad things, so I excluded it.
If you think a relay is being bad, instead of adding it to your ExcludeNodes you should report it by emailing bad-relays at lists.torproject.org.

If possible provide the relay's fingerprint and what bad thing it is doing, steps to reproduce the bad behavior are also very helpful. This will mean the relay will be ejected from the consensus (if it's found to be behaving badly) and will protect all Tor users, not just you.

If you don't have meaningful evidence that it is doing bad things, this is likely paranoia. It is irrational and it is harming you.

### So what does this mean?
Reducing the location that you will or will not exit from harms your anonymity. There are cases where an adversary can use this as a method to fingerprint you and reduce your anonymity set.

Attempting to avoid countries because you don't trust the government in charge of them is folly. Believing this helps against any kind of well funded or technically capable attacker is also folly. It is only harming you.

# Using it properly.
##### _(or how to score maximum grukgqzxpoints)_
## I solemnly swear that I am up to no good!
### Use it consistently.
If you only ever use Tor when you are performing activities that you don't want linked to your identity, then anyone who can observe when you use Tor and can see the results of your use will be able to build up a revealing pattern over time. This is generally weak for a purely passive adversary but an active adversary can do confirmation attacks with this. For example, if you are using a protocol that reports presence (like IRC or XMPP), they could wait for the party they suspect you of being to be active, then ask your ISP to drop your connectivity. If your psuedonymous identity drops when you drop they have a hit. Similarly an attacker without legal authority can perform a ["sniper" attack](http://www.robgjansen.com/publications/sniper-ndss2014.pdf), by trying to denial-of-service you or your guard node and performing a similar analysis.

### Stop snitchin'.
A real world example of this is how [Hector Monsegur](https://www.rt.com/usa/lulzsec-snitch-sabu-sentencing-288/) in co-operation with the FBI told them, through mouthfuls of donuts and FBI reproductive organs, if [Jeremy Hammond](https://www.freejeremy.net/)'s suspected alias was online or not. They correlated this to physical surveillance of Hammond and between his being AFK to his alias being offline, despite his proper use of Tor.

### The metadata is the message.
Another real world example of this kind of flaw is an old adage of Greenpeace's use of PGP in emails_[citation needed]_. They decided to only use it when they were up to no good, that it to say when they were planning protests they would use PGP to encrypt emails between each other to keep the details "secret", and didn't use it the rest of the time. The fact that the emails were encrypted and who was emailing who leaked a lot of information to observers about where and when Greenpeace were likely to act.

## Tor isn't a *(cyber-)*magic invisibility cloak.
### STFU is the best policy.
Tor provides a base from which other actions to hide your identity can be applied in a meaningful way, it allows you to anonymously contact resources on the internet. It does _not_ stop you running your mouth to those resources on the internet and _fucking it up_. Do _not_ give away personal information while acting psuedonymously.

If you _have_ to give away personal information, ensure that _before_ you do give it away and after you _have_ given it away you perform a clean break between the identities. That is to say, if you want to login to your person email on Tor Browser: - _before_ you go to your personal email account use `New Identity` to break from your old browsing session, then login to your person email and perform whatever tasks, log out of your personal email then use `New Identity` again _before_ you continue to browse. This creates distinct breaks in linkability between your personal email account and any browsing you performed before or after it. _Be aware_ that if you are acting as NotAlice then NotAlice disappears, then Alice reads her email for 5 minutes, then Alice disappears and NotAlice returns this is going to look mighty suspicious, especially if it occurs repeatedly.

### Acting locally.
If your activity, while performed perfectly anonymously, is an activity only likely to be carried out by a small set of people, either because only a small set of people have information (e.g. a leaked document) that would allow them to perform the activity or benefit from the activity will reduce your anonymity set dramatically. This, coupled with the smaller still set of people who meet the other prerequisite and also used Tor at the time of the activity further reduces anonymity. In [this case](http://www.businessinsider.com/harvard-student-used-tor-for-bomb-threat-2013-12) it was sufficient to deanonymize them.

Similarly, if you perform actions around something which is geographically local, it's likely the actor who was using Tor is also local and as such your anonymity set may only be that of your local area and in many cases this may just be you.

### Some software isn't fit for purpose.
Applications will snitch on you. Wittingly or unwittingly, directly or indirectly.

They'll tell people what timezone you're in, what time you think it is in your timezone, what specific version of a bit of software you're running, what operating system you're running, what _version_ of the operating system you're running, your computers name, your users name, if you've talked to the other party before, when you talked, who else you've talked with...the list of possible data leaks is endless.

All of these possible variations, while not all applicable to all applications, create an inconsistent anonymity set. It has a texture, if someone were to run their *(cyber-)*finger over it, it would present bumps and recesses. We don't want that, we want our anonymity set to feel as smooth, homogeneous, and indistinguishable as possible to any probing *(cyber-)*fingers. To hide in a crowd, you have to look like the rest of the crowd.

Some applications, even some *security* applications, still check for and download *unsigned* updates over plaintext. This is not safe in *any* scenario but becomes a more obvious problem on Tor.

To this end we should (where possible) use an as similar as possible set of tools. This creates a one-size-doesn't-quite-fit-all scenario which causes discomfort but if you're serious about anonymity, this is important.

### Faulty camouflage
Trying to make Application A look like Application B is folly, for a sufficiently complex application (looking at you, HTTP clients). I see a lot of crawler doing stupid things when crawling onions, like setting their user-agents to be the Tor Browser user-agent (which they invariably forget to update) and little do they know that their _behavior_ makes them stand out. Do you use a Keep-Alive connection? Which order do you request the resources in? What kinds of content-encoding do you accept? What kinds of content encoding do you accept _in a given context_? These all distinguish Application A from Application B and make you less anonymous, not more anonymous.

## "Transparent Proxy" and "Tor Router"
##### Anonabox, Invizbox, Invizbox Go, iCloak, TorFi, Cloak, iCloak, AVG Chime, and many, many more...are all full of shit.
##### (By which I mean they're lying profiteers selling you snake-oil and vaporware and even the FLOSS ones are mostly doing it wrong)
### Why are all these "Tor Routers" bad?
First of all they all set out to make it "easy", to this end they created an N to 1 mapping for a transproxy enforcing packet filter, so that multiple people could connect to the device and use it at once. To do this they made it connect _out_ over ethernet and provide a wireless network for clients. This is the first problem.

### Wireless cryptography is straight outta the 90s
Without even getting into the problems with WEP and it's IV re-use (No one should be using WEP, no one should have been using WEP for anything for _at least_ the last 10 years). WPA-PSK and WPA2-PSK also have a problem. PSK is the standard setup that's used by pretty much all home networks and the one thats most likely used by Tor Router type devices. It is where you have some key that is shared amongst users, and therein lies the rub. The shared secret is the _only_ shared secret value, no public key cryptography is involved.

This means first of all that there is _no_ forward-secrecy. If Eve can capture the 4-way handshake for the WPA connection, she can decrypt all the traffic for that session, similarly anyone who you are sharing the connection with can decrypt everyone else's traffic as they all have the same pre-shared key. The pre-shared key and the 4-way handshake is the only value required to compute your session key, which is used to encrypt your traffic which is visible to anyone within range of the wireless access point.

Secondly, and most critically, this weak cryptography is all that is protecting your traffic _before_ it reaches the Tor network. You are totally unprotected at this stage, if you're using a .onion service which uses HTTP the "end-to-end" of the cryptography is starting at the other side of the wireless connection, you are broadcasting this data without any protection. Similarly even if you use TLS things like SNI will indicate what hostnames you're connecting to. Consider also harm caused by man-in-the-middle attacks over the WLAN, where your traffic is unprotected before reaching the access point.

Thirdly, this lack of forward secrecy means that anyone who has recorded previous wireless sessions will be able to retroactively decrypt them if at some point in the future they are able to recover the pre-shared key.

WEP is similarly flawed, except worse in every possible way.

#### But I picked a strong passphrase!
Alright but there _may_ be a chink in the armor that is still attackable, WPS. The PSK, no matter how strong, _may_ only being protected by a 8 digit pin, of which 1 is a checksum. So it is providing `10 ^ 7` (or about 24bits~ of security) possible combinations. Once the correct PIN has been guessed, the pre-shared key is provided to the user. Further, [some implementations](https://sviehb.files.wordpress.com/2011/12/viehboeck_wps.pdf) allow an attacker to guess the PIN in two parts, so it provides only `10 ^ 4  + 10 ^ 3` (or about 14bits~ of security) possible combinations.

#### I don't have any other option! How could it suck less?
Use some kind of forward secret tunneling to access the device, connect to the tor router device over wireless, using OpenVPN or using SSH to setup a proxying, for example. This way even if the WPA is broken, you still have a forward secret tunnel protecting your traffic so that it can't just get passively pwned out of the air if the PSK is cracked.

Alternatively if this isn't an option because the device you're trying to use is utter trash (like an iPhone) then you should change your PSK for every session, make it strong and never ever allow anyone else to connect to the device, use it _only_ for yourself.

Follow the [PORTAL](https://github.com/grugq/portal)-like ([PORTALofPi](https://github.com/grugq/portalofpi), [PORTALofRaspbian](https://github.com/compoterhacker/PORTALofRaspian), etc.) setup, if you can connect to it over Etherenet, and have your outbound connection from the device over Tor be over wireless, then your wireless traffic will already be protected before anyone without physical access to the device can sniff it, making the wireless cryptography irrelevant.

### Transparent Proxying will always be sub-optimal
It's an option that exists purely because some situations just don't work (applications that don't support SOCKS5, etc) and this is probably the least sucky way to do it. You should never _prefer_ trasparent proxying. There are a few reasons why.

#### Transparent Proxying lacks context
It doesn't know what applications are making what requests, at best you could isolate by things like the user who is running the application but imagine a scenario like Tor Browser where all the traffic is coming out of single application to a single proxy port. Tor Browser, because it's been made to work with Tor, is able to use SOCKS5Auth based circuit isolation mechanisms, this isolate requests based on the first party domain of the tab the request is associated with. This means no single exit handles _all_ the requests you're making. Tails also makes use a multiple SOCKS ports, each of which will be isolated from each other and different applications use different ports, meaning they do not share circuits.

When you take your whole operating system and stick it behind transparent proxying, everything goes over Tor if it can. Everything that goes over Tor will, by default, use the same circuit. Your operating system updates, your email, your browsing, and fetching information about media you play will all share the same circuit. The exit node could connect all of these things together and link them to a single entity. This means things intended to be anonymous could be linked to things which reveal your identity, or link distinct psuedonyms. Similarly to this see "Not All Applications Are Fit For Purpose" above.

Further, the set of applications and how they behave can act as a fingerprint that an Exit or potentially a careful observer watching Exit traffic could piece together. This is why doing this without using an operating system with a good anonymity set (with many users with the same set of software and thus the same fingerprint) will make you _at best_ psuedonymous. This may suit your purposes but if the fingerprint is observed _outside_ of Tor then it may be possible to link your psuedonym to your real identity. See "Use It Consistently".

It _should_ only be used as a last resort if there is no other way and it _should_ be used sparingly as possible, _prefer_ to use native SOCKS5 or the `torsocks` wrapper.
