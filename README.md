Per-Pixel-Collision-Detection Plugin
====================================

This plugin allows for per-pixel collision detection in the [ImpactJS](http://impactjs.com/) engine. It comes with some caveats. To help you understand the caveats, let me explain how this plugin works.

The algorithm for per-pixel collision detection is simple: get the overlapping pixels from two Entities, and if both pixels are not transparent (alpha !== 0), a collision has occurred.

In its basic form, the algorithm looks like this:

```javascript
for( var y = yMin; y < yMax; y++ ){
    for( var x = xMin; x < xMax; x++ ){
        if( a[ x - this.pos.x + ( y - this.pos.y ) * this.size.x ] &&
            b[ x - other.pos.x + ( y - other.pos.y ) * other.size.x ] ){
            return true;
        }
    }
}
```

Where xMin, xMax, yMin and yMax are the dimensions of the overlapping rectangle (if the Entity A overlaps Entity B in a tile engine, then the overlap will always be in a shape of a rectangle). Impact only has to find the dimensions of this rectangle, and if such a rectangle exists, and the test stops there. 

With a per-pixel algorithm, each point (or pixel) within the rectangle has to be tested for a collision. If the collision rectangle is small, say the two Entities only overlap by a 2x2 rectangle, then you only need to test up to 4 pixels. But if the Entities overlap by a 10x10 rectangle, then up to 100 pixels may need to be tested. I say up to because that's the worst case running time. If a collision is found on the 27th pixel of the rectangle, then the algorithm only had to test 27 times vs 100 times.

As you can see, 4 or 100 additional tests > 1 test to find the rectangle. Thus, per pixel is a costly algorithm - O(n^2). This is the first caveat. The second caveat is that to optimize the algorithm, I had to cache an alpha array for each Image. This'll effect your game in two ways: one, it's a larger memory footprint; and two, Impact's initial load time will go up, as the alpha arrays need to be created.