# 3D DICE ROLLER

My goal here is to make a truly satisfying dice roller; One that's the next best thing to rolling real dice. This library animates polyhedrons with numbers on each face that are visible while rolling, and includes a sound effect. Supports d4, d6, d8, d10, d12, d20, and d100 plus modifiers. Can be used to create stand-alone dice roller apps, or to integrate dice rolling into your javascript-based game. Visit https://sarahrosannabusch.ca/#diceRoller to see a demo of how it works.

# Usage

Clone this repo and open index.html in a web browser. (Note that the index.html, main.js, and styles.css files included here are intended as a demo and should be replaced with your own application.)

## dice.js

Create a new dice_box and pass it the dom element you want to contain it. (The dice box canvas will fill to the size of the container element.)

```javascript
    var box = new DICE.dice_box(elem);
```

Set the dice to be rolled by passing a string in the format "1d4+2d6+3".

```javascript
    box.setDice(string);
```

Call start_throw() to roll the current set of dice.

```javascript
    box.start_throw();
```

Or bind throw to a swipe event on the element passed into bind_swipe()

```javascript
    box.bind_swipe(elem);
```

Optional callback functions can be passed into both start_throw() and bind_swipe().

```javascript
    box.start_throw(before_roll, after_roll);
    box.bind_swipe(elem, before_roll, after_roll);

    // @brief callback function called when dice roll event starts
    // @param notation indicates which dice are going to roll
    // @return null for random result || array of desired results
    function before_roll(notation) {
        console.log('before_roll notation: ' + JSON.stringify(notation));
        //do some stuff before roll starts        
        return null;
    }

    // @brief callback function called once dice stop moving
    // @param notation now includes results
    function after_roll(notation) {
        console.log('after_roll notation: ' + JSON.stringify(notation));
        //do something with the results
    }
```

### Rolling to a predetermined result

If the outcome is decided elsewhere (a server, a game engine, a log replay) and the dice should only animate it, call animate_results(). No swipe or other input is needed; the result is fixed before the dice are thrown, and after_roll is called once they stop moving.

```javascript
    var started = box.animate_results(
        [{ type: 'd20', value: 17 }, { type: 'd6', value: 3 }],
        function(notation) {
            console.log(notation.resultString); // "17 3 = 20"
        }
    );
```

- Returns `true` when the animation started, `false` if dice are already rolling (the callback is not called in that case).
- Throws if the array is empty, a type is unknown, or a value isn't on that die. Valid values: d4, d6, d8, d12, d20: 1 to max; d9: 0-9; d10: 1-10; d100: 0, 10 ... 90.
- d100 is the tens die only. For a percentile roll of 47, pass `{ type: 'd100', value: 40 }` and `{ type: 'd9', value: 7 }`.
- The outcome is simulated up front (about 90 ms of main-thread time for 10 dice), then the same throw is replayed with the faces relabelled so the pre-computed result lands face up. The dice still tumble and collide normally.
- Browsers can block the roll sound when no user gesture has happened yet; the roll still plays, silently.

The same thing can be done with the existing hooks: return the results from `before_roll`, or put them after an `@` in the notation passed to setDice(), ex: `"1d20+1d6@17 3"`.

Open `test-forced-results.html?auto` in a browser to check every value of every die type; it should end with `"problems": []`.

Dice notation object structured as follows:

```javascript
    {
        "set":["d100","d10","d4","d6","d8","d12","d20"],
        "constant":0,
        "result":[10,9,1,5,2,8,18],
        "resultTotal":53,
        "resultString":"10 9 1 5 2 8 18 = 53",
        "error":false
    }
```


# CREDITS

This project is derived from the work of Anton Natarov (aka Teal) found at http://www.teall.info/2014/01/online-3d-dice-roller.html and uses [three.js](https://github.com/mrdoob/three.js/) and [canon.js](https://github.com/schteppe/cannon.js) for the geometry and physics.

Sound Effect courtesy of http://commons.nicovideo.jp/material/nc93322 and https://github.com/chukwumaijem/roll-a-die.


