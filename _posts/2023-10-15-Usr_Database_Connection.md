---
toc: false
comments: true
layout: post
title: Usr Database Connection Test
description: Test to show backend pull with JWT.
courses: { csa: {week: 8} }
type: tangibles
---

Input email: <input id="uid" type="text">
<br>
Input password: <input id="password" type="text">
<button onclick="login_user()">Log in Usr</button>

<script>
    /// URL for deployment
    // var url = "https://spring.nighthawkcodingsociety.com"
    // Comment out next line for local testing
    url = "http://localhost:8085"
    // Authenticate endpoint
    const login_url = url + '/authenticate';


    function login_user(){
        // Set body to include login data
        const body = {
            email: document.getElementById("uid").value,
            password: document.getElementById("password").value,
        };

        // Set Headers to support cross origin
        const requestOptions = {
            method: 'POST',
            mode: 'cors', // no-cors, *cors, same-origin
            cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
            credentials: 'include', // include, *same-origin, omit
            body: JSON.stringify(body),
            headers: {
                "content-type": "application/json",
            },
        };

        // Fetch JWT
        fetch(login_url, requestOptions)
        .then(response => {
            // trap error response from Web API
            if (!response.ok) {
                const errorMsg = 'Login error: ' + response.status;
                console.log(errorMsg);
                return;
            }
            // Success!!!
            // Redirect to Database location
            // window.location.href = "/APCSA/data/database";
        })
    }
</script>