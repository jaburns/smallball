## Smallball

A tiny interactive physics simulation in a 350 byte HTML file.

Click the mouse to provide a downward impulse to the ball.

### [View it live](https://jaburns.github.io/smallball/)

### Final source
```html
<a onclick=k+=.03 href=#><b id=A><style onload="let y=j=k=0,a,f=(h,i)=>A.style[h]=238*(1+i),x=setInterval(z=>f
('left',x)|f('top',y)&&(z=(x-=j)*x+(y+=k+=5e-4)*y)>1&&(x/=z=Math.sqrt(z),y/=z,a=.8*(k*y-j*x),z=k*x+j*y,j=a*x+z
*y,k=z*x-a*y),5)">b,a{position:absolute;border-radius:50%;height:500;width:500;background:#437}b{height:24;wid
th:24;background:tan
```

### Source with whitespace and comments

#### HTML
```html
<!-- The large circle is the p element, and the ball is the nested anchor element. -->
<!-- k is the y-velocity of the ball, so clicking knocks it downwards -->
<a onclick=k+=.03 href=#><b id=A>
```

#### CSS
```css
b,a {
    position: absolute;
    border-radius: 50%;
    height: 500;
    width: 500;
    background: #437
}
b {
    height: 24;
    width: 24;
    background: tan

// We won't bother closing the last block or even the <style> element, because we don't need to.
```

#### Javascript
```javascript

let y =    // x and y always hold the position of the ball. x is defined a bit later.

    j =    // j holds the negative x-velocity of the ball.
    k = 0, // k holds the positive y-velocity.

    // Storing one of the velocity components as a negative value saves a negative sign in
    // the collision math later.

    a, // We need this later as a scratch variable

    // This function, f, is used to move the ball element to the correct position in the document.
    // The coordinate system we use for the simulation has (0,0) at the center of the big circle,
    // and one unit of distance is equal to the radius of the large circle minus the radius of the
    // small circle.  This means that if the ball is at (1,0) it should be touching the right wall
    // of the outer circle, but not passing through it.
    //
    //   238 = 250 (large circle radius) - 12 (ball radius)
    //
    // Also, we can just access the ball element by the global variable 'A' because elements with
    // id attributes are accessible via global variables automatically.
    f = (h, i) => A.style[h] = 238*(1+i),

    // Now we define x, the ball's horizontal position, and initialize it to the result of the
    // setInterval we use to call the main loop. setInterval returns a non-zero integer whose
    // exact value depends on the browser implementation, but since the position is bounds checked
    // right away it doesn't really matter what the value is. Initializing x this way saves a few
    // bytes of declaration. Additionally, the body of the loop function iteself is a single
    // statement so we don't need curly braces.
<<<<<<< HEAD
    x = setInterval(_ =>
=======
    x = setInterval(z => 
>>>>>>> 350

      // Move the ball element to the simulated position (x,y). We're chaining these statements together
      // with bitwise OR here to give a value that will virtually always be truthy so that the next
      // clause in the && will evaluate. If the ball somehow ends up in the perfect dead center of the
      // circle the simulation will freeze.
      f('left',x) | f('top',y)

      // Add some gravity to y-velocity, and add the velocity vector components to the position
      // vector components. Also, we'll get the square distance of the ball's position from the
      // center in to the variable 'z' we defined as a function parameter.
      && (z = (x-=j)*x + (y+=k+=5e-4)*y)

      // Now we'll compare that last clause, which has the value of 'z' to 1 and execute the next
      // parenthesized block conditionally. If the distance, square or otherwise, is greater than
      // one, we're past the edge of the circle.
      > 1 && (
          // Get the actual distance from the center in to z and use this length to normalize the
          // position vector. Since, in our coordinate space, a unit of distance equals the large
          // circle radius minus the ball radius, this puts us nicely on the edge of the circle.
          x /= z = Math.sqrt(z),
          y /= z,

          // Here we're getting the projection of the velocity vector on to the collision normal.
          // The trick here is that the position of the ball is now a unit vector, and it's equal
          // to the collision normal. Because of this, to get the projection of the velocity on to
          // the normal we can just dot the velocity with the position. The .8 scales this normal
          // component down to account for energy lost in the bounce against the wall.
          a = .8*(k*y - j*x),

          // Now we take the tangential component of the velocity by dotting the velocity with the
          // 90-degree rotation of the normal. We do that by using -x for y and y for x in the dot
          // product here. Remember that j is actually negative x-vel so the signs are weird.
          z = k*x + j*y,

          // We can now reconstruct the new velocity x/y components from the fresh normal/tangent
          // components. These statements are equivalent to something like:
          //    velocityVector = a * normalVector + z * tangentVector;
          // Of course normalVector is just position, and tangentVector is just position with x and
          // y swapped and one of them negative.
          j = a*x + z*y,
          k = z*x - a*y
      )

  // Here we set the main loop to fire at 200 Hz so it looks smooth at any refresh rate without having
  // to do math with delta time.
  ,5)
```

### Thanks
 - [PawkaHub](https://github.com/PawkaHub) for elements being accessible via global variables, not needing to close the style tag, and putting all the JS in an onload attribute.
