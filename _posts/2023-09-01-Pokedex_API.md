---
title: Pokédex API
layout: default
description: Using JavaScript, HTML and a free API to make a Pokémon database.
comments: true
hide: true
courses: { csa: {week: 2} }
type: hacks
---

# Pokédex API

Find your favorite Pokémon in the Pokédex!

Input a Pokémon: <input id="search_box" type="text" width="100"> <button id="search_button" onclick="fetchData()">Search</button>

<div id="loading" style="display:none;"></div>
<div id="big_container" style="display:none;">
    <div id="base_data" class="info_container">
        <div id="images" class="info_container">
            <div id="image_n" class="poke_image">
                <!--NORMAL IMAGE HERE-->
            </div>
            <div id="image_s" class="poke_image">
                <!--SHINY IMAGE HERE-->
            </div>
        </div>
        <div id="basic_info" style="width:340px;border: 2px solid white; margin: 5px; line-height: 0.8; text-align: center;">
            <h3 id="poke_name_header"><!--POKEMON NAME HERE--></h3>
            <h4 id="species_box"></h4>
            <div id="typing_box" style="display: flex; justify-content:center;"></div>
            <div id="evolution_box"></div>
            <div id="pokedex_entry_box" style="margin: 5px; line-height: 1;">
                <!--POKEDEX ENTRY HERE-->
            </div>
            <button id="new_entry_button" onclick="generateEntry()" style="margin-bottom: 5px;">Generate New Entry</button>
        </div>
    </div>
    <div id="stats_data" class="info_container" width="600" style="display:block;">
        <h4 id="stats_header" style="text-align: center;">BASE STATS</h4>
        <div id="base_stats_bars" style="display: block; margin: 5px;">
            <div id="hp" class="stat_box"></div>
            <div id="attack" class="stat_box"></div>
            <div id="defense" class="stat_box"></div>
            <div id="special-attack" class="stat_box"></div>
            <div id="special-defense" class="stat_box"></div>
            <div id="speed" class="stat_box"></div>
        </div>
    </div>
</div>

<style>
    #big_container {
        width: 600px;
        border: 2px solid white;
    }

    .info_container {
        display: flex;
        justify-content: space-around;
        border: 2px solid white;
        margin: 5px;
    }

    .poke_image {
        margin: 5px;
        width: 100px;
        text-align: center;
    }

    .type {
        border: 1px solid #333;
        padding: 2px;
        margin: 2px;
        border-radius: 5px;
        color: #fff;
        font-size: 14px;
        text-align: center;
        text-shadow: 2px 2px 2px rgba(0, 0, 0, 0.8);
    }

    .stat_box {
        text-align: center;
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 1);
        border: 1px solid black;
        white-space: nowrap;
    }
</style>

<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

