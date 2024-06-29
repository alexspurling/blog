## Advent of Code 2023

With perfect timing for the beginning of Advent of Code 2023, [a post appeared on Hacker News](https://news.ycombinator.com/item?id=38489340) 
introducing a new programming language called [Onyx](https://onyxlang.io/). This language compiles to WebAssembly which means it can be called 
from an HTML / JS website or even a server-side WebAssembly runtime.

### A Framework for a Puzzle Solving

Since my Advent of Code solutions would be compiled to WebAssembly, it made sense to build a webpage with an interface around each solution.
Sticking with the *advent* theme, I generated an image of a "coding inspired advent calendar" with Midjourney with a numbered window linking
into each solution:

![Advent of code screenshot](AdventScreenshot1.png)

Clicking on a window brings up a description of the problem with links that call through to the compiled Onyx solution.

![Problem description screenshot](AdventScreenshot2.png)

### Onyx API

In Onyx, I exported two functions, one to return the description of a given problem, and another to return the solution.

![Describe function code screenshot](DescribeFunction.png)

![Solve function code screenshot](SolveFunction.png)

With this entry point, I delegate to an Onyx module for that day's solution.

### Papercuts

In hindsight it probably was not a great idea to choose a brand new programming language to try to implement the tricky algorithms required
by AoC. Often I would have an idea of how to solve the problem only to get the wrong solution. I would often spend hours debugging
with print statements only to find a bug in the compiler would mean my variables were in the wrong type. In fact, during the first half of
the month I raised roughly one bug per day in the project's Github of varying severity. In addition to actual bugs, the project lacked
documentation of certain features. Having said all that, I cannot praise Onyx's author Brenden Hansen enough for his support answering all
of my questions on Discord and making fixes to the issues I had with Onyx quickly.

### Visualisation

An additional reason to build a framework around my Advent of Code solutions was to experiment with visualisation for some of the problems. In
this example, the problem requires solving a maze using specific rules around when you can turn left or right.

<video width="100%" preload="auto" muted controls>
    <source src="Problem17Visualisation.mp4" type="video/mp4"/>
</video>

This works by allocating a chunk of memory in Onyx and exporting a pointer to it. Then in Javascript, I reference this chunk of memory as
the location for the image data in a canvas:

```javascript
const canvasData = wasmByteMemoryArray.slice(canvasRef.canvasPointer, canvasRef.canvasPointer + canvasRef.canvasSize);
const canvas = document.getElementById("solutioncanvas");
const ctx = canvas.getContext("2d");
const imageData = ctx.createImageData(canvas.width, canvas.height);
imageData.data.set(canvasData);
ctx.putImageData(imageData, 0, 0);
```

This means in order to draw on the canvas, you have to manually update the bytes representing the pixels one by one. There are no `drawLine`
or `drawText` functions available in Onyx; everything must be done by hand. I even implmented a basic text renderer so I could output
debugging information.

