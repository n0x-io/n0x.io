+++ 
draft = false
date = 2023-03-14T23:54:16+01:00
title = "Solving 0xf.at - Part 1"
description = "A little collection of the approaches and stuff I used to solve 0xf.at challenges"
slug = ""
authors = ["_N0x"]
tags = ["hackit", "challenges", "riddles", "0xf.at"]
categories = []
externalLink = ""
series = []
+++

# Solving 0xf.at's funny little challenges - Part 1 Level 1 to 10

**WARNING!**
**THE FOLLOWING PAGE CONTAINS SPOILERS FOR THE PASSWORD-RIDDLE SITE 0XF.AT!**
_Only continue reading if you are okay with seeing spoilers or need some help with the riddles ;)_

```
  ___        __       _
 / _ \__  __/ _| __ _| |_
| | | \ \/ / |_ / _` | __|
| |_| |>  <|  _| (_| | |_
 \___//_/\_\_|(_)__,_|\__|
```

I guess it's time for my (almost) yearly blog entry so I remember how to use hugo...
So why not do something fun and solve some hacking challenges!!

[0xf.at](https://0xf.at) is a small website that offers a few password related riddles or "Hackits"
where the user is tasked to either recover passwords, bypass them or manipulate things in other ways to gain access.
There are a total of 38 levels ranging from very easy with nearly 20k solves to some super hard complex challenges that have less than five solves in comparison.
Next to a little name they also have a type that hints at how the riddle can be solved, be it something JavaScript relates, some logic and programming or maybe reversing some PHP code to gain access.

All in all some very fun things to tinker with and every now and then I stumble about this website and thought it might be a good idea to write down my though process and see how far I can make it.
So let's take a peek!

Oh and just as a side note - most of these puzzles generate a random solution so just copy and pasting the solutions from here won't always be an option :)

## Level 1 - Easy beginnings

This challenge has the Type "JavaScript" which already is a big giveaway as to what might be going on here.
We are presented with a simple screen where we just need to enter the password to proceed.

{{< figure src="images/lvl1_1.png" alt="Level 1" >}}

But what could the password be? Since JavaScript is client site we take a peek at what is happens when we click the "OK" Button.

```html
<h1>Level 1</h1>
<div>Easy beginnings</div>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  function checkPW() {
    var el = document.getElementById("pw");
    if (el.value == "tooeasy") document.location.href = "?pw=" + el.value;
    else alert("Wrong password!");
  }
</script>
```

Clicking the password calls the `checkPW()` function.
And in there we can see that the value entered will be checked against the phrase "_tooeasy_".

{{< figure src="images/lvl1_2.png" alt="Level 1 cleared" >}}

## Level 2 - JavaScript: Do you speak it?

Another JavaScript challenge.
The screen looks just like with the first level so straight for the code it is.

```html
<h1>Level 2</h1>
<div>Not that hard either..</div>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  function checkPW() {
    var pw = "%30%36%31%33%65";
    var el = document.getElementById("pw");
    if (el.value == unescape(pw)) window.location.href = "?pw=" + el.value;
    else alert("Wrong password");
  }
</script>
```

The value for the password seems to be a strange string "%30%36%31%33%65".
Which looks to be URL encoded.
Using the Browser console we can use the JavaScript command `decodeURIComponent("%30%36%31%33%65")` to see the value of those characters is "_0613e_".

{{< figure src="images/lvl2_1.png" alt="Level 2 cleared" >}}

## Level 3 - Without sourcecode

No sourcecode? But it's still a JavaScript challenge so we can dig into the website code again to see where things lead.
The joke behind this level is that there are a few hundred empty lines in the source code - buuut the FireFox or in my case LibreWolf Debugconsole doesn't show those

```html
<h1>Level 3</h1>
<!--
Source code disabled

  [MANY MANY EMPTY LINES]

Did I fool you?
-->
<div>How about: No sourcecode?</div>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  function checkPW() {
    var el = document.getElementById("pw");
    if (
      el.value ==
      unescape("r%20i%20g%20h%20t%20") + "" + "p" + "w" + "" + "6304b3377"
    )
      window.location.href = "?pw=" + el.value;
    else alert("Wrong password");
  }
</script>
```

The value of the password this time seems to be the result of the command `unescape("r%20i%20g%20h%20t%20")+""+"p"+"w"+""+"6304b3377"`.
Pasting that into the development console of the browser reveals the passphrase for this level: "_r i g h t pw6304b3377_"

## Level 4 - Length matters

Straight to the source code!

```html
<h1>Level 4</h1>
<div>What does .length mean?</div>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  function checkPW() {
    var el = document.getElementById("pw");
    var pwinfo =
      "2ab135a4dd3ffa04fd5b53ee5ed1cbf    3122c05 ecc31321321b 353a51a12e";
    if (el.value == pwinfo.length) window.location.href = "?pw=" + el.value;
    else alert("Wrong password");
  }
</script>
```

This is just as easy as the previous ones and the name of the level already gives pretty much everything away.
We just need to take a look at how long the value of `pwinfo` is.
We can do that with the console again.
JavaScript is a silly language so it allows us to run something like `"2ab135a4dd3ffa04fd5b53ee5ed1cbf    3122c05 ecc31321321b 353a51a12e".length` in the console.
Which reveals the length of 66.
Just entering "_66_" as the password solves the level - I don't know what's worse, having the password in cleartext or using the length as a value which would probably be cracked even faster ;)

Be aware that the value of `pwinfo` seems to be regenerated with each site reload so the actual value of the result might change!

## Level 5 - ASCII fun

ASCII is indeed fun and the author of the side also seems to be a fan of using something like `figlet` - [for example this website](https://www.askapache.com/online-tools/figlet-ascii/) - to generate silly ASCII images from text.
But back to the challenge.

```html
<h1>Level 5</h1>
<div>Have you heard of ASCII?</div>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  function checkPW() {
    var el = document.getElementById("pw");
    if (el.value == atoi("o") + 66) window.location.href = "?pw=" + el.value;
    else alert("Falsches Passwort");
  }

  // converts a character to its ASCII number
  function atoi(a) {
    return a.charCodeAt();
  }
</script>
```

`atoi()` is a usually function used in C and similar languages to convert a string or single character to it's corresponding ASCII value.
But it just seems to be a wrapper for the JavaScript function `charCodeAt()`.
Once more the debugging console comes to the rescue and we see that `"o".charCodeAt()` equals 111.
Now we only add 66 and we have our password of "_177_".
Nice!

## Level 6 - Color codes

Level 6 is yet again a JavaScript challenge.
It looks a little different than the previous once tho.

{{< figure src="images/lvl6_1.png" alt="Level 6 pretty colors" >}}

```html
<h1>Level 6</h1>
<div>
  <span id="nice" style="color:#cc3333">Nice</span>
  <span id="though" style="color:#99ff33">colors</span>
  <span id="colors" style="color:#33ffff">though</span>
</div>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  function checkPW() {
    var el = document.getElementById("pw");
    if (el.value == rgb2hex(document.getElementById("though").style.color))
      window.location.href = "?pw=" + escape(el.value);
    else window.location.href = "?pw=" + escape(el.value);
  }

  function rgb2hex(rgb) {
    rgb = rgb.match(/^rgb\((\d+),\s*(\d+),\s*(\d+)\)$/);
    function hex(x) {
      return ("0" + parseInt(x).toString(16)).slice(-2);
    }
    return "#" + hex(rgb[1]) + hex(rgb[2]) + hex(rgb[3]);
  }
</script>
```

Looking at the `if` statement is seems the color value of the element with the id `thought`.
Checking that with the console reveals the value `rgb(153, 255, 51)` which gets passed into the `rgb2hex()` function.
This only seems to turn the input format into the typical HTML color code.
In this case the method returns "_#99ff33_" which once again solves the puzzle!

An even quicker way than to look at the `rgb2hex` function would be to look at the value of the `thought` element in the second line of code which reveals the value directly.
Solved again!

## Level 7 - Jerry's screw-up

First level that isn't of type JavaScript.
Apparently a Jerry is to blame for this mess.
Starting the level greets us with the message "Jerry f\*cked up, he forgot the password for this level but he mumbled something about a robots.txt file and something about a hint.."

Oh well that can't be too hard then! Let's just look at the `robots.txt` file which is usually stored in the webroot of a website and is used for webcrawlers (for example how google indexes the web) to tell them where too look for specific information and how to scan the site or which areas are not to be scanned.

So taking a look at the url [https://0xf.at/robots.txt](https://0xf.at/robots.txt) shows that there is a section `Disallow` with a hint on the solution:

```txt
User-agent: *
Allow: /

Disallow: /play/solutionforlevel7 #don't allow google to find the solution for level 7
```

Disallow in this case makes sure google and co don't index that very page so we can't just google for the solution ;)
Looking at the page https[]()://0xf.at/play/solutionforlevel7 (not hyperlinked so I don't _fully_ ruin the fun) shows us that Jerry seems to be a little bit full of themselves.

{{< figure src="images/lvl7_1.png" alt="I don't know how you found it but you've found it!" >}}

But I guess "_jerryIsDaBossc0_"

## Level 8 - The password field

This one is a neat one that could actually prove useful in some case.
It focuses more on HTML than JavaScript and there are multiple approaches to it

{{< figure src="images/lvl8_1.png" alt="Typo in password!" >}}

Now the easiest solution would be to just send the password as is and look a the GET request query - since the password is passed as a regular parameter.

{{< figure src="images/lvl8_2.png" alt="GET Query" >}}

Just change the "0" to a "o" and send it again, level solved.

But there is a funnier version that seems to be more in line with what the author might have wanted us to do.
Let's look at the HTML code.

```html
<h1>Level 8</h1>
<p>
  Nice, someone already entered the password but they made a small mistake.<br />The
  FIRST occurring 0 (zero) should actually be a small "o".
  Can you fix it?
</p>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  function checkPW() {
    var el = document.getElementById("pw");
    window.location.href = "?pw=" + el.value;
  }
</script>

[some more irrelevant code]
```

The password filed is an input field and has the id `pw` with the type `password`.
We can just change the type to `text` and the text appears in our browser and we can edit the wrong password! So turning "z6sjdxnix9kfcu6qdnq809jtdp9k1edt" into "_z6sjdxnix9kfcu6qdnq8o9jtdp9k1edt_" gives us the same solution as the previous idea.

## Level 9 - Calculations

This one is just math and making sure you use the right variable in the right order!

```html
<h1>Level 9</h1>
<p>Let's think this through</p>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  function checkPW() {
    var foo = 5 + 6 * 7;
    var bar = foo % 8; //modulo.. look it up if you don't know what it does
    var moo = bar + 1;
    var rar = moo / 3;
    var el = document.getElementById("pw");
    if (el.value.length == moo) window.location.href = "?pw=" + el.value;
    else alert("Wrong password");
  }