<script>
    // disable flowers
    flowerDisable = true;

    // pokemon name input
    const pokeSearch = document.getElementById("search_box");
    pokeSearch.addEventListener("keydown", enterHandler);

    // colors for typings
    const typeColors = {
        "normal": "#A8A77A",
        "fire": "#EE8130",
        "water": "#6390F0",
        "electric": "#F7D02C",
        "grass": "#7AC74C",
        "ice": "#96D9D6",
        "fighting": "#C22E28",
        "poison": "#A33EA1",
        "ground": "#E2BF65",
        "flying": "#A98FF3",
        "psychic": "#F95587",
        "bug": "#A6B91A",
        "rock": "#B6A136",
        "ghost": "#735797",
        "steel": "#B7B7CE",
        "dragon": "#6F35FC",
        "dark": "#705746",
        "fairy": "#D685AD"
    };

    const statConversion = {
        "hp": "HP",
        "attack":"Att",
        "defense":"Def",
        "special-attack":"SpA",
        "special-defense":"SpD",
        "speed":"Spe"
    };

    // establishing global variables
    const loadingBox = document.getElementById("loading");
    var movesArray = [];
    var defaultData = [];
    var speciesData = [];
    var evolutionData = [];

    // handle presses to enter key
    function enterHandler(event) {
        if (event.keyCode === 13) {
            fetchData(); // if enter is pressed in input button, the fetchData runs
        }
    }

    // USING POKEAPI
    // function to fetch data based on user input
    function fetchData() {
        // don't show big container yet
        // document.getElementById("big_container").style.display = "none";
        //jQuery
        $("#big_container").hide();
        loadingBox.style.display = "block";
        loadingBox.innerHTML = "Loading...";

        // prepare fetch options
        var url = "https://pokeapi.co/api/v2/pokemon/" + pokeSearch.value.toLowerCase();
        var options = {
            method: 'GET',
            mode: 'cors',
            cache: 'default',
            credentials: 'omit',
            headers: {
                'Content-Type': 'application/json'
            },
        };

        // fetch the API
        fetch(url, options)
            .then(response => {
                // check for response errors
                if (response.status !== 200) {
                    var errorMsg = 'Database response error: ' + response.status;
                    console.log(errorMsg);
                    loadingBox.innerHTML = "Your Pokémon couldn't be found! Make sure you've spelled its name right.";
                }
                // valid response will have JSON data
                response.json().then(data => {
                    console.log(data);
                    defaultData = data;
                    fetch(defaultData["species"]["url"], options).then(response => {
                        response.json().then(data => {
                            console.log(data);
                            speciesData = data;
                            fetch(speciesData["evolution_chain"]["url"], options).then(response => {
                                response.json().then(data => {
                                    console.log(data);
                                    evolutionData = data;

                                    // image boxes
                                    var pokeImgN = document.createElement("img"); // normal image
                                    var pokeImgS = document.createElement("img"); // shiny image
                                    pokeImgN.src = defaultData["sprites"]["front_default"]; // normal image source
                                    pokeImgS.src = defaultData["sprites"]["front_shiny"]; // shiny image source
                                    document.getElementById("image_n").innerHTML = "<figcaption>Normal Sprite</figcaption>";
                                    document.getElementById("image_s").innerHTML = "<figcaption>Shiny Sprite</figcaption>";
                                    document.getElementById("image_n").appendChild(pokeImgN);
                                    document.getElementById("image_s").appendChild(pokeImgS);

                                    // basic data box
                                    var pokeName = defaultData["name"]; // lowercase name of pokemon
                                    var pokeId = defaultData["id"]; // pokedex id number
                                    for (entry of speciesData["genera"]) {
                                        if (entry["language"]["name"] == "en") {
                                            var pokeSpecies = entry["genus"];
                                            break;
                                        }
                                    }
                                    var pokeTypes = []; // types data
                                    for (type of defaultData["types"]) {
                                        pokeTypes.push(type["type"]["name"]);
                                    }
                                    document.getElementById("typing_box").innerHTML = "";
                                    for (type of pokeTypes) {
                                        var typeIcon = document.createElement("div");
                                        typeIcon.setAttribute("class", "type");
                                        typeIcon.style.backgroundColor = typeColors[type];
                                        typeIcon.innerHTML = type.charAt(0).toUpperCase() + type.slice(1);
                                        document.getElementById("typing_box").appendChild(typeIcon);
                                    }
                                    //document.getElementById("poke_name_header").innerHTML = pokeName.toUpperCase() + " (#" + pokeId.toString() + ")";
                                    // jQuery
                                    $("#poke_name_header").html(`${pokeName.toUpperCase()} (#${pokeId})`);
                                    document.getElementById("species_box").innerHTML = pokeSpecies;
                                    if (speciesData["evolves_from_species"]) {
                                        var evolvesFrom = "The evolved form of " + speciesData["evolves_from_species"]["name"].charAt(0).toUpperCase() + speciesData["evolves_from_species"]["name"].slice(1) + ".";
                                    } else {
                                        var evolvesFrom = "Unevolved Pokémon.";
                                    }
                                    document.getElementById("evolution_box").innerHTML = evolvesFrom;
                                    generateEntry();

                                    // stats box
                                    for (stat of defaultData["stats"]) {
                                        var statContainer = document.getElementById(stat["stat"]["name"]);
                                        // jQuery
                                        $(statContainer).css("width", `${stat["base_stat"] * 2}px`);
                                        statContainer.style.backgroundColor = valueToColor(stat["base_stat"]);
                                        statContainer.innerHTML = statConversion[stat["stat"]["name"]] + ": " + String(stat["base_stat"]);
                                    }
                                    loadingBox.style.display = "none";
                                    loadingBox.innerHTML = "";
                                    // document.getElementById("big_container").style.display = "block";
                                    $("#big_container").show();
                                })
                            })
                        })
                    })
                })
            })
            .catch(err => {
                console.error(err);
                loadingBox.innerHTML = "Your Pokémon couldn't be found! Make sure you've spelled its name right.";
            });
    }

    function generateEntry() {
        var selecting = true;
        while (selecting) {
            var chosenEntry = speciesData["flavor_text_entries"][Math.floor(Math.random() * speciesData["flavor_text_entries"].length)];
            if (chosenEntry["language"]["name"] == "en") {
                selecting = false;
            }
        }
        var entryText = chosenEntry["flavor_text"].replace(/\n/g, ' ');
        console.log(entryText);
        document.getElementById("pokedex_entry_box").innerHTML = entryText;
    }
    // fetchData();

    function valueToColor(value) {
        // Map the value to a hue between 0 (green) and 120 (red) degrees
        const hue = (value / 140) * 120;
        
        // Convert HSV to RGB
        const saturation = 100; // You can adjust this value as needed
        const lightness = 50;  // You can adjust this value as needed

        const c = (1 - Math.abs((2 * lightness) - 100) / 100) * saturation / 100;
        const x = c * (1 - Math.abs((hue / 60) % 2 - 1));
        const m = lightness / 100 - c / 2;

        let r, g, b;
        if (0 <= hue && hue < 60) {
            r = c;
            g = x;
            b = 0;
        } else if (60 <= hue && hue < 120) {
            r = x;
            g = c;
            b = 0;
        } else {
            r = 0;
            g = x;
            b = c;
        }

        r = Math.round((r + m) * 255);
        g = Math.round((g + m) * 255);
        b = Math.round((b + m) * 255);

        return `rgb(${r}, ${g}, ${b})`;
    }
</script>