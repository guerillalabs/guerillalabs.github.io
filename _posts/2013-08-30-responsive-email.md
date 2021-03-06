---
layout: blog
title: Responsive Email and You
author: Nick Pruitt
categories: Blog
tags: [ blog, rss, html, email ]
css: screen
code: true
excerpt: <p>Let us face a truth together &#8212; coding an HTML email is maybe one of the worst things ever. At least, that&#8217;s how I think most people view it. I find it a strangely interesting challenge. It all depends on how you look at it, I guess.</p>
origin: originally posted at <a href="http://nickpruitt.com/2013/01/28/responsive-email/">nickpruitt.com</a>
---

Let us face a truth together: coding an HTML email is maybe one of the worst things ever. At least, that's how I think most people view it. I find it a strangely interesting challenge. It all depends on how you look at it, I guess. On one hand, you are relegated to using old outdated methods. The excellent [Campaign Monitor Guidelines](http://www.campaignmonitor.com/resources/will-it-work/guidelines) --- which you simply must read if you're going to code an HTML email --- states that you should expect to take your coding skills back a good decade. That means inline styles, forgetting about using floats for pretty much anything, and, yes, using tables for your layout. But on the other hand, because of these limitations, you can get away with just about anything, which is interesting. You can do the ugliest, hackiest thing ever, the kind of thing that makes you wake up in a cold sweat in the middle of the night, and so long as it works, it's pretty much okay. This is kind of awesome in its own strange way. Pull off an awesome end-result, even with lots of ugly, and you can be proud of your work.

