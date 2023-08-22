---
toc: false
comments: true
layout: post
title: Pixel Art Canvas
description: Use it to create some cool pixel art and learn about hex codes!
type: hacks
---

<style>
    button {
        padding: 5px;
        background: white;
        color: black;
    }
    .pixel {
        background: #FFFFFF;
        border: none;
        padding: 0;
        margin: 0;
    }
    .canvas {
        display: block;
    }
</style>

<div id="size_inputs" margin="25px">
    Width: <input id="width_input" type=number min="1" max="64" placeholder="16" value="16"><br><br>
    Height: <input id="height_input" type=number min="1" max="64" placeholder="16" value="16"><br><br>
    <button id="submit_size" onclick="createCanvas()">CREATE CANVAS</button>
    <div id="input_error_box"></div>
</div>

<div id="canvas_controls" style="display:none;">
    <input id="color_input" type="color" value="#000000">
    <button id="undo_button" onclick="undoMove()">UNDO</button>
    <button id="reset_button" onclick="createCanvas()">RESET</button>
</div>

<table id="canvas" class="canvas" margin="0">
    <!--CONTENT FROM CODE HERE-->
</table>

<script>
    const sizeInputs = document.getElementById("size_inputs");
    const widthInput = document.getElementById("width_input");
    const heightInput = document.getElementById("height_input");
    const submitSize = document.getElementById("submit_size");
    const inputErrorBox = document.getElementById("input_error_box");
    const canvas = document.getElementById("canvas");
    const canvasControls = document.getElementById("canvas_controls");
    const colorInput = document.getElementById("color_input");
    const resetButton = document.getElementById("reset_button");
    const undoButton = document.getElementById("undo_button");
    var newPixel = ""; //to be replaced with html
    var pixId = 0; //to be edited throughout process; must be global for eventHandler
    var isMousePressed = false;
    var currentColor = "#000000";
    var colorHistory = []; //to be filled in with color records
    //color history formatting: [pixelID, previous color]

    function createCanvas() {
        var canvasWidth = Number(widthInput.value);
        var canvasHeight = Number(heightInput.value);
        if (1 > canvasWidth || canvasWidth > 64 || 1 > canvasHeight || canvasHeight > 64) {
            inputErrorBox.innerHTML = "The width and height of the canvas must be between 1 and 64!";
            return;
        }
        // inputErrorBox.innerHTML = "Congratulations! Normally, this would create a canvas with a width of " + String(canvasWidth) + " and a height of " + String(canvasHeight) + ".";
        sizeInputs.style["display"] = "none";
        canvasControls.style["display"] = "flex";
        buildCanvas(canvasWidth, canvasHeight);
    }

    function buildCanvas(width, height) {
        // reset canvas
        canvas.innerHTML = "";
        newPixel = "";
        pixId = 0;
        colorHistory = [];
        // determine pixel dimensions
        if (width > height) {
            var pixelDimensions = Math.floor(640 / width);
        } else {
            var pixelDimensions = Math.floor(640 / height);
        }
        // build the canvas
        for (let i = 0; i < height; i++) {
            var newRow = document.createElement("tr");
            for (let j = 0; j < width; j++) {
                newPixel = document.createElement("td");
                newPixel.setAttribute("class", "pixel");
                newPixel.setAttribute("id", "pixel" + String(pixId));
                newPixel.setAttribute("style", "width:" + String(pixelDimensions) + "px;height:" + String(pixelDimensions) + "px;");
                newPixel.setAttribute("onclick", "changeColor('pixel" + String(pixId) + "')");
                newPixel.addEventListener("mousedown", () => {isMousePressed = true;});
                newPixel.addEventListener("mouseup", () => {isMousePressed = false});
                newPixel.addEventListener("mouseleave", holdHandler);
                newPixel.addEventListener("mouseenter", holdHandler);
                newRow.appendChild(newPixel);
                pixId++;
                newPixel = "";
            }
            canvas.appendChild(newRow);
        }
    }

    function holdHandler(event) {
        if (isMousePressed) {
            changeColor(event.target.id);
        }
    }

    function changeColor(pixelID) {
        currentColor = colorInput.value;
        var changePixel = document.getElementById(pixelID);
        if (currentColor != changePixel.style["background"]) {
            colorHistory.push([pixelID, changePixel.style["background"]]);
            changePixel.style["background"] = currentColor;
        }
    }

    function undoMove() {
        if (colorHistory.length) {
            var previousMove = colorHistory.pop();
            var targetPixel = document.getElementById(previousMove[0]);
            targetPixel.style["background"] = previousMove[1];
        }
    }
</script>