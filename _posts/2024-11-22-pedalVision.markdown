---
layout: post
title: "PedalVision - A Persistence of Vision Bike Display"
date: 2024-11-22 14:23:31 -0800
categories: projects
---

This is where I'll write all about "Pedal Vision" (name pending).

|              ![Image](/images/hamster.jpg)              |
| :-----------------------------------------------------: |
| If you look closely, you can see the individual diodes! |

There's loads of these persistence of vision displays out there to buy and attach to your bike. The lights are arranged in a single line going from the center of the wheel to the edge. As the bike wheel rotates, the lights change colors and sweep a continous image. The colors change at the right time to put the correct pixels at the right places. I really wanted to have my own and not only have it but UNDERSTAND it. The best way to truly understand something is to create it yourself! Or, barring that, reading a well written detailed blog post from someone who's already done it!

Ok so before we start, we need a spec. What do we expect this thing to do? What features do we want? We'll later talk about how these features can be achieved with hardware / software.

First off, it's gotta actually show an image at a reasonable speed. The faster the speed of the bike, the faster the wheel spins and the easier the POV effect is to produce. The human eye has a persistence of vision "time" of about 1/16th of a second. Various sources say other numbers but 1/16th seems like a common occurence. It really varies by image brightness and ambient lightning conditions. It's longer in the dark! But let's go with 1/16th of a second. Remember how I mentioned the light display is just a line of lights that spans from the center of the wheel outward? This line of lights needs to complete a full rotation in at least 1/16th of a second in this case for us to see a full image covering 360 degrees.

A typical bike wheel is about 700mm in diameter giving us a circumference of about 2.2 meters. So the wheel needs to travel 2.2 meters in 1/16th of a second along the ground to make the effect work. That's ughh.. Oh no.. That's 126 km/hr. (Almost 80 mph)! Yeah, that's not gonna work. Any easy way to fix this is to add another strip of lights 180 degrees from the first strip! Now, each light strip needs to sweep across 180 degrees (rather than 360) in order to create a full image. So we need to now only travel at half the speed which is still... 63 km/hr. Ok you see where I'm going with this now right? Let's cut to the chase and add two more light strips so that each one is spaced apart by 90 degrees. Each strip now only needs to sweep 90 degrees to complete a full image. We now only need to go a quarter of the original speed which is about 31 km/hr (20 mph). That's totally doable! When doing this at night we can expect even slower speeds to be perfectly sufficient!

Next, we want as detailed of an image as we can get. We want the resolution of our image to be as high as possible. In something that spins and sweeps LEDs in a circle, the resolution can be expressed as a radial and an angular resolution. The radial portion is just how many LEDs you can fit into a single strip going from the center to the edge of the wheel. The angular resolution is determined by how often you switch the LED colors as they spin. For my bike wheel, I've been able to pack 42 of these LEDs into each strip and have determined an angular resolution of 1 degree is suitable. Any lower starts to bring in diminishing returns. Higher resolutions mean higher storage requirements. A higher angular resolution requires ever faster color switching speeds. With our 1 deg of angular resolution and 42 LEDs per strip radially, we get 360 _ 42 = 15120 pixels per image. Taking that a step further, given that each pixel is a set of three values for Red, Green and Blue, we need 15120 _ 3 = 45360 numerical values per image. Considering each color value ranges between 0 and 255 which is the size of a byte, we need 45360 bytes to store a single image.

To add speed into the equation, if the bike is travelling at 31 km/hr it will be completing about 4 revolutions per second. So each strip will need to sweep through 360 \* 4 = 1440 colors per second or 0.00069 seconds (nice) per color.

So what LEDs can we use to do this? Enter the SK9822 Also known as the Adafruit DotStar. Looking at it's datasheet there's an equation to calculate the refresh rate of the LEDs based on how many you have connected together. With our 42 per strip and 4 strips running it through that equation (at maximum clock speed of 30 mHz) gives us a frame rate of 5514 frames per second. That's way more than the 1440 colors per second we need. Of course, the required FPS changes depending on bike speed so in theory these can work up to 117 km/hr. So there is no worry there with LED switching speed.

We want to be able to switch to any image we want. Upload any image and have it be displayed. This means we need some WiFi. In this case I'll slap on an ESP32 as the microcontroller of choice and call it a day. In addition to this there needs to be some app that will connect to our ESP32 WiFi and transmit the image over. In addition to THIS, we need some way to convert a regular image format like JPG or PNG to a list of RGB pixel values arranged in circular coordinates. As we will see later, the data must be arranged in a certain way to have the microcontroller send it to the LEDs very quickly.

We want animations. If we can display an image, why not a sequence of images in quick succession?

Waterproof? Ehh maybe in the future? That would involve some conformal coating and I'd rather not right now.

Needs to run off of a battery.

Just a code snippet test for later
{% highlight javascript %}
const a = 123;
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]: https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
