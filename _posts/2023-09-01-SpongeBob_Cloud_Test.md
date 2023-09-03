---
title: SpongeBob Cloud Test
layout: default
description: Trying to make it work for my whole site.
comments: true
hide: true
courses: { csa: {week: 2} }
type: hacks
---

## Sample Header

This is sample text to see how it looks when text is included as well. There could be lots and lots of text to make this situation better. There are all sorts of things this could say, and there are even code blocks and bullets that could get in front.
- The bullets will look like this.
    - The subbullets are like this.
        - And there are even sub-subbullets. Crazy, huh?
- More bullet thing.
- More, even.
- Even more.

### Subheader

Check this out, dude.

#### Sub-Subheader

For more testing.


Length.



Length.



Length.



Length.



Length.



Length.

<!--REMNANT OF FLOWER TEST
<div id="cloud-element" class="cloud-container">
    <img src="{{site.baseurl}}/images/flower-clouds/blue_flower.png" alt="SpongeBob Cloud" class="cloud">
</div>
-->

<style>
.cloud-container0 {
    position: absolute;
    z-index: 1;
    /*top: 20%;*/ /*consider again*/
    left: -100px; /* Start off the screen on the left */
    transform-origin: center;
    animation: moveRotate0 40s linear infinite, blur 40s linear infinite alternate;
}

.cloud-container1 {
    position: absolute;
    z-index: 1;
    /*top: 20%;*/ /*consider again*/
    left: -100px; /* Start off the screen on the left */
    transform-origin: center;
    animation: moveRotate1 40s linear infinite, blur 40s linear infinite alternate;
}

.cloud {
    width: 25vw;
    z-index: 1;
    height: auto;
    opacity: 0.9; /* adjust opacity as needed */
    transition: filter 0.5s ease; /* smoothly transition blur effect */
}

@keyframes blur {
    0% {
        filter: blur(2px);
    }
    100% {
        filter: blur(5px); /* adjust blur amount as needed */
    }
}
</style>

<script>
    // variable for flower element replacement when it's time
    var currentCloud = 0;
    // variable for cloud images
    var flowerArray = ["blue_flower", "green_flower", "light_blue_flower", "orange_flower", "pink_flower", "yellow_flower"];

    var flowerObj1 = document.createElement("div");
    var flowerObj2 = document.createElement("div");
    var flowerStyle1 = document.createElement("style");
    var flowerStyle2 = document.createElement("style");

    var flowerObjects = [flowerObj1, flowerObj2];
    var flowerStyles = [flowerStyle1, flowerStyle2];

    function flowerSystem() {
        // getting a height reference from the body element
        var bodyHeight = document.getElementById("list_check_region").clientHeight;
        console.log(bodyHeight);

        // generating heights
        var startHeight = Math.floor(Math.random() * (bodyHeight - 400)) + 350;
        var endHeight = Math.floor(Math.random() * (bodyHeight - 400)) + 350;
        console.log(startHeight, endHeight);

        // creating animation
        var currentStyleElement = flowerStyles[currentCloud % 2];
        var currentAnimation = `@keyframes moveRotate` + String(currentCloud % 2) + ` {
            0% {
                transform: translateX(-20vw) translateY(-${startHeight}px) rotate(0deg);
            }
            100% {
                transform: translateX(120vw) translateY(-${endHeight}px) rotate(360deg);
            }
        }`
        currentStyleElement.textContent = currentAnimation;
        document.body.appendChild(currentStyleElement);

        // creating flower object
        var currentContainer = flowerObjects[currentCloud % 2];
        currentContainer.innerHTML = ""; // reset
        currentContainer.setAttribute("class", "cloud-container" + String(currentCloud % 2));
        var flowerImg = document.createElement("img");
        flowerImg.setAttribute("src", "{{site.baseurl}}/images/flower-clouds/" + flowerArray[Math.floor(Math.random() * 6)] + ".png");
        flowerImg.setAttribute("class", "cloud");
        currentContainer.appendChild(flowerImg);
        document.getElementById("list_check_region").appendChild(currentContainer);

        // increment which cloud variable
        currentCloud++;
    }

    flowerSystem();
    setInterval(flowerSystem, 20000);
</script>