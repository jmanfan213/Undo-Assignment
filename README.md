# HTML5 Canvas Drawing App — Undo/Redo Assignment

An extension of William Malone's HTML5 Canvas drawing app that adds stroke-based undo, redo, and an on-page command history.

## Launch instructions

1. Download or extract the project folder.
2. Keep `html5-canvas-drawing-app.html`, `html5-canvas-drawing-app.js`, and the `images` folder together in their existing layout.
3. Open [html5-canvas-drawing-app.html](html5-canvas-drawing-app.html) in **Google Chrome** or **Mozilla Firefox**. You can right-click the file and choose **Open with** to select the browser.

No installation, build step, or local web server is required. Supported browsers for this assignment are Chrome and Firefox, using a desktop mouse.

## Using the app

- Choose a color on the left and a tool or brush size on the right.
- Hold the left mouse button and drag inside the drawing area to draw a stroke.
- Click **Undo** to hide the most recent active stroke, or **Redo** to restore an undone stroke.
- Drawing a new stroke after undoing discards the remaining redo history.
- The **History** list displays recorded strokes and marks the current command. Scroll the list to view longer histories.

The drawing and history are held in memory; refreshing or closing the page resets them.

## Changes from the original

### Command storage and undo/redo

- Added a `Command` class that stores a stroke's inclusive `firstIndex` and `lastIndex` in the original parallel point arrays (`clickX`, `clickY`, `clickColor`, `clickTool`, `clickSize`, and `clickDrag`).
- Added a `CommandStack` class with a `stack` array and a `currentIndex` pointer. A pointer of `-1` represents no active commands, allowing the first stroke to be undone.
- Added `addCommand()` to append completed strokes and remove commands after the pointer when drawing resumes after an undo. The underlying point arrays are retained.
- Added `undo()` and `redo()` to move the pointer within its bounds, refresh the history display, and redraw the canvas.
- Exposed `undo`, `redo`, and `redraw` through the `drawingApp` public interface alongside `init`.

### Drawing and event handling

- Changed `redraw()` from iterating over every stored point to iterating over the commands through `currentIndex`, then drawing each command's point range using the original stroke-rendering code.
- Included the unfinished stroke while `paint` is true so drawing remains visible during dragging, before the command is recorded on release.
- Added `currFirstIndex` and `currLastIndex` to record stroke boundaries. The first index is captured before adding the new stroke's points; the last index is captured on release.
- Added `mouseInsideCanvas` and `trackStroke` checks. A mouse movement inside the drawing area with the left button held qualifies the gesture for history, preventing ordinary tool-selection clicks from becoming commands.
- Retained the original brush tools, colors, sizes, clipping, textures, and parallel-array storage. Also reformatted the JavaScript and added explanatory comments and command-stack console logging.

### HTML and history display

- Added Undo and Redo buttons that call the new public functions.
- Added `updateCommandHistory(forceAutoScroll)` to rebuild an ordered list from the command stack after a recorded stroke, undo, or redo.
- Highlighted the current command in bold with a `<- current` label; all other entries are gray. Undone commands remain listed until a new stroke replaces that branch of history.
- Added a bordered history box with a maximum height of 200 pixels and vertical scrolling.
- Added a scroll-to-bottom request for new strokes. In the current implementation, this runs before the list is rebuilt, so it may not reach the newest entry.

## Current behavior notes

The history qualification check runs during mouse movement and uses `e.buttons`. A click without movement does not create a recorded stroke, and the inherited touch handlers do not currently qualify strokes for the new history tracking. A user-facing Clear function has not been added.

## Original project

Original drawing app by William Malone. See the [original tutorial](http://wmalone.com/draw) and the included [LICENSE](LICENSE). Original source copyright and license notices remain in the JavaScript files.
