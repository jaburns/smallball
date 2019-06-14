## Smallball

A tiny interactive physics simulation as an HTML file weighing under half a kilobyte.

### [View it live](https://jaburns.github.io/smallball/)

### Source with whitespace and comments

#### CSS
```css
body {
    margin:20px 0 0 20px
}
a {
    width: 500px;
    height: 500px;
    background: #444;
    border-radius: 50%;
    position: absolute;
    cursor: pointer
}
#x {
    height: 24px;
    width: 24px;
    background: #fff
}
```

#### HTML
```html
<!-- We'll make this thing out of two <a> elements because they have a short name -->
<a /><a id=x />
```

#### Javascript
```javascript
let D = document,

    x =  .3, // x and y always hold the position of the ball.
    y = -.7,

    j = 0, // j and k always hold the velocity.
    k = 0,

    z, a, // z and a are just scratch variables.

    // This function, f, is used to move the ball element to the correct position in the document.
    // The coordinate system we use for the simulation has (0,0) at the center of the big circle,
    // and one unit of distance is equal to the radius of the large circle minus the radius of the
    // small circle.  This means that if the ball is at (1,0) it should be touching the right wall
    // of the outer circle, but not passing through it.
    //
    //  Magic numbers:
    //   238 = 250 (outer circle radius)
    //       -  12 (inner circle radius)
    //   258 = 238 + 20 (document margin)
    //
    f = (h, i) => D.getElementById('x').style[h]=258+238*i+'px',

    // The g() function defined here is the main loop, and it's called at regular intervals.
    g = () =>
    {
        // Add some gravity to y-velocity, and add the velocity components to the position components.
        k += 5e-4;
        x += j;
        y += k;

        // Get the square distance of the ball's position from the center.
        z = x*x + y*y;

        // If the distance, square or otherwise, is greater than one, we're past the edge of the circle.
        // This x&&(a,b) construct is equivalent to if(x){a;b}.
        z>1&&(

            // Get the actual distance from the center in to z
            z = Math.sqrt(z),

            // Normalize the position vector. Since in our coordinate space a unit of distance equals
            // the large circle radius minus the ball radius, this puts us nicely on the edge of the
            // circle.
            x /= z,
            y /= z,

            // Here we're getting the projection of the velocity vector. The trick here is that
            // the position of the ball is now a unit vector, and it's equal to the collision normal!
            // Because of this, to get the projection of the velocity on the collision normal we can
            // just dot the velocity with the position. The negative at the front flips the normal
            // component of the velocity, which gives us our bounce, and the 0.8 makes it bounce with
            // a little less energy than it started with. That's what makes the bounce decay over time.
            a = -.8*(j*x + k*y),

            // Now we take the tangential component of the velocity by dotting the velocity with the
            // 90-degree rotation of the normal. We do that by using -x for y and y for x in the dot
            // product here.
            z = j*y - k*x,

            // We can now reconstruct the new velocity x/y components from the fresh normal/tangent
            // components. These statements are equivalent to something like:
            //    velocityVector = a * normalVector + z * tangentVector;
            // Remember that normalVector is just position, and tangentVector is just position with
            // x/y swapped and one of them negative.
            j = a*x + z*y,
            k = a*y - z*x
        );

        // Move the ball element to its newly simulated position.
        f('left', x);
        f('top', y)
    };

// Here we set the main loop to fire at 200 Hz so it looks smooth at any refresh rate without having
// to do math with delta time in requestAnimationFrame.
setInterval(g, 5);

// Knock the ball downwards a bit if the user clicks anywhere on the page.
D.onclick=()=>k+=.03

// That's it!
```
