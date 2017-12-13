# Inducing DNS Leaks in Onion Web Services

_(Note: I will publish the code...after it's gone through some thorough cleaning up and the threading and concurrency mess I've created is resolved...hopefully soon.)_

Some time over a year ago now (I don't recall the exact dates but the timestamp on a backup copy of my code suggests around September 2016), inspired by DNS based [Canary Tokens](http://canarytokens.org/generate) I decided to follow a similar method to see if I could get remote HTTP services hosted as a Tor onion service to resolve a supplied domain name.

To skip the boring backstory and awful prose, [click here](#findings)

My code was naive, the method was hacky and the list of onions that I tested against was relatively small (around 1000). My results were similarly poor, out of the 1000 scanned I got around 3 or 4 hits, and all but 1 had resolved through Tor suggesting that they were enforcing Tor for outbound connections. All it eventually showed was that some DarkWeb hosting services website was making resolves through a DNS resolver at OVH.ca, which suggests where it was hosted. An adversary with subpoena powers could have discover the location of the server (said onion is now defunct, for reasons unrelated to my research).

I reported the results to the Tor and PrivacyTech communities on IRC, I fired off the code to Sarah Jamie Lewis suggesting that it might be a worthwhile module for OnionScan to consider. I then shelved the code, since my results were poor and there was little interest.

About a week ago [an article was published](http://digitalforensicstips.com/2017/11/using-burp-suites-collaborator-to-find-the-true-ip-address-for-a-onion-hidden-service/) by [Matt Edmondson](https://twitter.com/matt0177) about using a tool called Collaborator Everywhere  (created by [James Kettle](https://twitter.com/albinowax)) to perform the same kind of attack and successfully causing an onion service to resolve a supplied hostname. Collaborator Everywhere is a tool that follows a similar tactic, generating unique codes to be inserted into headers with each request and watching for connect-backs to those unique addresses, by DNS, HTTP or otherwise. It was far
better researched and designed than my original tool and  managed to get a positive result. You can see more about the tool in his [Blackhat 2017 presentation](https://www.youtube.com/watch?v=zP4b3pw94s0).

Inspired by this, I decided to revisit my original code to make it suck less. My new code  created more unique tags (although collisions hadn't been an issue), tie the tags to a specific header (so we know which headers work and where) and sends each header as it's own request, to try and rule out cases where one header invalidates another (e.g. the webserver sees two hosts and only resolves one, but both might work). While it's still not as likely to get good coverage of all potential targets to induce DNS resoltion on a webapp as Collaborator, it is better suited to scanning large sets of onions rather than a targeted engagement.

The code still needs work and I have some ideas of what would make it better:

 * Integration with a crawler, to cover more potential targets (e.g. maybe it only triggers if the request is passed on to a PHP/CGI interpreter).
 * Integration with Tor's Control Port, so we can check if a proposed Onion has a live descriptor before we waste time on it (HSFETCH).
 * Better handling of time-outs, my scans took a lot longer than they should have due to the naivety ("simplicity", I tell myself) of my code.

Armed with my new code, and a copy of Ahmia.fi's [onionlist.txt](https://ahmia.fi/static/onionlist.txt), I started off the scanner. More quickly than I anticipated, results started coming in.

"Okay", you ask, "cool story bro but what the fuck did you actually find?"

### Findings
Out of all 26741 on the Ahmia onion list, many are now defunct and aren't available, I got 76 resolves outside of Tor. The overwhelming majority of which were due to the `X-Forwarded-For` header.

     74 X-Forwarded-For
      1 X-Forwarded-Host
      1 X-Real-IP

But we shouldn't take that at face value. When looking through the list of affected onions, I spotted some old friends. Onions that I had previously scanned years ago when searching for onions that exposed /server-status. Those results gave me a group of approximately 80 onion addresses all of which claimed to offer the usual "dorkweb"  services: guns, drugs, fake id, credit cards, bitcoin tumblers, bitcoin exchanges, etc.

By grouping them together based on /server-status  output, I was able to tell they were all hosted on a single server.

This is the case again with the X-Forwarded-For  leaks. Infact 71  out of those 74 are all part of the same cluster of onions, which all have similar web page characteristics and link to each others "services".

    4tcuts4ajnwqxvzo63572cpnci7pdsogguh4z5dxpytc6qz5 http://applesf6emggp2pz.onion/ X-Forwarded-For
    eiwagfgslqd6zdfyfapgjybbifqu6muczep7uiygyafenaqu http://amazonfkuuy6g3ou.onion/ X-Forwarded-For
    qms4rvjd3thdyw6icvtzfchwmma3ohkrfqlqkusrw2fzyf4s http://skimmerkvmpi5vyn.onion/ X-Forwarded-For
    pmvp5nkvrlva5omahnbuqb656isuim3msivah5e46yyrxhug http://coinpigih6i444lm.onion/ X-Forwarded-For
    24ahjms4pk2lafseetpcmgjabembfehfhjxz5mvucvhomqxf http://electrotev3tgo2p.onion/ X-Forwarded-For
    x4ijw4br24orhbxmgxp2y22bhsmb3dyowvzmabb6rvajrprs http://russianyhluzsk53.onion/ X-Forwarded-For
    lidnyv346jyda7lqk6zof5z2zyeo5ztspx5e5l5i5i5hqrfi http://bitstorenctdwhmo.onion/ X-Forwarded-For
    f6q2bmn6zr5v77ur4n5wxyv5hiwdbgkixb5gtjg4xa2odzry http://bryemnetihwcflrs.onion/ X-Forwarded-For
    42wdy5c5zke6dbtrnfzcsv2cv5wkfn3juri4qkh72adlawql http://walletbjvdecnjgp.onion/ X-Forwarded-For
    4h2ylfempojcvqsf4n532r66wnekoixeraosnfn4ld2lg2f4 http://blenderri3sud4e6.onion/ X-Forwarded-For
    l5pbyqafbvniujjmynx3qndocbrxnay4z74tqjxwb24grlqq http://loundryslz2venqx.onion/ X-Forwarded-For
    h2yjajexi5dzbs3f2mip3vnxf3tmj6y5jhkf373wk52d5o2f http://btcwash7jmra3hgm.onion/ X-Forwarded-For
    xnoj76lhuutdow4rlx7xm6rpfgrwi7ion4sfknng62epr4mn http://laundrymy244rcwn.onion/ X-Forwarded-For
    fzdcw7ypgfxtj7gy7ymptrtjtc25nn4247vo26eis373m4tt http://mixer6nyvxalc252.onion/ X-Forwarded-For
    drna4wj6cluuwo4bzugb3lryh4lbffva2stiijdbzunmyknb http://buybtcstbl2d3igz.onion/ X-Forwarded-For
    276d5kwhjamqtdr7vhe5xdzk5l3mru2obxlwqtityjut2ckl http://mixerrzbgcknjzk4.onion/ X-Forwarded-For
    i35mvfob2pkyvszsvbv5amdlugfp6aoibdq64zwgmutxs2vf http://fogwalletgw4g2nc.onion/ X-Forwarded-For
    vr37arly4m52u427uiwzo7efgqsp5pghv3b44udwwig4ausf http://bettorzztykidrx2.onion/ X-Forwarded-For
    ntmll3w5ljhievnqfwqdzfrlcdd72siyar3gjjanj2oeg2bd http://cards5yvy44gucvo.onion/ X-Forwarded-For
    qsz74bmnv3rbfyo53ticu5fb22t3zelk2pbqixvxi6drtwnw http://cleancoikmh6uamc.onion/ X-Forwarded-For
    uexsxg4lppd5sit2spvketzz32we6kpapdh4fkuo24fhbthu http://bitmixergv5vvbza.onion/ X-Forwarded-For
    zihqrnmtmjr2s2s4dgfchdo4ybpxoogvlggcewb2ntfdxw35 http://ccvendorwepczh7c.onion/ X-Forwarded-For
    crfr2gkx6hjekceg3yh5bikubtz5wk257m6clig6ljjkipu2 http://payshielgjp4lsmx.onion/ X-Forwarded-For
    xo2lm25i35ilu2loz2wccfbpe6ucy3ohw23ok3suhc7mub5b http://spacechadxxpkf6t.onion/ X-Forwarded-For
    ymsj2lakxjnwtrnjqxcqwrozan5cktfhy4zsa25mucplmwkm http://dumpstreedhgfndk.onion/ X-Forwarded-For
    e7nqhdvokkvrahmfexwf4ifxhbhylln77vd3ikwou3l3bcmt http://acteam2nmbucjnht.onion/ X-Forwarded-For
    n2od5ex2tqlcdjphkhunyc2lsjpjbsg6xbnk5bfh2ba5v4zr http://dumpsmarslfo4h5g.onion/ X-Forwarded-For
    bjgjtyinwnwmhdqxtt2imea5pezxjva6k4a45pvqucegqsoe http://betcoinahk4j27yb.onion/ X-Forwarded-For
    bgzljwyagxjonj3b2ni6smutfo2ww3qs6sba7mqcfahtmwhn http://ccpalsto5gglun22.onion/ X-Forwarded-For
    spvlso5p4rmi6mewvq7sfpahcpyqcybk5ertv6uyanfzq5qb http://vendorcugc6oppvb.onion/ X-Forwarded-For
    f3b44zmdsgwciz2urjg3kz5li6q2jllgdiowabwfnz53yld6 http://plasticmrjhmyrxt.onion/ X-Forwarded-For
    75uyejspak5grsy6xx2ixo2pchwsucfdu5jozw4pj5d6s2zs http://pistolcqex2ecr5r.onion/ X-Forwarded-For
    57bewx5nod6if6rezketrcjbk7hg5z54wavhslt2doapcyoa http://armoryohajjhou5m.onion/ X-Forwarded-For
    r4qfpsqln5z3z54h2on7basfyt6umqjn5qxz77pdk2kuks3k http://paypalfhnohwoy6b.onion/ X-Forwarded-For
    pouwjthor6ehzyd5wtbtw3ed7odfdx237vhrga7qwjv5dcfb http://weaponstrxqniqrt.onion/ X-Forwarded-For
    qy7ny6eqvcwhjwngmawhrjqsyh7rzhfcooc6ht3yfg3nlnfl http://luckp47s6xhz26rn.onion/ X-Forwarded-For
    mu4vjea44hkpviyk3f3tm4jcc4tuitbd5j64ftled6gcc5ez http://drkseidwayn6uc5x.onion/ X-Forwarded-For
    vthjspa644wourbu52kq64pbtnquimapjqtb7rjxaompsuvp http://hackeroql4l2mejs.onion/ X-Forwarded-For
    yznb5amhk6flvjdoa6h5byttjpmky3y7sgyztyy3cwlsxvjb http://hackerrljqhmq6jb.onion/ X-Forwarded-For
    ncpox5bghmdnvwzpyx4mz75a34wmi3jhd7zo6nc3zn6dtnjm http://fakeids5bps3l6qb.onion/ X-Forwarded-For
    6xjfn72tyska57oeiitqztf2tdhl3o6syxhadx46oulcrwco http://fakeidjgjmadhyr6.onion/ X-Forwarded-For
    lacxnbus5w5q5jtrpwnvnpzczxynepolncq2d4pdsjkwnvc2 http://ukpasspprmwaqrsd.onion/ X-Forwarded-For
    kwbpvvwzczl3jer5ojqwq35nxaybai7fh72de3zmop4pxkpb http://countermltd42g4x.onion/ X-Forwarded-For
    jva5wwq3iff7ickne54qwbkvovwgrrmcmx57tngezzmzovmv http://passporxakpmzurx.onion/ X-Forwarded-For
    lrdovbpinkjfndcaf2m4a4bbqeqtz5vt264aftf42bu2f2gt http://replicaf6cjadwxs.onion/ X-Forwarded-For
    d2ey62x5oqrqdjgjmgjaksu7ucd6ro4nehlti4beiguzvgqh http://cityzenp4d2eytjh.onion/ X-Forwarded-For
    qigg2vrglxwubc45ky4tpwpbwkin3bumdjisbu6dus6kmv3m http://counterfxhjvtrlw.onion/ X-Forwarded-For
    zi4pl65lpdjvmpfkveowv5ln62xbcxov2srm4olbodvpkq7z http://cfactoryxecooa6l.onion/ X-Forwarded-For
    ryedmn6p2og6zxzjsa6bz7ong3prvmz7jexxofay7qtyeakp http://crackersccqxsmdb.onion/ X-Forwarded-For
    fiytezkgb7aqydxgn7q2q55b4vd4ngwkmwps6egbefo5thor http://drugszun7tvsgsaa.onion/ X-Forwarded-For
    6f6hcn4fabnlslnoff34cj3jwwxqxljiwkitlkej6ujvffug http://bitphar76n5t3qag.onion/ X-Forwarded-For
    rmyzvxl6b5hhzggyc7ukvkyaj4p5xtd3p445tqeplaonbawd http://psyched25pydrgul.onion/ X-Forwarded-For
    nd4qmn3grogg6x4apmtgql5krurpes5ccbfdkvisflzmwz2l http://eucannapggbtppdd.onion/ X-Forwarded-For
    5hs23yhw2tokxpmmzugqzqkkigsqy3znpycnw3rbsuqe4f23 http://dedopedhvmcsxylb.onion/ X-Forwarded-For
    anbwrjdw6drow6azltt4nw7bqabsydr7xvjtzvpgt3h4vmau http://cannabi4ewmalq3g.onion/ X-Forwarded-For
    3jqfx3gakdpvr4y63ykfouqpgpytxotprnvf5s7ltmohuquu http://kamagraujn45ja5w.onion/ X-Forwarded-For
    egytbghekrni6bw4hbpxudqy5db4d6ya2hdbarq2hfwqx2ms http://smokerhv5hlklzh2.onion/ X-Forwarded-For
    abtrmxtkqisf5ml5qbsxu6bhncra6ski2bjbvsdxccwsq5iz http://pharma5jbbmwjoo3.onion/ X-Forwarded-For
    q5os5tlrdxldhbfjsajzrmdndbolciijjuvfwjfoepzswk7w http://cocahze7fqy4qwwx.onion/ X-Forwarded-For
    izgnovbvgkhxgqtdhmkxqiszgrowlnov3pzfm2fcrruvkind http://maghrebwzbkucctg.onion/ X-Forwarded-For
    afow4ljm5jwxrlpmo2x4t443xfbbda7l6aggkcr56smakk7j http://amazingd64el2zty.onion/ X-Forwarded-For
    xqpnv4v2qplgkvt64fguwatagn5aaq3ljvx345j5zbqh3zvq http://cocain2xkqiesuqd.onion/ X-Forwarded-For
    eqwxasytspoh57f6py7zgbrsqiigcq3aab7qtpwxaf5hpsyb http://safebtck4qejiero.onion/ X-Forwarded-For
    rdf7cxjwvzkuwisuodtojqeigj53vpp3qde4v6gxcrwbpvkm http://market77w4eaafd3.onion/ X-Forwarded-For
    f5mkhvvkob4a2wb3mlwodfbxakncfvnscej4q2qdn5j6il55 http://weedsragjdyuimdm.onion/ X-Forwarded-For
    npvs5iesxljf3osrg7mkmxwkbtxhnybnnw6sxuiezw7w64ga http://mollyworh4524fop.onion/ X-Forwarded-For
    n4jagcgg45j5ppfbc3qm276g2pqauq4skvw5pb73cg576rur http://weed46fkpfzc3lvi.onion/ X-Forwarded-For
    iz2n6rjdh5pswq7fkffi6veo3f434okuvxejpuyei6nef2g6 http://mystorea4mbkgt76.onion/ X-Forwarded-For
    ggnniuo7knkgm55eozqo74pq2bjafyi4jwgwxgxu24pg7wgg http://cmarketsiuhtiix5.onion/ X-Forwarded-For
    t3yv4dzwr7ww6pk6jkzls6odwsou334gowhsdn33kyqu2tyk http://chemspain7iw2zby.onion/ X-Forwarded-For

These onions are either bitcoin scams (most of the services want bitcoin payments, up front too I'd bet) or are law enforcement honey pots...or maybe they're the worlds worst, and luckiest, dorkweb hosting provider. The key to seeing this is russianyhluzsk53.onion, which claims to be a wiki/directory of onion services but in reality links only to other services within the cluster.

Other interesting findings include that two Gitlab services hosting on onions, although one did resolve over Tor, also performed an HTTP connection (also over Tor) to a listening HTTP server in response to an X-Forwarded-Host  header.

    #My scan logs
    onbei2lau67hutbfkshtxwzn42aftllgv2h7xveii3lti7m4 http://git3mre26f4myq74.onion/ X-Forwarded-Host
    #Nginx logs
    [error] 408#408: *3383 open() "/usr/share/nginx/html/users/sign_in" failed (2: No such file or directory), client: 185.86.149.97, server: _, request: "GET /users/sign_in HTTP/1.1", host: "onbei2lau67hutbfkshtxwzn42aftllgv2h7xveii3lti7m4.[REDACTED]", referrer: "http://git3mre26f4myq74.onion/"
    #My scan logs
    cpk3bcrbmdmrpgaojv3f5np3dnx4wtthntcvjmq5i3b74syi http://haklab4ulibiyeix.onion/ X-Forwarded-Host
    #Nginx logs
    [error] 408#408: *3513 open() "/usr/share/nginx/html/users/sign_in" failed (2: No such file or directory), client: 197.231.221.211, server: _, request: "GET /users/sign_in HTTP/1.1", host: "cpk3bcrbmdmrpgaojv3f5np3dnx4wtthntcvjmq5i3b74syi.[REDACTED]", referrer: "http://haklab4ulibiyeix.onion/"


The one response to `X-Real-IP` seems to be an onion mirror of a cam/porn site.

In the interest of not being a cop, I'm not going to explicitly name some other vulnerable services but which resolvers you use can say a lot, using your ISPs resolvers will often give an indication of your location and your ISP, narrowing down where you're connecting from significantly.

If you're running an onion service and are doing so because you have strong anonymity requirements then you must  enure that you're filtering outbound traffic, preferrably just dropping it entirely rather than forcing it over Tor but never  outside of Tor.

### Post-Script
Amusingly, someone seems to have spotted my scan while it was in progress and
probed at my listener (over Tor).

    [error] 408#408: *4208 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 176.36.117.185, server: _, request: "GET /favicon.ico HTTP/1.1", host: "[REDACTED]"
    [error] 408#408: *4208 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 176.36.117.185, server: _, request: "GET /favicon.ico HTTP/1.1", host: "[REDACTED]"
    [error] 408#408: *4211 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 176.36.117.185, server: _, request: "GET /favicon.ico HTTP/1.1", host: "woozawuzza.[REDACTED]"
    [error] 408#408: *4211 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 176.36.117.185, server: _, request: "GET /favicon.ico HTTP/1.1", host: "woozawuzza.[REDACTED]"
