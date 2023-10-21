---
toc: false
comments: true
layout: post
title: Usr Database Connection Test
description: Test to show backend pull with JWT.
courses: { csa: {week: 8} }
type: tangibles
---

## Login and Get Pull

Input email: <input id="email" type="text">
<br>
Input password: <input id="password" type="text">
<button onclick="login_user()">Log in Usr</button>

<button id="read_button" onclick="read_pull()" style="display:none;">Read Pull</button>

## User Creation

Input email: <input id="email_creator" type="text">
<br>
Input password: <input id="password_creator" type="text">
<br>
Input first and last name: <input id="name_creator" type="text">
<button onclick="create_user()">Create Usr</button>

<script>
    flowerDisable = true;
    /// URL for deployment
    // var url = "https://spring.nighthawkcodingsociety.com"
    // Comment out next line for local testing
    // Authenticate endpoint
    const login_url = "http://localhost:8085/authenticate";
    // prepare URL
    //var url = "https://spring.nighthawkcodingsociety.com/api/person/";
    // Uncomment next line for localhost testing
    const read_url = "http://localhost:8085/api/usr/";
    //var url = "https://spring.nighthawkcodingsociety.com/api/person/";
    // Uncomment next line for localhost testing
    const post_url = "http://localhost:8085/api/usr/post";

    function login_user() {
        // Set body to include login data
        var authBody = {
            email: document.getElementById("email").value,
            password: document.getElementById("password").value
        };

        // Set Headers to support cross origin
        var authOptions = {
            method: 'POST',
            mode: 'cors', // no-cors, *cors, same-origin
            cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
            credentials: 'include', // include, *same-origin, omit
            body: JSON.stringify(authBody),
            headers: {
                "content-type": "application/json",
            },
        };

        // Fetch JWT
        fetch(login_url, authOptions)
        .then(response => {
            // trap error response from Web API
            if (!response.ok) {
                const errorMsg = 'Login error: ' + response.status;
                console.log(errorMsg);
                return;
            }
            document.getElementById("read_button").style.display = "block";
            // Success!!!
            // Redirect to Database location
            // window.location.href = "/APCSA/data/database";
        })
    }

    function read_pull() {
        // set options for cross origin header request
        const options = {
            method: 'GET', // *GET, POST, PUT, DELETE, etc.
            mode: 'cors', // no-cors, *cors, same-origin
            cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
            credentials: 'include', // include, *same-origin, omit
            headers: {
            'Content-Type': 'application/json',
            },
        };

        // fetch the API
        fetch(read_url, options)
            // response is a RESTful "promise" on any successful fetch
            .then(response => {
            // check for response errors and display
            if (response.status !== 200) {
                const errorMsg = 'Database response error: ' + response.status;
                console.log(errorMsg);
                return;
            }
            // valid response will contain json data
            response.json().then(data => {
                console.log(data);
            })
        })
    }

    function create_user() {
        const createBody = {
            email: document.getElementById("email_creator").value,
            password: document.getElementById("password_creator").value,
            name: document.getElementById("name_creator").value
        };
        // url encoding
        const email = encodeURIComponent(document.getElementById("email_creator").value);
        const password = encodeURIComponent(document.getElementById("password_creator").value);
        const name = encodeURIComponent(document.getElementById("name_creator").value);
        // updated post url
        const updatedPostUrl = post_url + `?email=${email}&password=${password}&name=${name}`;
        // set options for cross origin header request
        const postOptions = {
            method: 'POST',
            mode: 'cors', // no-cors, *cors, same-origin
            cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
            credentials: 'include', // include, *same-origin, omit
            body: JSON.stringify(createBody),
            headers: {
                "content-type": "application/json",
            },
        };

        // fetch the API
        fetch(updatedPostUrl, postOptions)
            // response is a RESTful "promise" on any successful fetch
            .then(response => {
            // check for response errors and display
            if (response.status !== 200) {
                const errorMsg = 'Database response error: ' + response.status;
                console.log(errorMsg);
                return;
            }
            // valid response will contain json data
            response.json().then(data => {
                console.log(data);
            })
        })
    }
</script>