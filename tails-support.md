# Accessing Tails Support

This guide will primarily be based around accessing it _from_ Tails, but will generally also apply to other situations.

Tails' support channel is now located on the RiseUp.net XMPP/Jabber Multi-User Chat server.

XMPP, previously known as Jabber is a federated instant messaging protocol. The names Jabber and XMPP are interchangable but I will be using XMPP for the remainder of this guide. Just know that this also means Jabber.

To access it you will need an XMPP client, such as Pidgin in Tails, and an XMPP account. Note that the account _does not_ need to be a RiseUp.net account. Note that I called XMPP _federated_, this means that clients from different servers can talk to each other much like a gmail.com account can email a riseup.net account.

# Creating an XMPP account

## If you already have an XMPP account, [skip this section](#joining-a-chat).

There are many XMPP providers, most will support Multi-User Chat although some (like xmpp.jp) seem to have problems with it.

Picking an XMPP provider is a lot like picking an email provider, the threats are the same. The email providers can see your contact lists and can see your messages sent to them which is why it's common to use OTR for one-to-one chats over XMPP, to hide the message content from the server providers.

A large set of privacy conscious providers offer .onion accesses to their servers and also will federate between themselves over .onion to reduce the visibility of connections between servers to anyone trying to spy on communications between them.

In this example I'm going to pick The Calyx Institute as a provider because I know that they allow you to create accounts directly from the client and they provide .onion access and federate with other servers over .onion but other providers also offer this and should be considered, some of them are listed [here](https://github.com/nickcalyx/xmpp-onion-map/blob/master/onions-map.lua).

From the top bar of the Tails user interface, we want to select: _Applications_ -> _Favorites_ -> _Pidgin_.

Pidgin should now open, with two windows: _Buddy List_ and _Accounts_. On the _Accounts_ window choose _Add_. From the _Add Account_ window:
- Select _XMPP_ from the pull-down Protocol menu
- For the username pick a unique username thats preferably not linked to you, you might take your prompt from the existing accounts with randomized usernames that Tails has already created. You may need to add some extra characters or numbers to ensure that you have a unique enough username so you don't choose one that already exists.
- For _Domain_ enter `jabber.calyxinstitute.org`
- Leave _Resource_ blank.
- For _Password_ generate a new password, you might like to use KeepassX for this on Tails, available under _Applications_ -> _Favorites_.
- Make sure you save the username and password, as you will need them again in a minute.

Now go to the _Advanced_ tab:
- For Connect Server enter `ijeeynrc6x2uy5ob.onion`

Now, at the bottom of the _Add Account_ window tick _Create this new account on the server_ and click _Add_.

Back on the _Accounts_ window, tick "Enable" next to your newly added account. You will be prompted to accept the certificate for `ijeeynrc6x2uy5ob.onion`, this is fine since we're using the .onion we have already verified the servers identity, click _Accept_.

You will now be prompted for the username and password again, which you saved earlier. Re enter them. If all goes well, it should prompt you that the account has been successfully registered.

You can now _Close_ the _Accounts_ window.

# Joining a chat

Back on the Tails _Buddy List_ window, pick the _Buddies_ pull-down menu and select _Join a Chat_, on the new _Join a Chat_ window:

- Ensure the _Account_ is your new XMPP account.
- For _Room_ enter `tails`
- For _Server_ enter `conference.riseup.net`
- Edit your _Handle_ if you prefer, or leave it as it is, this is the name others will see you as.
- Leave _Password_ blank and click _Join_.

Shortly, a new window should appear entitled `tails@conference.riseup.net`, allowing you to chat to the Tails developers or community for support.
