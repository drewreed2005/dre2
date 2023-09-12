---
title: SSS Styling
layout: none
description: Making style for Silver Screen Stats temporarily.
comments: true
hide: true
courses: { csa: {week: 3} }
type: hacks
---

<body>
    <div id="movie_container">
        <div id="movie0" class="movie">
            <div id="player_score">Movie Points: 0</div>
            <img id="poster0" class="poster">
            <div id="results0" class="results"></div>
            <div id="button_container0" class="button_container"><button id="button0" class="movie_button" onclick="selectMovie(0, false)">⬆</button></div>
        </div>
        <div id="movie1" class="movie">
            <div id="timer_container"><img id="timer_icon" src="{{site.baseurl}}/images/timer-icon.png"><div id="timer_text">10</div></div>
            <div id="results1" class="results"></div>
            <img id="poster1" class="poster">
            <div id="button_container1" class="button_container"><button id="button1" class="movie_button" onclick="selectMovie(1, false)">⬆</button></div>
        </div>
    </div>
    <div id="results"></div>
    <div id="timer"></div>
</body>
<style>
    body {
        margin: 0;
        padding: 0;
    }
    #movie_container {
        display: flex;
        font-family: 'Open Sans', sans-serif;
    }
    #timer_container {
        position: absolute;
        text-align: right;
        align-items: right;
        font-size: 35px;
        margin: 5px 5px 5px 15px;
        text-shadow: 2px 2px 2px rgba(0, 0, 0, 0.9);
        color: white;
        z-index: 11;
    }
    #timer_icon {
        width: 30px;
        height: 30px;
        position: relative;
        top: 5px;
        margin: 0px 5px 0px 0px;
        filter: drop-shadow(2px 2px 2px white);
    }
    #player_score {
        position: absolute;
        font-size: 30px;
        margin: 5px;
        text-shadow: 2px 2px 2px rgba(0, 0, 0, 0.9);
        color: white;
        z-index: 11;
    }
    .movie {
        position: relative;
        border: 1px solid black;
        margin: 0px;
    }
    .results {
        position: absolute;
        color: white;
        text-align: center;
        align-items: center;
        justify-content: center;
        text-shadow: 2px 2px 2px rgba(0, 0, 0, 0.9);
        z-index: 11;
        font-size: 80px;
    }
    .movie_button {
        background-color: white;
        font-size: 40px;
        color: black;
        border: 5px solid black;
        border-radius: 50px;
        padding: 15px 40px;
        transition: background-color 0.3s, color 0.3s;
        position: relative;
        z-index: 11;
    }
    .movie_button:hover {
        background-color: black;
        color: white;
    }
    .button_container {
        position: absolute;
        align-items: center;
        text-align: center;
    }
    .poster {
        z-index: 1;
        position: absolute;
        object-fit: fill;
    }
