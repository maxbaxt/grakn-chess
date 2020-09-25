 Day 4

"If I had more time, I would have written a shorter letter"

So wrote Decartes in a letter to a friend. I was reminded on this as I implemented the rest of the schema. I wrote the rest of the rules with the new tenary relation as discussed on day 3, and added rules for "potential moves" to "legal moves", which checked a potential move didn't try to land on a square with a piece of the same colour. (Blocking being done at transitivity).But now I had to try to build through inserting data. I limited it to a 4x4 board, but even this required a lot of statements. When I got to doing the diagonal adjacencies, I decided it was too much effort, I should get the reasoner to do it. This wasn't just for laziness sake though, it was to demonstrate that since a diagonal is a necersarry fact from the two other cross adjencies, it should be easy to work it out. The input nopw has to declare a diagonal direction, and how they relate to othe directions, and the reasoner does the rest:

```
    $fromWhite isa cross, has colour "white";
    $fromBlack isa cross, has colour "black";
    $left isa cross;
    $right isa cross;
    $nwDiag isa diagangle, has colour "white";
    $neDiag isa diagangle, has colour "white";
    $swDiag isa diagangle, has colour "black";
    $seDiag isa diagangle, has colour "black";

    (base-dir: $left, base-dir: $fromWhite, inferred-dir: $nwDiag) isa dir-creation;
    (base-dir: $left, base-dir: $fromBlack, inferred-dir: $swDiag) isa dir-creation;
    (base-dir: $right, base-dir: $fromWhite, inferred-dir: $neDiag) isa dir-creation;
    (base-dir: $right, base-dir: $fromBlack, inferred-dir: $seDiag) isa dir-creation;

    (start-square: $sa1, end-square: $sa2, adj-direction: $fromWhite) isa adjacency;
    (start-square: $sa2, end-square: $sa3, adj-direction: $fromWhite) isa adjacency;
    ## etc...
```



I then wonderd if I could half my effort again by decalring a mirror rule and saying "left" and "right" were mirrord. Then I would only need to insert left and from-white directions. This will work, but I'd already written the inserts explicitly, and wanted to get to testing.

I inserted some pieces on the 4x4 board, and asks it to give me the legal moves of each peice. After fixing some bugs in the code, it all worked fine. The only issue was the time taken: working out where a piece could move took 5-15 minutes.

It must be remembered the reasoner is doing a lot, sincd it is reconstriucting the entire board layout and adjency. To demonstrate this further, I want to give the reasoner some more alien geometry, and keep the move rules the same. This includes:

3-player chess
wrapped boards
3-D chess
triangle or hexagon based boards

It will be great if the reasoner can do this with only a change to the insert data, and not the rules coding itself.

