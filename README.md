# Synopsis
Final Project for Javascript Class - Southwest Tech

This is my final project for my Javascript Class at Southwest Tech.  I converted the W3Schools Game Instruction code to be something that my child would enjoy playing with.  The primary changes I made were to bind the movement options to the direction keys, and adjust the movement to be vertically focused instead of horizontal. 

Credited links:

[W3Schools Game](https://www.w3schools.com/graphics/game_intro.asp), 

[Freepik Icon For Rocketship](https://www.flaticon.com/), 

[Giphy Space Background](https://giphy.com/gifs/space-galaxy-ygAaR0n5RsyAM/)

# Major Code Revision - Directional Key Binding

To bind the direction keys to the Up/Down/Left/Right buttons, I used the following code:

This required the standard switch/case function code found in a variety of places online as well as the creation of two functions that listened for when the keys were down and when they were up to stop the movement. 

```
document.addEventListener("keydown", moveSomething, false);
document.addEventListener("keyup", stopSomething, false);

            function moveSomething(e) {
                switch (e.keyCode) {
                    case 13:
                        restartGame();
                        break;
                    case 37:
                        moveleft();
                        break;
                    case 38:
                        moveup();
                        break;
                    case 39:
                        moveright();
                        break;
                    case 40:
                        movedown();
                        break;
                }

            }

            function stopSomething(e) {
                if (e.keyCode >= 37 && e.keyCode <= 40) {
                    myGamePiece.speedX = 0;
                    myGamePiece.speedY = 0;
                    return false;
                }
            }
```
            
# Major Code Revision - Convert to Vertical Movement 

Convering the movement from horizontal to vertical was the most difficult part of this process.  The movement was somewhat simple in changing myObstacles to a y focus, but adjusting the myObstacles to show up in the right place in a vertical orientation was the most difficult.

```
function updateGameArea() {
                var x, y, min, max, height, gap;
                for (i = 0; i < myObstacles.length; i += 1) {
                    if (myGamePiece.crashWith(myObstacles[i])) {
                        myGameArea.stop();
                        document.getElementById("myfilter").style.display = "block";
                        document.getElementById("myrestartbutton").style.display = "block";
                        return;
                    }
                }
                if (myGameArea.pause == false) {
                    myGameArea.clear();
                    myGameArea.frameNo += 1;
                    myscore.score += 1;
                    if (myGameArea.frameNo == 1 || everyinterval(200)) {
                     ----->   x = myGameArea.canvas.width;
                        y = myGameArea.canvas.height - 400;
                        min = 20;
                        max = 580;
                        width = Math.floor(Math.random() * (max - min + 1) + min);
                        min = 60;
                        max = 100;
                        gap = Math.floor(Math.random() * (max - min + 1) + min);
                        myObstacles.push(new component(width, 10, "yellow", 0, y));
                        myObstacles.push(new component(x - width - gap, 10, "yellow", width + gap, y));<------
                    }
                    for (i = 0; i < myObstacles.length; i += 1) {
                    ---->    myObstacles[i].y += 1;<-----
                        myObstacles[i].update();
                    }
                    myscore.text = "SCORE: " + myscore.score;
                    myscore.update();
                    myGamePiece.x += myGamePiece.speedX;
                    myGamePiece.y += myGamePiece.speedY;
                    myGamePiece.update();
```

# Major Code Revision - Add Graphics

Updating the graphics of the game were some of the quickest fixes.  

First, the background update was a simple CSS change:

```
canvas {
                border:1px solid #d3d3d3;
                ----->background-image: url("space.gif");<-----
            }
```
            
Second, changing the red square to a rocketship occurred within the startGame function:

```
function startGame() {
                myGameArea = new gamearea();
                ----->myGamePiece = new component(30, 30, "rocket.png", 275, 300, "image");<------
                myscore = new component("15px", "Consolas", "white", 20, 25, "text");
                myGameArea.start();
            }
```

# Major Code Revision - Putting Boundaries on Game Area

I was asked to add boundary code to the game area to stop the rocketship from moving outside the boundaries of the canvas.  At first, I was able to use the [hit the bottom](https://www.w3schools.com/graphics/game_gravity.asp) code shown in the W3Schools game tutorial.  However, this not only stopped the game piece from going past the bottom, it also stopped the obstacles. At this point, I had to separate out the component code to create the game piece separately.  From there, I was able to pass the various hitDirection if statements to stop the rocketship from moving through the rest of the screen boundaries.

```
function pieceComponent(width, height, color, x, y, type) {

                this.type = type;
                if (type == "text") {
                    this.text = color;
                }
                if (type == "image") {
                    this.image = new Image();
                    this.image.src = color;
                }
                this.score = 0;
                this.width = width;
                this.height = height;
                this.speedX = 0;
                this.speedY = 0;
                this.x = x;
                this.y = y;
                this.update = function() {
                    ctx = myGameArea.context;
                    if (this.type == "text") {
                        ctx.font = this.width + " " + this.height;
                        ctx.fillStyle = color;
                        ctx.fillText(this.text, this.x, this.y);
                    } else if (type == "image") {
                        ctx.drawImage(this.image,
                            this.x,
                            this.y,
                            this.width, this.height);
                    } else {
                        ctx.fillStyle = color;
                        ctx.fillRect(this.x, this.y, this.width, this.height);
                    }
                    this.hitBottom();
                    this.hitRight();
                    this.hitLeft();
                    this.hitTop();
                }
                this.hitBottom = function() {
                    var rockbottom = myGameArea.canvas.height - this.height;
                        if (this.y > rockbottom) {
                            this.y = rockbottom;
                        }
                }
                 this.hitRight = function() {
                    var rockright = myGameArea.canvas.width - this.width;
                    if (this.x > rockright) {
                        this.x = rockright;
                    } 
                }
                this.hitLeft = function() {
                    var rockleft = 0;
                    if (this.x < rockleft) {
                        this.x = rockleft;
                    } 
                }
                this.hitTop = function() {
                    var rockTop = 0;
                    if (this.y < rockTop) {
                        this.y = rockTop;
                    } 
                }
```
