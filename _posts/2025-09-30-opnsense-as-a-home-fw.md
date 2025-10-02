So since my ISP is finally going to transition me to [FTTH](https://en.wikipedia.org/wiki/Fiber_to_the_x), I thought that it would finally be time to get an OPNSense box.
<!--more-->
Not only because that means more control over my network, but also because of stuff like vlans, logging, advanced DNS and some other stuff.
But I had a problem, I had no standalone DSL modem!

"No problem, I can just buy one... [Oh](https://www.amazon.com/NETGEAR-High-Speed-DM200-100NAS-Compatible-CenturyLink/dp/B01HTAPPJE?th=1)."

Yeah, essentially all of the modems _if they are still being sold_ are expensive as fuck. I know that some combination routers have a bridge mode, but sadly I don't have such a device.

# 7412
Enter the FRITZ!Box 7412. It was made exclusively for 1&1/United Internet and released in 2014 as a low-cost option. 1&1 Had an **even lower cost** option without WiFi, even though this was just a software hack and could be enabled by just [re-flashing the stock firmware](https://www.youtube.com/watch?v=bpfjQ8epsD0). 

Anyways, I happen to own one of these! It has already gone through me opening it to get [UART access](https://openwrt.org/toh/avm/avm_fritz_box_7412?s[]=fritzbox&s[]=7412#serial), me flashing [OpenWrt](https://openwrt.org/start), [freetz-ng](https://github.com/Freetz-NG/freetz-ng) and some other stuff not worth mentioning.

But there's one problem. No FRITZ!Box (as of writing this) supports a bridge mode. **BUT** It does have something called PPPoE passthrough. This is perfect! the FRITZ!Box itself just acts as a media converter between ADSL2+ and ethernet, and the outdated firmware (FRITZ!OS 6.88) doesn't touch the outside internet at all! This allows my OPNSense box to actually establish the PPP connection.

_Note: Yes, I know that PPPoE passthrough =/= bridge mode. But it's the closest option and my ISP doesn't charge me extra if the FRITZ!Box itself is offline._
{:.info}

# 4
This is how I connected everything in the end:

![DSL/WAN -> FRITZ!Box -> OPNSense -> LAN](/assets/img/diag.png)
It's not the prettiest, but who cares.
As you can see, it's just DSL/WAN -> FRITZ!Box -> OPNSense -> LAN

First up, I gotta enable PPPoE passthrough on the FRITZ!Box. For that, I used [this guide](https://deer-it.de/fritzbox-7412-modem-bridge-mode-pppoe-passthrough/) here.
This also makes the FRITZ!Box handle the VLAN 7 tagging required by most german ISPs. Nice!

Now, onto actually configuring OPNSense.

![PPPoE settings in OPNSense](/assets/img/opnsense_as_a_home_fw/pppoe.png)
Really easy, just had to set the type to PPPoE and enter my user:pass.

Now I'm finally online again! The only thing left to do is to configure my unbound DNS. I personally use quad9, so of course, I set that as the upstream resolver.

![quad9](/assets/img/opnsense_as_a_home_fw/quad9.png)
Done!
# LAN FRITZ!Box Config
Last thing for me to do is to configure my FRITZ!Box 7530 (that was my modem/router/AP before) as an access point.
For that, I had to set it to function as an IP client:

![FRITZ!Box set to IP Client mode](/assets/img/opnsense_as_a_home_fw/fb_lan.png)
_Note: This was auto-translatred by firefox. I was too lazy to set it to english. Sorry!_
{:.info}

After that, I set it to use a static IP outside my DHCP range:

![FRITZ!Box set to 192.168.1.2](/assets/img/opnsense_as_a_home_fw/fb_ip.png)

Done! After testing on my phone, I can confirm that it is getting an IP from the OPNSense DHCP server and that I can access the internet.


Okay, that's enough for now! I will defo set up vlans and a network for all the untrusted stuff in the future 
*cough* when i get a new network switch *cough*

Overall, I'm very happy with this setup! It works, but it's mostly temporary until I FINALLY get fiber optic.

Thanks for reading! I know that this isn't one of these really exciting posts, but I just wanted to write about this