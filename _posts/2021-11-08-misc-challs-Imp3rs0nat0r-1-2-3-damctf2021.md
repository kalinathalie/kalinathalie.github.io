---
title: DamCTF 2021 - Imp3rs0nat0r 1, 2 & 3
layout: post 
date: '2021-11-08 09:00:00'
description: solving a OSINT vuln challs
image: 'https://i.imgur.com/A8uZ5A6.jpg'
tags:
- misc
- osint
- forensic
- ctf
- infosec
- writeup
author: k4l1
---

Hi everyone! C:

This DamCTF was pretty awesome, we played with a half of the team and two guests and got #8/827 place.

On this CTF I focused on MISC challs and solved 4. Here I'll explain a sequence of 3 challs, and the other chall will be posted soon.

**All the challs bellow was made by perchik (perchik#7272)**


## MISC Challenges - Imp3rs0nat0r 1, 2 & 3

### Imp3rs0nAt0r-1

#### Description

104 solves / 442 points

>Some dumb college student thought he was leet enough to try and hack our university using a school computer. Thankfully we were able to stop the attack and we confiscated the equipment for forensic analysis.

>Can you help us figure out what their next moves are?

>Flag is in standard flag format.

[Download Here: UsrClass.dat](https://cdn.discordapp.com/attachments/906373269270188082/906374837176516648/UsrClass.dat)


#### Solution

This UsrClass.dat is a MS Windows registry file contains information regarding applications which have been executed. We can map all the files with the software [ShellBags Explorer](https://www.sans.org/tools/shellbags-explorer/).

Like that:

![](https://cdn.discordapp.com/attachments/906373269270188082/906434596814610432/unknown.png)

There is here some suspicious directory names, so if the path names is the only thing that we have, let's search this on Google...

![](https://i.imgur.com/cg0U5Fo.png)

![](https://i.imgur.com/BQwEfZl.png)


Yeah, we got that!
So, after some checks on the Github Repositories, we found this Discord Bot Token on commits:

![](https://i.imgur.com/YPPBYaT.png)

At first moment, We can up this bot to read all messages, members and channels of all servers that this bot is related.

```py
import discord, base64
from discord.ext import commands

bot = commands.Bot(command_prefix='$', case_insensitive=True)

@bot.event
async def on_ready():
    print('We have logged in as {0.user}'.format(bot))
    for guild in bot.guilds:
        print("Servers:")
        print(guild.name)
        for member in guild.members:
            print("Members:")
            print(member)
        for channel in guild.text_channels:
            print("Channels:")
            print(channel.name)
            for current_message in await channel.history(limit=500).flatten():
                print(current_message.author)
                print(current_message.content)

token = base64.b64decode(b'T0RReU1qUTNPRFl6TWpVek56STVNamt3LllKeWljZy43S0c5MzRWRWxtM1J0Wm45YlVhQ0xTdnJPeUk=').decode()
bot.run(token)
```

![](https://i.imgur.com/K1lgTBi.png)

This worked!, a lot of messages like this SUP3R H4CK3R W3BCH4T above, bot nothing of flag :(

So... let's create a invite for this Discord server...

```py
import discord, base64
from discord.ext import commands
.
.
.
        for channel in guild.text_channels:
            link = await channel.create_invite(max_age = 300)
            print(link)
.
.
.
```

![](https://i.imgur.com/Y0dTM17.png)

BOOM! Tha flag did'nt appear before because this text was embbeded, and the bot was only showing normal messages.

`dam{Ep1c_Inf1ltr4t0r_H4ck1ng!!!!!!1!}`

### Imp3rs0nAt0r-2

#### Description

57 solves / 471 points

>Great work figuring out their next moves! We have gone ahead and preemptively secured the aforementioned services. Now let’s start to try and put this whole thing to rest.

>Can you find the full name of the person who was behind the operation?

>Submit the flag as dam{firstname_lastname}.


#### Solution

Ok, let's see the `nc-lnvp` profile on Discord:

![](https://i.imgur.com/5ly13iS.png)

Of course that this is a hint, let's search about the E-mail on Github commits.

![](https://i.imgur.com/qE3JqaB.png)

Easy, `nn9262186@gmail.com`.

After some HOURS OSINTing this E-mail on Google, Github, DuckDuckGo and whatever E-mail/Gmail recon tool, we found this website: [tools.epieos](https://tools.epieos.com/email.php).
Basically this site is a compilation of Google Account recon tool, [Holehe](https://github.com/megadose/holehe), and LinkedIn linked E-mails.

![](https://i.imgur.com/kCSkkOV.png)

This is one of lot of solves about OSINT/Recon on Google E-mails. Google Accounts have some public information like GoogleID, E-mail, Contributions on Google Maps, Photos and Calendar, on this chall there is another way via Maps, as we can see on this comment:

![](https://i.imgur.com/dZ5SM0Z.png)

So...

Flag: `dam{pax_vallejo}`

### Imp3rs0nAt0r-3

#### Description

37 solves / 482 points

>Amazing work figuring out who this "hacker" really is! Unfortunately, we have no leads as to what their current whereabouts could possibly be.

>Can you try and deduce the address of where they are staying? Maybe you can find some vacation photos to help you out.

>Submit the flag as dam{full_name_of_hotel_as_it_appears_on_google}.


#### Solution

`Pax Vallejo` is on Hotel? ok!

This last chall made me crazy... Why? The only thing location-related that we found about `Pax Vallejo` was the comment on `Oregon State University`, so after lot of hours searching about Hotels on Oregon State, Corvallis city and brute-forcing hundred of Hotels on this location on format flag, we concluded: this F****** script kiddie isn't there!
Ok, let's back to OSINT thing...

I have to say that after search on each social media, `Twitter`, unfortunatelly, was the last thing that appear on my head, why? My brain: "This is the last chall, `Twitter` is too obvious". Hahahahaha

![](https://i.imgur.com/kbZNhe8.png)

Yeah, finally!

There is 5 pictures on this account(a museum, a river, a trains station, and 2 images about the current location showing trains and rails), for not image-spamming I'll just post the link here and you can check: https://twitter.com/cheesep27684825 .

After checking that there is no trains around Oregon State University hahahaha... my girlfriend found that the images was taken in London because this tower below:

![](https://i.imgur.com/UfWx8kL.png)


Now starts the real FBI job... (This isn't funny cuz I never ever got on a train)

![](https://i.imgur.com/5heRCfg.png)

On first image we can see `Circle line` on train, I researched about the London train map for this line... nothing else lot of results and hours.

![](https://i.imgur.com/vVyhHg7.png)

Lot of numbers em data that I don't even know how to look.

OK, after some hours, on the first image that show the `Pax Vallejo` Point of view, we can see this tower followed by a building-with-circular-thing on top.

![](https://i.imgur.com/bGZuidn.png)


Definitively, a church. So, let's search some high chuchrs in London...

![](https://i.imgur.com/vpS3J3W.jpg)

No joke, searching `London Church` on Google Images we can see this image above. Seems similar.

This church is called `St Mary Abbots Parish`. So with We want to see that better, let's use [Google Earth](https://www.google.com.br/intl/pt-BR/earth/) to check this church followed by a building-with-circular-thing.

![](https://i.imgur.com/gNxadMA.jpg)

Yeah, finally we got that! (Google Earth is insane).

Following the image to the `Pax Vallejo` building...

![](https://i.imgur.com/1t92Fbh.jpg)

![](https://i.imgur.com/cN95g6u.jpg)

The name is `Copthorne Tara Hotel London Kensington`.
So the flag is: `dam{copthorne_tara_hotel_london_kensington}`

## References

* [Solving a OSINT vuln challs](https://kalinathalie.github.io/misc-challs-Imp3rs0nat0r-1-2-3-damctf2021.md/)