</style>
<script>
    // consts
    const results = document.getElementById("results")
    const firstResults = document.getElementById("results0");
    const secondResults = document.getElementById("results1");
    const firstMovie = document.getElementById("movie0");
    const secondMovie = document.getElementById("movie1");
    const timerBox = document.getElementById("timer_text");
    const cursorPaths = ["paint_cursor", "mort_cursor"];
    let timerVar; // global for timer stopping/starting
    var currentTime = 10; // global time
    var currentMovies = [];
    var currentMovieScores = [];
    var playerScore = 0;
    var currentStreak = 0;
    var fetchData = 0; // needs to be global for some reason
    // get initial client width and height
    let currentWidth = document.body.clientWidth;
    let currentHeight = document.body.clientHeight;
    console.log("Width: " + String(currentWidth) + ", Height: " + String(currentHeight));
    for (let i = 0; i < 2; i++) {
        console.log(i);
        document.getElementById("movie" + String(i)).style.width = `${Math.ceil(currentWidth / 2)}px`;
        document.getElementById("movie" + String(i)).style.height = `${currentHeight}px`;
        document.getElementById("results" + String(i)).style.width = `${Math.ceil(currentWidth / 2)}px`;
        document.getElementById("results" + String(i)).style.top = `${Math.floor(currentHeight / 2.5)}px`;
        document.getElementById("button_container" + String(i)).style.width = `${Math.ceil(currentWidth / 2)}px`;
        document.getElementById("button_container" + String(i)).style.top = `${Math.floor(currentHeight / 1.7)}px`;
        document.getElementById("button" + String(i)).style.display = "none";
    }
    document.getElementById("timer_container").width = `${Math.ceil(currentWidth / 2)}px`
    // update global variables and resize movies
    function updateWindowDimensions() {
        currentWidth = document.body.clientWidth;
        currentHeight = document.body.clientHeight;
        console.log("Width: " + String(currentWidth) + ", Height: " + String(currentHeight));
        for (let j = 0; j < 2; j++) {
            document.getElementById("movie" + String(j)).style.width = `${Math.ceil(currentWidth / 2)}px`;
            document.getElementById("movie" + String(j)).style.height = `${currentHeight}px`;
            document.getElementById("poster" + String(j)).style.width = `${Math.ceil(currentWidth / 2)}px`;
            document.getElementById("poster" + String(j)).style.height = `${currentHeight}px`;
            document.getElementById("results" + String(j)).style.width = `${Math.ceil(currentWidth / 2)}px`;
            document.getElementById("results" + String(j)).style.top = `${Math.floor(currentHeight / 2.5)}px`;
            document.getElementById("button_container" + String(j)).style.width = `${Math.ceil(currentWidth / 2)}px`;
            document.getElementById("button_container" + String(j)).style.top = `${Math.floor(currentHeight / 1.7)}px`;
        }
        document.getElementById("timer_container").width = `${Math.ceil(currentWidth / 2)}px`;
    }
    // window resize listener
    window.addEventListener('resize', updateWindowDimensions);
    const options = {
        method: 'GET',
        headers: {
            accept: 'application/json',
            Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJmYzdmYzdmMTkzOWNlYWZjODA3MzZiZDUwMmQ1MDc1YSIsInN1YiI6IjY0ZjY3MGRlMTIxOTdlMDEzOGI1ZGFkOSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.jTFa6OHmAJBJOfHxI8XxCqaHxSbH1X0y4AKHlIsaI_Y'
        }
    };
    function rng(max) {
        return Math.floor(Math.random() * max);
    }
    function generateRandomMovies() {
        // make images invisible
        for (let j = 0; j < 2; j++) {
            document.getElementById("button" + String(j)).style.display = "none";
        }
        currentMovies = [];
        for (let i = 0; i < 2; i++) {
            var selecting = true;
            var selectedPage = String(rng(500) + 1);
            var movieIndex = rng(20);
            fetch('https://api.themoviedb.org/3/discover/movie?api_key=fc7fc7f1939ceafc80736bd502d5075a&include_adult=false&language=en-US&original_language=&page=' + selectedPage, options)
                .then(response => response.json())
                .then(response => {
                    console.log(response);
                    if (response["results"][movieIndex]["original_language"] == "en" && response["results"][movieIndex]["vote_average"] > 0) {
                        console.log("This is right.")
                        selecting = false;
                    } else {console.log("This ain't right.")}
                    fetchData = response;
                    console.log(fetchData);
                    console.log(fetchData["results"][movieIndex]);
                    var selectedMovie = fetchData["results"][movieIndex];
                    currentMovies.push(selectedMovie);
                    var moviePoster = document.getElementById("poster" + String(i));
                    moviePoster.src = "https://image.tmdb.org/t/p/w300/" + selectedMovie["poster_path"];
                    moviePoster.style.width = `${Math.ceil(currentWidth / 2)}px`;
                    moviePoster.style.height = `${currentHeight}px`;
                    document.getElementById("button" + String(i)).style.display = "inline-block";
                })
            .catch(err => console.error(err));
        }
        timerSet(true);
    }
    function timerSet(on) {
        console.log(timerVar);
        if (on) {
            currentTime = 10;
            timerVar = setInterval(() => {
                currentTime -= 0.1; // subtract 0.1 second
                currentTime = Math.floor(currentTime * 10) / 10; // rounding for floating point error fixing
                timerBox.innerHTML = String(currentTime);
                if (currentTime <= 0) {
                    console.log("Time's up!");
                    selectMovie(0, true);
                }
            }, 100); // Run every 100 milliseconds (0.1 seconds)
        } else {
            clearInterval(timerVar);
        }
    }
    function updateScore() {
        document.getElementById("player_score").innerHTML = "Your points: " + String(playerScore);
    }
    function selectMovie(int, timer) {
        console.log(timerVar);
        timerSet(false); // stop the interval
        console.log(int);
        currentMovieScores = [];
        for (movie of currentMovies) {
            currentMovieScores.push(movie["vote_average"]);
        }
        for (let i = 0; i < 2; i++) {
            document.getElementById("poster" + String(i)).style["filter"] = "blur(3px)";
            document.getElementById("results" + String(i)).innerHTML = currentMovieScores[i];
            document.getElementById("button" + String(i)).style.display = "none";
        }
        console.log(currentMovieScores);
        if (timer) {
            currentStreak = 0;
            console.log("Time's up! You lose 300 points for not guessing on time.");
            if (playerScore >= 300) {
                playerScore -= 300;
            } else {
                playerScore = 0;
            }
            updateScore();
            return;
        }
        if (currentMovieScores[int] > currentMovieScores[(int + 1) % 2]) {
            console.log("Congratulations! You chose the higher-rated movie.");
            currentStreak += 1;
            var newScore = Math.floor((1000 * (currentTime / 10)) * (1 + (currentStreak * 0.1)));
            playerScore += newScore;
        } else if (currentMovieScores[int] < currentMovieScores[(int + 1) % 2]) {
            console.log("Oh no! The movie you chose wasn't as highly rated.");
            currentStreak = 0;
            if (playerScore >= 250) {
                playerScore -= 250;
            } else {
                playerScore = 0;
            }
        } else {
            console.log("It looks like they're tied! Lucky--you get free points!");
            currentStreak += 1;
            playerScore += 500;
        }
        updateScore();
    }
    generateRandomMovies();
</script>