So when I was tasked with figuring out a mobile solution for our email campaigns, I was both intrigued and a little intimidated. How with decade old techniques could I do this? But then again, how with decade old techniques *could* I do this? It was fun to figure out. At the outset, we decided to support the email clients listed in the [Campaign Monitor CSS Support Table](http://www.campaignmonitor.com/css) short list. For me this means, forget you Lotus Notes, and forget you Blackberry.

In the end we are really pleased with the results. Here is the [email](http://nickpruitt.com/work/responsive-email/store_email_responsive_template_standard.html), and here are a few main points on its construction.

## The magic of align=&#8220;left"

The main question I had going into this project was, "How will I do this without floats?" Floats come in handy when you're building responsively, following the pattern of starting with stacked elements in the narrow, mobile context, and then floating them as you have more screen real estate and having them appear all nice and side-by-side. But CSS support for floats is fairly abysmal in the clients we chose to support, leaving us with the question of just what to do.

<aside class="note">
    <p>Yeah, I know that's a gross simplification of responsive design. That layout is only one component of it. That it's not all not stacking and unstacking stuff. Even that it's not solely about hitting certain viewports because you just make a bunch of fixed width layouts. I get it.</p>

    <p>Basically, this all depends on the design, and for the design of this particular email, going to Stacksville is the right thing to do. So there.</p>
</aside>

Turns out the answer is simple. You can just use <code class="language-markup"><table align="left"></code>. Yes, our ancient friend <code class="language-markup">align="left"</code>. Not since my days of working in a horrible, outdated, inhouse CMS at my first job have I used it. This humble bit of history is what makes all the stacking in the responsive email possible. When there is room for both elements, they appear side-by-side. When there isn't, we make the width of those elements <code class="language-css">100%</code> and let them stack. This is surprisingly effective. But how are we getting the <code class="language-css">100%</code>, you may ask? Why, what a nice segue to my next point.

## Media Queries, of course

The next piece of pulling off this responsive email was the obvious inclusion of media queries. How is this obvious when most email clients render pages worse than Netscape did while you were still looking at Star Trek fan pages in your mamma's basement over the extra phone line? As fate would have it, mobile email clients in general have way better CSS support than their desktop brethren (looking at you Outlook 2007/10/13, you miserable sonsofshamsters!). So we can use media queries and some well placed classes to make it happen.

I set up a main table to be the background and contain everything and then nested tables inside that with a class of <code class="launguage-markup">inner</code>. Then I topped it off with a class of <code class="language-css">flex</code> on images that I wanted to resize. After that, the bulk of what I needed to start seeing things work and resize was handled with a surprisingly small amount of CSS:

<pre>
<code class="language-css">@media only screen and (max-width: 480px) {
    table[id=wrapper] .inner {
        width:100% !important;
    }
    img[class=flex] {
        max-width:100% !important;
        height:auto !important;
    }
}</code>
</pre>

Remember that the <code class="language-css">!important</code> is --- well --- important because you are overriding the width of the table for wider resolutions than are specified in your inline styles on that element. Hey, I warned you it was gross.

## The swapping banner trick

The main banner at the top was getting too short and the text too small at lower resolutions, so we decided to use this as an opportunity to serve up an image just for mobile users. There is added bonus here too if we can highlight the coupon code, so someone can just pull up the email at a store to redeem it.  Generally, you can pull this off by hiding the <code class="language-markup">img</code> and setting a background image on its containing element with something to this effect.

<pre>
<code class="language-css">@media only screen and (max-device-width: 480px) {
    td[class="hero"] {
        background-image: url(images/header-mobile.png);
        width: 325px !important;
        height: 115px !important;
    }
    td[class="hero"] img {
        display: none;
    }
}</code>
</pre>

<pre>
<code class="language-markup">&lt;table width=&quot;100%&quot; border=&quot;0&quot; cellspacing=&quot;0&quot; cellpadding=&quot;0&quot;&gt;
   &lt;tr&gt;
      &lt;td class=&quot;hero&quot;&gt;
        &lt;img src=&quot;images/header-desktop.png&quot; border=&quot;0&quot; width=&quot;600&quot; /&gt;
    &lt;/td&gt;
   &lt;/tr&gt;
&lt;/table&gt;</code>
</pre>

But in our case, I wanted it to be clickable and scalable. So simply switching it out inside the <code class="language-markup"><td></code> wasn't going to cut it. So I did the unthinkable. I put it in there twice in the HTML. I know, gross. But in the world of email, I just wanted to survive.

<pre>
<code class="language-markup">&lt;table id=&quot;content&quot; class=&quot;inner&quot; cellpadding=&quot;0&quot; cellspacing=&quot;0&quot; border=&quot;0&quot; width=&quot;700&quot; align=&quot;center&quot;&gt;
    &lt;tbody&gt;
        &lt;tr&gt;
            &lt;td height=&quot;5&quot;&gt;&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td class=&quot;hero&quot; align=&quot;center&quot;&gt;
                &lt;a href=&quot;http://www.lifeway.com/images/4c93d4fa-567a-4ec8-914a-2462c2908c9a.pdf&quot;&gt;&lt;img id=&quot;desktop-hero&quot; class=&quot;flex&quot; src=&quot;http://s7d9.scene7.com/is/image/LifeWayChristianResources/20121106%5Fvets%5Fcoupon%5F110112?scl=1&quot; border=&quot;0&quot; width=&quot;694&quot; height=&quot;200&quot; style=&quot;display: block;&quot; alt=&quot;Veterans Day Coupon&quot; /&gt;&lt;/a&gt;

                &lt;a href=&quot;http://www.lifeway.com/images/4c93d4fa-567a-4ec8-914a-2462c2908c9a.pdf&quot;&gt;&lt;img id=&quot;mobile-hero&quot; class=&quot;flex&quot; src=&quot;http://s7d9.scene7.com/is/image/LifeWayChristianResources/hero%5Falt?fmt=jpg&amp;qlt=100,1&quot; border=&quot;0&quot; style=&quot;height:0;width:0;&quot; alt=&quot;Veterans Day Coupon&quot; /&gt;&lt;/a&gt;
            &lt;/td&gt;
        &lt;/tr&gt;
    &lt;/tbody&gt;
&lt;/table&gt;</code>
</pre>

Then I swap out which one is visible thusly:

<pre>
<code class="language-css">@media only screen and (max-width: 480px) {
    img[id=desktop-hero]{
        display:none !important;
    }
    img[id=mobile-hero]{
        display:block !important;
        width:100% !important;
    }
}</code>
</pre>

Note that Gmail doesn't understand <code class="language-css">display:none;</code>, so I set the width and height to zero to hide the mobile version. But then Outlook doesn't understand either of these methods, so I ended up having to add a conditional comment just to hide this from Outlook 2007/10/13. Then it would be hidden in Outlook through the conditional comment, and from everything else through the CSS.

<pre>
<code class="language-markup">&lt;table id=&quot;content&quot; class=&quot;inner&quot; cellpadding=&quot;0&quot; cellspacing=&quot;0&quot; border=&quot;0&quot; width=&quot;700&quot; align=&quot;center&quot;&gt;
    &lt;tbody&gt;
        &lt;tr&gt;
            &lt;td height=&quot;5&quot;&gt;&lt;/td&gt;
        &lt;/tr&gt;
        &lt;tr&gt;
            &lt;td class=&quot;hero&quot; align=&quot;center&quot;&gt;
                &lt;a href=&quot;http://www.lifeway.com/images/4c93d4fa-567a-4ec8-914a-2462c2908c9a.pdf&quot;&gt;&lt;img id=&quot;desktop-hero&quot; class=&quot;flex&quot; src=&quot;http://s7d9.scene7.com/is/image/LifeWayChristianResources/20121106%5Fvets%5Fcoupon%5F110112?scl=1&quot; border=&quot;0&quot; width=&quot;694&quot; height=&quot;200&quot; style=&quot;display: block;&quot; alt=&quot;Veterans Day Coupon&quot; /&gt;&lt;/a&gt;

                &lt;!--[if !mso]&gt;&lt;!--&gt;
                    &lt;a href=&quot;http://www.lifeway.com/images/4c93d4fa-567a-4ec8-914a-2462c2908c9a.pdf&quot;&gt;&lt;img id=&quot;mobile-hero&quot; class=&quot;flex&quot; src=&quot;http://s7d9.scene7.com/is/image/LifeWayChristianResources/hero%5Falt?fmt=jpg&amp;qlt=100,1&quot; border=&quot;0&quot; style=&quot;height:0;width:0;&quot; alt=&quot;Veterans Day Coupon&quot; /&gt;&lt;/a&gt;
                &lt;!--&lt;![endif]--&gt;
            &lt;/td&gt;
        &lt;/tr&gt;
    &lt;/tbody&gt;
&lt;/table&gt;</code>
</pre>

This was by far my most grevious sin. On my next responsive email, I would like to try hiding the image inside the link element and setting a background image on it. Then maybe both could scale in peaceful harmony using some kind of <code class="language-css">background-size</code> trickery. I know it's an email and all, but it would at least be cleaner and leave those with crappy clients a nicer fallback. Deep down Lotus Notes users are human, so how can I completely turn my back on my brothers? I have more of these scheduled, so more to come on that soon.

## The cool trick of the spacer ##

This is so blatantly obvious and simple that I almost didn't include it, but I think it's cool so I'm doing it anyway! There were certain situations where I needed more space around an element at lower resolutions, say some text that got a little too close to the edge of the screen.

<pre>
<code class="language-css">@media only screen and (max-width: 480px) {
    div[class=spacer] {
        margin-top:0;
        margin-right:12px;
        margin-bottom:0;
        margin-left:12px;
    }
}</code>
</pre>

<pre>
<code class="language-markup">&lt;tr valign=&quot;top&quot;&gt;
    &lt;td class=&quot;tiled&quot; width=&quot;50%&quot; style=&quot;padding-right:7px;&quot;&gt;
        &lt;a href=&quot;http://www.lifeway.com/Product/jesus-today-P005525243&quot;&gt;
            &lt;img class=&quot;flex&quot; src=&quot;http://s7d9.scene7.com/is/image/LifeWayChristianResources/20121106%5Fjesus%5Ftoday%5Frev?fmt=jpg&amp;qlt=100,1&quot; border=&quot;0&quot; style=&quot;display: block; padding-bottom:10px;&quot; alt=&quot;The Newest From Sarah Young - Jesus Today&quot; width=&quot;155&quot; height=&quot;155&quot; /&gt;
            &lt;div class=&quot;spacer&quot;&gt;
                &lt;span style=&quot;color:#225d99; font-family:verdana, arial, helvetica; font-size:13px;&quot;&gt;&lt;strong style=&quot;font-weight:bold;&quot;&gt;Jesus Today&lt;/strong&gt;&lt;br/&gt;by Sarah Young&lt;/span&gt;
            &lt;/div&gt;
        &lt;/a&gt;
        &lt;div class=&quot;spacer&quot;&gt;
            &lt;strong style=&quot;font-family:verdana, arial, helvetica; font-size:12px; color:#d2222a;&quot;&gt;SALE - $9.59&lt;/strong&gt;
            &lt;br/&gt;&lt;span style=&quot;font-family:verdana, arial, helvetica; font-size:12px; color:#555555;&quot;&gt;Regular - $15.99&lt;/span&gt;
        &lt;/div&gt;
    &lt;/td&gt;
&lt;/tr&gt;</code>
</pre>

Voila! Insta-add-space-only-when-you-need-it-class! This also performed quite well because mobile email clients actually understand margins where many desktop ones don't. How novel a concept.

## Litmus Test

Our last step is to run the email through [Litmus](https://litmus.com) for final testing. Litmus is a seriously awesome and easy testing tool for running this stuff through email clients quickly. I lived in it my last day of development, and it worked just great. So here we are looking good on mobile and on desktop, and working in our clients of choice. And because Litmus is cool, you can view the results of the test [here](https://litmus.com/pub/107ea49).
