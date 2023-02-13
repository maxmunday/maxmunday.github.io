---
layout: post
title: "Basic Security for Everyone"
date: 2017-01-28
description: "IT security guide for everyday users."
number: "0x0b"
---
\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-  

Every person with a computer, a phone and the Internet needs to be aware of the security of their devices and data. Access to someone's email address allows password resets to such services as Internet banking and PayPal among other things. Something as simple as losing an unprotected phone can result in serious financial problems.

The most important security component for everyday users is a little bit of knowledge paired with some simple configuration of your devices.

Friends and family always ask me for tips on basic computer security, and I have witnessed some poor practices by these same people. For these reasons I have completed this guide that will go through basic security for everyday use.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-


\-\-[ SUMMARY ]

\* Be very careful about what you click on.

\* Be very careful about the information you put online.

\* Update all firmware and software.

\* Use strong, unique passphrases. Use a password manager.

\* Install [uBlock Origin](https://github.com/gorhill/uBlock) and [HTTPS Everywhere](https://www.eff.org/https-everywhere%20) on your Internet browser.

\* Use antivirus and anti-malware software. I use [BitDefender](https://www.bitdefender.com.au/solutions/free.html) and [Malwarebytes](https://www.malwarebytes.com/).

\* Set [UAC](https://www.tenforums.com/tutorials/3577-user-account-control-uac-change-settings-windows-10-a.html) to full in Windows.

\* Set strong login password and encrypt your drives.

\* Change default login password and SSID for your WiFi router.

\* Use WPA2 + AES for your WiFi encryption and set a strong password.

\* Use a secure lock screen for your mobile.

\* Only download apps from official sources.

\* Never use public WiFi without a VPN.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ THREAT MITIGATION ]

Who or what are you protecting yourself from?

This is the first question to ask when considering your security posture. This guide is for general everyday users. If you have serious data privacy concerns, like an activist or journalist, you should consider a more thorough COMSEC/OPSEC plan. Check out [the gruq](https://twitter.com/thegrugq) and follow some of his guides as a start.

The everyday user basically needs to protect themselves from virus infection, malware, and web-based exploit kits. And maybe the odd stolen phone or laptop.

If you want to protect yourself from a nation state, you're pretty much screwed. Try [this](https://www.qubes-os.org/) or [this](https://tails.boum.org/) I guess.


== Online Behaviour ==

This is the first and best line of defence: don't click on things you don't understand.

\* Don't open suspicious emails or attachments, especially strange invoices or fines.  

\* Don't click on pop-up ads.    

\* Don't download software or stream from untrustworthy sites.    

\* Don't use pirated software.    

\* Don't click on links in strange social media messages, even from friends.  

== Social Media ==

\* Go through all of your social media accounts privacy and security settings and choose a configuration you're comfortable with.  

\* Turn off geo-location.  

\* Consider how some people might put together your post history to paint a picture of where you live and when you are away from home for example.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ UPDATES ]

Update all the things!
 
Updates and patches for Operating Systems and software are released often, and for good reason. Vulnerabilities are discovered all the time, and companies work hard to patch their products to overcomes these as quickly as possible. 

Update your OS, software, and Internet browser as often as possible. Make it automatic if you can. 

Don't let the notifications just sit there, updates are very important.

More info and specific guides [here](https://www.staysmartonline.gov.au/computers/update-software).

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ PASSWORDS/MFA ]

This is a big one. Most people have very poor passwords: too short, easy to guess, easy to bruteforce, and then these passwords are often reused.

If you use your email and one-size-fits-all password on say, a small niche forum, as well as your online banking, it is likely that the substantially weaker security mechanisms on the forum will negate the great security of your bank. 

Password breaches happen all the [time](https://www.troyhunt.com/100-data-breaches-later-have-i-been-pwned-gets-its-first-self-submission/) and reused passwords are an easy way in for a hacker. Check [here](https://haveibeenpwned.com/) to see if any of your accounts associated with your email have been compromised in the past.

== Good password practice == 

\* Use long, complex passphrases with mixed case letters, numbers and symbols.  
\* Do not reuse passwords - [here's](https://security.illinois.edu/content/why-you-should-use-different-passwords) why.

Relevant XKCD on passphrase strength:

![](https://imgs.xkcd.com/comics/password_strength.png){:height="488px" width="565px"}

But seriously:

["The only secure password is the one you can’t remember."](https://www.troyhunt.com/only-secure-password-is-one-you-cant/)

== Use a password manager! ==

You only need to remember one passphrase (make it a strong one!) and it automatically generates large, random passwords, and stores them easily in a secure file. You can then just drag and drop or copy and paste these into your browser. 

I cannot stress this enough. It makes a huge difference to your security and genuinely makes life a whole lot easier.

Personally I use [KeePass](http://keepass.info/), but I hear good things about [LastPass](https://www.lastpass.com/) and [1Password](https://1password.com/).

They can be used and synced across computers, phones and tablets. Please use one.

== Multi-Factor Authentication ==

Where possible enable and use [multi-factor authentication (MFA)](https://en.wikipedia.org/wiki/Multi-factor_authentication). This helps prove your identity through two or more methods. So in addition to your password and username, you receive a single-use code that you are required to input. Usually it is something you know (passphrase) and something you have (mobile phone, code generator).

You should definitely use MFA for important applications such as email and banking.

[Here](https://support.google.com/accounts/answer/185839?hl=en) is the Gmail guide for enabling 2-Step Verification.

SMS two-factor codes are better than nothing, but still have some [problems](https://www.smh.com.au/technology/consumer-security/malware-hijacks-big-four-australian-banks-apps-steals-twofactor-sms-codes-20160309-gnf528.html). If possible use security tokens such as [YubiKey](https://www.yubico.com/start/).


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ INTERNET BROWSERS ]

\* Keep them updated.

\* [Don't let your browser store/save passwords](https://www.pandasecurity.com/mediacenter/security/browser-saves-password-secure/) (use a password manager instead).


\* Install uBlock Origin ([Chrome](https://chrome.google.com/webstore/detail/ublock-origin/cjpalhdlnbpafiamejdnhcphjbkeiagm?hl=en), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/)). This wide-spectrum blocker stops [malvertising](https://en.wikipedia.org/wiki/Malvertising) among other things.

\* Install HTTPS Everywhere ([Chrome](https://chrome.google.com/webstore/detail/https-everywhere/gcbommkclmclpchllfjekcdonpmejbdp?hl=en), [Firefox](https://www.eff.org/https-everywhere)). This forces websites to use HTTPS and makes browsing more secure.

\* For less experienced users consider BitDefender TrafficLight ([Chrome](https://chrome.google.com/webstore/detail/trafficlight/cfnpidifppmenkapgihekkeednfoenal?hl=en), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/trafficlight/)).

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ ANTIVIRUS/ANTI-MALWARE ]

Antivirus can be a contentious subject. A lot of them don't offer much, they install a lot of bloatware and constantly hassle the user with upselling pop-ups.

 Antivirus software alone is not enough to protect yourself online.

== Antivirus ==

Windows Defender, the stock Windows antivirus, is fine.

== Anti-Malware ==

[MalwareBytes](https://www.malwarebytes.com/premium/) is the best anti-malware tool in my opinion. Install it and run it regularly. If you are prone to installing malware then consider buying the premium version for real-time protection.


\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ OS CONFIGURATION ]

== Clean Install ==

Ideally you should do a clean install of Windows, this gets rid of all the bloatware that some manufacturers include in the OS. 

[Here](https://www.howtogeek.com/224342/how-to-clean-install-windows-10/) is a guide to clean install Windows 10.  

If you're going to do a clean install make sure to: 

\* Update the BIOS/UEFI - look for instructions for your particular hardware via the manufacturer's website.

\* Enable TPM and SecureBoot options in the BIOS.

== Security Settings ==

\* Go through the Security and Privacy settings and choose a configuration you're comfortable with.

\* Ensure you have a strong login password, especially for laptops. 

\* Ensure antivirus and Firewall are turned on as mentioned above.  

\* Set User Account Control (UAC) to full:  
`Control Panel > User Accounts > Change User Account Control settings`   
Slide panel to Always notify

![](/pictures/UAC_Always_notify_me_when.jpg){:height="543px" width="623px"}

== Disk Encryption ==

\* For Windows 10 Home: 

`Start > Settings > System > About`  
Look for Device Encryption if available. If its not there your computer may not support encryption.

\* For Windows 10 Pro: 

`Start > Control Panel > BitLocker Drive Encryption > Turn on BitLocker`   
If a warning about TPM comes up try [this](http://www.howtogeek.com/howto/6229/how-to-use-bitlocker-on-drives-without-tpm/).

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ WIFI/ROUTER ]

\* Log into the management page using the details usually found on the bottom of the router. [Here](http://www.routerpasswords.com/) is a site to help you if you need it.

\* Upgrade the router's firmware.

\* Change the default admin password. This is important as the default passwords are common and easily searchable.

\* Change the SSID to something other than the default. The default SSID can give away the manufacturer details of the router which can lead to someone finding the default login password.

\* Ensure the WiFi encryption type is set to: `WPA2 Personal + AES` - this is very important!

\* If your WiFi is encrypted with [WEP](https://en.wikipedia.org/wiki/Wired_Equivalent_Privacy) buy a new one or ask your ISP to send you another. It is an encryption technology that is no longer considered secure. 

\* Turn off: Remote Management and Respond to Ping.

\* Set a strong password for the WiFi.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

\-\-[ MOBILE PHONES ]

Securing your phone is very important. It is the device most likely to be lost or stolen, and probably has access to your email and lots of other personal information.

To keep it as secure as possible:

\* Update the OS as soon as it is available.

\* Use a lock screen secured with a PIN or password.

\* Only install apps from the official App Store or Google Play Store.

\* Enable [Find My iPhone](https://www.apple.com/au/icloud/find-my-iphone.html) or an app like [Cerberus](https://www.cerberusapp.com/home/en).

\* Enable sync backups regularly.

\* Never use public Wifi, or if you must use a [VPN](https://en.wikipedia.org/wiki/Virtual_private_network). [Here's](https://www.howtogeek.com/178696/why-using-a-public-wi-fi-network-can-be-dangerous-even-when-accessing-encrypted-websites/) why.

\* Be extra careful when jailbreaking or rooting.

\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-

