+++ 
draft = true
date = 2023-03-14T23:55:16+01:00
title = "Solving 0xf.at - Part 2"
description = "A little collection of the approaches and stuff I used to solve 0xf.at challenges"
slug = ""
authors = ["_N0x"]
tags = ["hackit", "challenges", "riddles", "0xf.at"]
categories = []
externalLink = ""
series = []
+++

# Solving 0xf.at's funny little challenges - Part 2 Level 11 to 20

## WARNING!
**THE FOLLOWING PAGE CONTAINS SPOILERS FOR THE PASSWORD-RIDDLE SITE 0XF.AT!**
_Only continue reading if you are okay with seeing spoilers or need some help with the riddles ;)_

```
  ___        __       _
 / _ \__  __/ _| __ _| |_
| | | \ \/ / |_ / _` | __|
| |_| |>  <|  _| (_| | |_
 \___//_/\_\_|(_)__,_|\__|
```

To keep things a little more organized I've split up the solutions and approaches into ten challenge segments so not everything is crammed into a single post. In here we will look at levels 11 to 20. For Level 1 to 10 take a look at [the first post]({{< ref "0xfat_1" >}}).

## Level 11 -  Understand the algorithm

The next challenges are more focused on PHP, programming and logic.
Level 11 shows us the PHP function with which the password is calculates:

```php
function pwCheck($password)
{
    if($password==date("d.m.Y")) //GMT +1
        return true;
    else return false;
}
```

Even without much PHP knowledge we can assume that the `date` function might give us the current date in the specified format of `d.m.Y`. 
PHP has a [very good documentation](https://www.php.net/docs.php) so we can just take a look a the [`date` function](https://www.php.net/manual/en/function.date) there.
It shows that it is used to format a UNIX timestamp into the specified format.
The meaning of the [format](https://www.php.net/manual/en/datetime.format.php) string is also easily found in the documentation and shows us that `d.m.Y` stands for
 - d = Day of month (with leading zero!) e.g. `15`
 - m = Month (with leading zero as well) e.g. `03`
 - Y = Full year e.g. `2023`
Since no further parameter is specified for `date` it just defaults to the current timestamp so the solution will be the current date written as for example "*15.03.2023*"

## Level 12 - Sums

This level requires us to enter the sum of all (integer) numbers from 1 to (in my case) 465.
We could either write a little program that loops through all those number and sums them up but it's way easier to use the Gaussian summation formula of `( n ( n+1 ) ) / 2` so in this case `( 465 ( 465+1 )) / 2 = 108345`.
So "*108345*" is our solution for this level!

## Level 13 - Understand the algorithm II

Another PHP challenge!

```php
function pwCheck($username,$password)
{
    if(!$username || !$password) return false;
    if(strlen($username)==$password)
        return true;
    else return false;
}
```

}
```

This time we have two fields to fill - `Username` and `Password`. 
First we see that Username and Password can't be the same so we have to type something different for each of them.
it seems the only condition we need to meet to pass this is that the password as a numeric value has to be the length of the username!
Not that hard either and we can freely choose what to try so let's for for something like `tooeasy` for the username and `7` for the password.

## Level 14 - Understand the algorithm IV

Strangely enough the fourth algorithm comes before the third (Level 16) but oh well. 
This time we need to enter a GUID - so a sort of long, standardized identifier -  as well as a password and the code displayed is:

```php
function pwCheck($guid,$password)
{
	if(!$guid || !$password) return false;
    $users = implode(file('/data/login_info.json'));
	$json = json_decode($users,true);

	foreach($json['result'] as $data)
		if($data['guid']==$guid && $data['password'] == $password && $data['account_status']=='active')
			return true;
	return false;
}
```

For this challenge a little more PHP knowledge is required. 
The first check makes sure both the `guid` field and the `password` field are actually filled with a value.
Next a JSON file `/data/login_info.json` is loaded as an array using the `file` method as well as `implode` to turn a string into an array. 
`json_decode` is used on that array to turn the content of the file into a JSON object to further process. 
Next each `result` object in the JSON file is checked if the `guid` and `password` match and if the account is still active. 
Now we know what the method is checking for so we just need to take a look a the file which is located at https[]()://0xf.at/data/login_info.json. 
Only one account is marked as active so we grab the guid "*bc3c1364-4b24-4f60-8fe4-7628e72391ed*" and the password "*Vencom*" aaaand success!

## Level 15 - The 0xf.at Enigma

{{< figure src="images/lvl15_1.png" alt="0xf.at Enigma" >}}