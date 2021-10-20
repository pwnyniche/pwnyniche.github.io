---
layout: post
title: UIUCTF Writeups
tags: [osint]
comments: true
---

### I am a new CTF player who come from VietNam (yeah here I am, sorry for my English) and mainly work on pwn & osint. 
### I join the UIUCTF solo (yes, by myself) under the nickname "niche" and end up at 177th (╥﹏╥). UIUCTF 2021 was a well-rounded and fun CTF btw.
### I managed to solve all the osint (except the OSINT The Creator 2 since I didn't check the new challenge) and here're my writeups.
This includes:
- [Osint The Creator](#osinthecreator)
- [Osint The Creator 2](#osintthecreator2)
- [Chaplin's PR Nightmare - 1](#osintchaplin1)
- [Chaplin's PR Nightmare - 2](#osintchaplin2)
- [Chaplin's PR Nightmare - 3](#osintchaplin3)
- [Chaplin's PR Nightmare - 4](#osintchaplin4)
- [Chaplin's PR Nightmare - 5](#osintchaplin5)
- [Chaplin's PR Nightmare - 6](#osintchaplin6)
- [Chaplin's PR Nightmare - 7](#osintchaplin7)
- [Chaplin's PR Nightmare - 8](#osintchaplin8)


## <a name="osinthecreator"></a> Osint The Creator
---
_author: Thomas_

_There is a flag on a few of the organizer's profiles. Find it!_

_**Hint:** It's on Discord :)_

---


Okay this is a nice one, I solved all the Chaplin's PR Nightmare before solving this (it hurts my eyes scrolling down the Discord members' list since the description says "There is a flag on **a few of the organizer's profiles**"). Yeah, I think the flag must be obvious and there're some people who has the flag but didnt show up (I was wrong).

After wasting time searching all the profile I could possibly find on Discord, I come back to Thomas (since he is the **author**). And yes, he hide the flag in his bio (using some markdown technique I guess):

![Thomas's profile on discord](https://i.ibb.co/cxHMp4f/pic1.png "Hidden flag")

> Flag: **uiuctf{@b0uT_m3_suppOrT5_maRkD0wN}**

## <a name="osintthecreator2"></a> Osint The Creator 2
---

_author: Thomas_

_I released another flag somewhere on one of my Twitter accounts. Can you find it?!_

---

This is a funny one since I did a lot of research on Thomas more than necessary because of being unable to find the flag (:D). From the first challenge, I found his info in the [sigpwny's website](https://sigpwny.com/uiuctf/) and [his website](https://thomasquig.dev/).

Scrolling down his website (which is a nice one) I found his Twitter's account (@Thomas_Quig and another one @0xQuig in his twitter bio). Now all we need to do is to find something related to the flag, so I used the Twitter's advanced search to find all the tweet that have the keyword "uiuctf{" (the character "{" is ignored o(TヘTo)) from @Thomas_Quig or @0xQuig. Luckily I found the flag:

![Search Results](https://i.ibb.co/pfSKxDq/Screen-Shot-2021-08-02-at-21-37-47.png "The flag")

> Flag: **uiuctf{it5_@_sEcr3t_t0ol_thAT_w!ll_he7p_u$_l@ter}**

## <a name="osintchaplin1"></a> Chaplin's PR Nightmare - 1
---
_author: Thomas_

_Charlie Chaplin has gotten into software development, coding, and the like... He made a company, but it recently came under fire for a PR disaster. He got all over the internet before he realized the company's mistake, and is now scrambling to clean up his mess, but it may be too late!! Find his Twitter Account and investigate! NOTE THAT THESE CHALLENGES DO NOT HAVE DO BE DONE IN ORDER!_

_The inner content of this flag begins with "pe"_

_**Hint 1:** If you are struggling to find his Twitter account, check out the #chal-updates channel in our Discord_

_**Hint 2:** The bot says "I have a friend named charlie chaplin who does coding or development now! He is on twitter and I forget the at is, its some combo of those words i think though."_

---

Charlie Chaplin, coding & development are the main keywords, let's find him on twitter!

![Search Results](https://i.ibb.co/NgvnWTn/Screen-Shot-2021-08-02-at-21-49-23.png)

It's not so hard to find Charlie on twitter, guess he's the only developer who wants to bring you the best comedic code ╮(￣ω￣;)╭.

The trick from Osint The Creator 2 wouldn't find anything so I have to check all of his tweets. Finally I found the one with suspicious comment.

![Suspicious Tweet](https://i.ibb.co/TbdnCHQ/Screen-Shot-2021-08-02-at-21-54-31.png)

Checking his lists (yes Twitter has that function ┐(￣ヘ￣)┌	 ) I found exactly what I was looking for (the flag).

> Flag: **uiuctf{pe@k_c0medy!}**

## <a name="osintchaplin2"></a> Chaplin's PR Nightmare - 2
---
_author: Thomas_

_Charlie made an advertisement to promote his company, he is using the modern media platform YouTube to present it! Can you find it?_

_The inner content of this flag begins with "ch"_

---

Charlie Chaplin is nice enough to leave us a Youtube link in his twitter (which is also can be found by searching on Youtube with the right keyword). This is a 'patient' challenge to me because the flag appears at the end of the video, not anywhere else in the description (I really did enjoy the vid (ﾉ´ з `)ノ, didnt even skip a sec). 

![The Flag](https://i.ibb.co/K95rhvs/Screen-Shot-2021-08-02-at-22-03-38.png)

> Flag: **uiuctf{ch@plin_oN_th3_tV!!}**

## <a name="osintchaplin3"></a> Chaplin's PR Nightmare - 3
---
_author: Thomas_

_Charlie even has a website!!! Find it and look around!_

_The inner content of this flag begins with "ch"_

---

After doing some of the challenges I realized that Charlie is a mischievous guy who loves to leave Easter Eggs in everything he does. That's why I check his Youtube channel very carefully, and he did leave his website link in the About section on his Youtube channel (of course Mr.Google can help with the website, but I'd like to follow the way he paved for us (◕‿◕)).

Checking his front page, I can easily find the flag which is right in some of his pictures (which he says for no reason :>).

![The Flag](https://i.ibb.co/84Ssw4R/Screen-Shot-2021-08-02-at-22-11-46.png)

> Flag: **uiuctf{ch@pl1n_i5_eL337}**

## <a name="osintchaplin4"></a> Chaplin's PR Nightmare - 4
---
_author: Thomas_

_Charlie left another flag on his company's website. Maybe you have to reach out to him???_

_The inner content of this flag begins with "w3"_

---

This challenge seems to have the best hint when it says **"you have to reach out to him"**. If you want to reach out to someone, you need to find their contacts (yes, the website did have the contact page). Checking all the contents of Chaplin's contact you can easily find a form to contact him (the obvious way you can possibly find the flag). Filling out the form  (or not) and submit it to get the flag.

![The Flag](https://i.ibb.co/JqZRBsL/Screen-Shot-2021-08-03-at-10-06-16.png)

> Flag: **uiuctf{w3_d0_nOt_v@lu3_yoUR_1nPuT}**

## <a name="osintchaplin5"></a> Chaplin's PR Nightmare - 5
---
_author: Thomas_

_Charlie also has an imgur account that may hold the secret to his PR nightmare... Can you find it?_

_The inner content of this flag begins with "tH"_

_**Hint:** Do images grow on trees?_

---

This time the hint doesn't clearly point out anything (to me (*μ_μ)), but it says there is something related to images. So what I did was go through all the images in Chaplin's website to find a reference or a link (or something like that). Luckily there is a clickable link in the **About us** page attached to this image:

![The imgur link](https://i.ibb.co/nsQyMMg/Screen-Shot-2021-08-03-at-10-14-46.png)

The link attached to the image gets us to its source on Imgur, and the owner of this Image is an account named ChaplinDevelopment (we all know who). Inspecting his account we can find other images that he also published. 

![Other images](https://i.ibb.co/Tqh1gmk/Screen-Shot-2021-08-03-at-10-22-40.png)

Go through all images (I mean 3) we can found the flag was in a comment of an image. We can also go through the comments to find the flag.

![The Flag](https://i.ibb.co/b18YFGL/Screen-Shot-2021-08-03-at-10-28-20.png")

> Flag: **uiuctf{tH3_pR_p0Lic3_h@vE_cAugHt_Up?!}**

## <a name="osintchaplin7"></a> Chaplin's PR Nightmare - 7
---
_author: Thomas_

_Chaplin left some code up on GitHub which may be the actual PR nightmare I hope not though... Leaked code is bad. Find it!!!_

_The inner content of this flag begins with "th"_

_**Hint:** OUtlNDQ4ME1pY1U=_

---

Let me talk about the hint first (I love Julia Michaels), this is a base64 encoded text, which can be decoded into _"9Ke4480MicU"_. At first I didn't know what it means, so I googled it and found out this is actually the video code of Youtube's music video for Julia Michaels - **Issues**. Linking this hint with Github I figured out that Github has an Issues page, which is used for reporting bugs and questions about the project. So all we need to do is find Charlie Chaplin's Github and check his project's Issues section.

His Github account is not so hard to find, since he is one of the four Charlie Chaplin who really have something to do with code:

![Chaplin's Github account](https://i.ibb.co/YDbdMrt/Screen-Shot-2021-08-03-at-10-37-07.png)

The obvious account we are looking for is the first account (joined 15 days ago, Comedy and Development Hell, ...). The account has only 2 repositories, and only one (charliechaplindev/C3D-Official) really has something to do with code. So I jumped in this repository and inspect the Issues.

![C3D repository's Issues](https://i.ibb.co/vz1pvgp/Control-V-8.png)

The default Issues page only shows currently open issues, so after inspect the 2 open issues, I noticed that there is still an issue that has been closed. So I use the filters to show all issue (just delete the "is:open" code). I found the closed one is the one that hold the flag.

![All open and closed Issues](https://i.ibb.co/wLpJJRM/Screen-Shot-2021-08-03-at-10-53-29.png)

![The Flag](https://i.ibb.co/xXwfKp9/Screen-Shot-2021-08-03-at-10-55-50.png)

> Flag: **uiuctf{th3_TrUe_pR_N1gHtm@r3}**

## <a name="osintchaplin6"></a> Chaplin's PR Nightmare - 6
---
_author: Thomas_

_Wow Charlie even set up a linkedin account, but not well it is kind of a mess. Is the PR nightmare here??_

_The inner content of this flag begins with "pr"_

---

This challenge is a little more tricky than the others, since the linkedin account isn't easy to find. I managed to solve this thank to the 7th challenge, using his Github account's name to find his linkedin account (This is why I put the 7th challenge before this).

Even though I found another hint in his website (the 3rd & 4th challenge) that says _"You can also find us on Linkedin, but you will have to search for us I forget what our name is, either like "Charlie Chaplin Coding or Development" or "C3D" something like that."_, I still cannot find his linkedin account (︶︹︺). So I decided to do the 7th challenge instead, but this actually lead me to another clue that his account name could be "Charlie C" instead of "Charlie Chaplin". So I do a couple of combinations with his name and "development" or "coding" (and filtered out people) and found his actual account on linkedin.

![Search Result](https://i.ibb.co/QXDDqGV/Screen-Shot-2021-08-03-at-11-05-27.png)

After inspecting the account, I still couldn't find the flag. However, a small details in his experience drew my attention.

![The Account's Experience](https://i.ibb.co/74Yy1Hy/Control-V-9.png)

This says there was **a(n) event called "Top Hat Development Night"** and we can still searched it up. So I follow the saying and search for it (filter out only the events), and the flag was there.

![The Event](https://i.ibb.co/9VTY4GP/Screen-Shot-2021-08-03-at-11-15-00.png)

> Flag: **uiuctf{pr0f3s5iOn@l_bUs1n3sS_envIroNm3n7}**

## <a name="osintchaplin8"></a> Chaplin's PR Nightmare - 8
---
_author: Thomas_

_Straightup doxx Charlie by finding the email he set all these accounts up with, and investigate it._

_The inner content of this flag begins with "b0"_

_**Hint:** This challenge was inspired by something previous._

---

The hint points out that we need to go through previous clues to find his email. If you are a cautious person, you may notice that I didn't go through all (which I did in other accounts/pages) of Charlie Chaplin's Github account. So if you go through all the commits of the (charliechaplindev/C3D-Official) repository, you would find a security.txt file that actually contain Mr.Charlie Chaplin's email. It is **_"charliechaplin.dev@gmail.com"**_

![security.txt](https://i.ibb.co/QfjD1fW/Screen-Shot-2021-08-03-at-11-30-58.png)

So the point of this challenge is to osint someone with their **Gmail** address. I wouldn't have solved this challenge if I haven't read [this article](https://medium.com/week-in-osint/getting-a-grasp-on-googleids-77a8ab707e43) before. The article shows us some ways to get the GoogleID from Gmail address, and use that ID to get more information on other Google's platforms.

I will report what I did here, since I didn't use the method in the article (which I actually did, but found no result). What we need to do is:

- First: Get the GoogleID from the gmail.
- Second: Use the GoogleID on other platforms of Google to get information.

To get the GoogleID, I use Google Mail to compose an email to **charliechaplin.dev@gmail.com**. Before doing this, you need to open the Developer Tools (available on Chrome, Firefox, Safari,...) first, and switch to Network to inspect packets. We'll need to inspect the packet comes from _contacts.google.com_ (which contains the GoogleID) so I filtered it out for easier inspection.

![Inspect Network Packets on Chrome](https://i.ibb.co/3fHXfqt/Screen-Shot-2021-08-03-at-11-41-43.png)

Then I compose a new email to **charliechaplin.dev@gmail.com** and entered it. After pressing "enter" and click into the email, there'll be a packet begining with the name "batchexecute" that contains the GoogleID of the Gmail address, so we need to investigate that packet and get the GoogleID we need (notice that the GoogleID starts with "10" or "11").

![Find GoogleID in the packet](https://i.ibb.co/ZmhmCMm/Control-V-10.png)

There're some platforms that we can abuse this GoogleID to get information like Photo Albums, Google API, Youtube Account, Google Drive,... (which is included in the article I mentioned before). In this challenge, the platform we need is Google Maps. We can inspect Google User's contribution on Google Maps using the ID simply by requesting this URL:

```
https://www.google.com/maps/contrib/{userID}
```

I found the flag in the image that he took at Montrose Beach:

![The Flag](https://i.ibb.co/F8jZ3Xh/Screen-Shot-2021-08-03-at-11-59-44.png)

> Flag: **uiuctf{b0rk_b0rk_1_lOv3_mY_d0g<3}**

# Conclusion

All the OSINT challenges are kind of easier than the other categories, but it is fun to do and requires a lot of patience and caution from the hacker. You need to investigate everything clearly and mustn't miss a single detail because every small detail can lead to surprisingly big informations.

<center>~ THE END ~ </center>

