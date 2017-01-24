---
layout: post
comments: true
title: Sending SMS on macOS Programmatically
category: note
---

I noticed that a particular cell phone provider in Canada was putting on a charity campaign for mental health that included a 5¢ donation for every text message sent.

<p><img src="{{ site.file }}/cell-phone-charity.png" alt="cell phone charity"></p>

5c per text message can add up to a lot. I wanted to try to find out how someone might mass send SMS messages if they _really_ wanted to contribute A LOT to mental health on this particular day, since most people have free SMS messaging Canada wide.

A paltry 100,000 SMS messages would add up to $5,000 for mental health charities.

There are a few restrictions here:
- It can't be iMessage. It has to be SMS messages.
- You need to have a number tied to the existing cellular provider (we can't use Twilio with a new number)

Since I have both a Mac and iPhone, the easiest way for me to programmatically mass send SMS messages is with AppleScript.

You can set up a simple AppleScript like so:

```
repeat 10 times
  tell application "Messages"
    send "text for mental health" to buddy "4166667777" of service "SMS"
  end tell
end repeat
```

The above will send an SMS 10 times to the number 4166667777, but only if the other person doesn't have iMesssage (if they do messages just defaults to iMessage always).

This was just an experiment to see what was possible. Use at your own risk :)
