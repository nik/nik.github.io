---
layout: post
title:  "Strings & Arrays - Unique Characters"
date:   2016-05-18 15:23:21 -0400
categories: strings&arrays
---

There are several ways to figure out whether a string has all unique characters.

<p>
  There are several ways to do this. In my opinion, the best way is to use a bit vector. This will include some basic knowledge
  of bit arithmetic. So let's quickly remember some basic bit logic.
  <ul>
    <li><code>1 | 0 = 1</code></li>
    <li><code>0 | 0 = 0</code></li>
    <li><code>1 | 1 = 1</code></li>
  </ul>
  <ul>
    <li><code>1 & 0 = 0</code></li>
    <li><code>0 & 0 = 0</code></li>
    <li><code>1 & 1 = 1</code></li>
  </ul>

  One of the other funky operators is the left bit shift operator <code>>></code>. It will shift each bit on the left-hand side to the left
  by the number of positions given by the right hand side.
  It is a lot easier to show with 0s and 1s. So, <code>0001 << 1</code> will produce <code>010</code> and by the same logic
  <code>0001 << 3</code> will produce <code>1000</code>
  <br/>
<p>

<p>
  So let's dig into some actual code.
</p>

{% highlight java %}
public boolean hasUniqueChars(String word) {
  int checker = 0;
  for (int i = 0; i < word.length(); i++) {
    int val = word.charAt(i) - 'a';
    int mask = (1 << val);
    if ((checker & mask) > 0) {
      return false;
    }
    checker = checker | mask;
  }
  return true;
}
{% endhighlight %}

<p>
  One of the main things to note here is the <code>mask</code> variable and <code>val</code>.
  First off -- this code assumes that the letters we're dealing with are lower case a-z. The TL;DR as to what's happening here
  is that we loop through each letter in the string, and create a value from the difference between that letter and the first
  possible character in the range, in this case being 'a'. We're using ASCII codes to create this value. Whatever val gets
  set to, we use that value to set the mask. The mask is then checked against <code>checker</code> and if there's a "collision",
  meaning the value already exists, checker will be greater than 0. If there's no collision, then checker is 'or-ed' against the
  mask and where there is a <code>1</code> in the binary represenation of <code>val</code>. 
  <br/>
  <br/>
  Let's write out what happens in binary with the string "sas".
  <br/>
  First loop, 's'
  <br/>
    {% highlight java %}
      int val = word.charAt('s') - 'a'; // will result in 0b10010 (18)
      int mask = (1 << val); // will result in 0b1000000000000000000 (262144)
      checker & mask > 0 // will result in false since there are no (1 & 1) being compared
      checker = checker | mask; // will result in 0b1000000000000000000 (262144)
    {% endhighlight %}
  Second loop, 'a'
    {% highlight java %}
      int val = word.charAt('a') - 'a'; // will result in 0b00000 (0)
      int mask = (1 << val); // will result in 0b0000000000000000001 (1)
      checker & mask > 0 // will result in false since there are no (1 & 1) being compared
      checker = checker | mask; // will result in 0b1000000000000000001 (262145)
    {% endhighlight %}
  Lastly loop, 's'
    {% highlight java %}
      int val = word.charAt('s') - 'a'; // will result in 0b10010 (18)
      int mask = (1 << val); // will result in 0b1000000000000000000 (262144)
      checker & mask > 0 // will result in true since there is a 1 in checker and val, thus a collision
    {% endhighlight %}

  So just to go over what just happened. A value was created for a character in the string. The character was used
  to create a mask. The mask is bitwise and-ed to <code>checker</code> to check whether it already exists because if 
  there's a 1 in the binary representation, then the comparison will return greater than 0. If it doesn't exist,
  checker is <code>or-ed</code> against the calculated value of the character and a 1 is set in the binary position of
  the mask. Something you might have noticed by now -- the difference between 'a' and a character isn't necessary to
  get the correct answer. The reason a value is subtracted is because left bit shifting a huge number will result in
  enormous <code>val</code>'s. I dig this algorithm. 10/10.
</p>