</script>
```

So `foo` is 47.
This modulo 8 gives us 7 (for `bar`) which we add 1 to to get the solution of 8 for `moo`.
`rar` is not even relevant as `moo` is the variable used in the condition for the password.
The tricky bit is that in here not the password itself is compared with `moo` but the length of the password.
So what we enter is irrelevant as long as the length is 8! So lets try the super secret passphrase of "_asdf1234_".
Very nice!

## Level 10 - Variables

With this level it's back to the JavaScript challenges so first let's get the code from the website.

```html
<h1>Level 10</h1>
<p>Try not to be fooled</p>
<input id="pw" type="password" />
<br /><input type="button" value="OK" onclick="checkPW()" />
<script type="text/javascript">
  var CodeCode = "moo0bc";

  function checkPW() {
    "+CodeCode+" == "0xf.at_hackit";
    var el = document.getElementById("pw");
    if (el.value == "" + CodeCode + "")
      document.location.href = "?pw=" + el.value;
    else alert("Wrong password");
  }
</script>
```
Many fancy lines of code.
But looking closely nothing really is happening with the initial value for `CodeCode`.
Just some concatenation that has no effect and some very strange comparisons that also have nothing to do with the initial value.
So it's all about reading the code a little.
Very simple to see the solution here can only be "*moo0bc*"

## Final words for the first levels

All in all some easy but still fun challenges that makes you think a little more how things work in a browser, be it how JavaScript works or what the `robot.txt` file is and where to find it on a website!

To keep things a little more organized I've cut the Levels down into ten Level segments so it's not just one giant blog post but is a little more easy to navigate. 
You can find the writeup for the next ten levels [here]({{< ref "/posts" >}}) once they are up!