## Smallball

A tiny interactive physics simulation as an HTML file weighing under half a kilobyte.

### [View it live](https://jaburns.github.io/smallball/)

### Final source
```
<p onclick='k+=.03'><a id=A><script>let x=.3,y=-.7,j=0,k=0,z,a,f=(h,i)=>A.style[h]=238*
(1+i)+'px';setInterval(()=>{k+=5e-4;x+=j;y+=k;z=x*x+y*y;z>1&&(z=Math.sqrt(z),x/=z,y/=z,
a=-.8*(j*x+k*y),z=j*y-k*x,j=a*x+z*y,k=a*y-z*x);f('left',x);f('top',y)},5);</script>
<style>p,a{cursor:pointer;position:absolute;border-radius:50%;height:500px;width:500px;
background:#444}a{height:24px;width:24px;background:#fff
```

### Source with whitespace and comments

#### HTML
```html
<!-- The large circle is the p element, and the ball is the nested anchor element. -->
<!-- k is the y-velocity of the ball, so clicking knocks it downwards -->
<p onclick='k+=.03'><a id=A>
```

#### CSS
```css
p,a {
    cursor: pointer;
    position: absolute;
    border-radius: 50%;
    height: 500px;
    width: 500px;
    background: #444
}
a {
    height: 24px;
    width: 24px;
    background: #fff

// We won't bother closing the last block or even the <style> element, because we don't need to.
```

#### Javascript
```javascript
let x =  .3, // x and y always hold the position of the ball.
    y = -.7,

    j = 0, // j and k always hold the velocity.
    k = 0,

    z,a, // z and a are just scratch variables.

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
    
    f = (h, i) => A.style[h] = 238*(1+i) + 'px';

// Now we define the main loop, which is triggered at regular intervals.
setInterval(() =>
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

// Here we set the main loop to fire at 200 Hz so it looks smooth at any refresh rate without having
// to do math with delta time.
},5);
```
