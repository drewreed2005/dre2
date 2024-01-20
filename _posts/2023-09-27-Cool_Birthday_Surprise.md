---
toc: false
comments: true
layout: post
title: Cool Birthday Surprise
description: An incredible birthday surprise.
---

<button id="bday_button" onclick="bdayHappening()">Click this button</button>
<div id="bday_text"></div>
<div id="click_this" style="display: none;"><a href="https://freepngclipart.com/download/awesome/3430-awesome-awesome-image-hd-image.jpg">click this link now</a></div>
<script>
    function bdayHappening() {
        document.getElementById("bday_button").style.display = "none";
        document.getElementById("bday_text").innerHTML = "Hapy brithday to Michael A Reed, my dad";
        document.getElementById("click_this").style.display = "block";
    }
</script>