Day 2:

A lot of back and forth went into the right level opf abstraction. We knew we had a geometric board layer, and then pieces. We decided we need a layer inbetween "traversals", that builds possible starting and ending squares. These then form the basis for moves, which when combined with a concrete piece gives us an actual move. The nice thing about this is it makes giving move rules a natural, clean and conscise look, tells us we've done something right with abstraction layers.

[give exampe of move rule]

Then we had to wrangle out a sense of direction so we could define pawn rules. This proved tricking, because we cannot (yet) use arithmetic logic in the reasoner engine. However, this made me think more deeply about what I wanted. When I play chess, I don't count how many squares away a piece is from the board use that to know direction. I just have an innate sense of direction from the board (which was defined at the start of the game, and is not determined form where a piece *is*, only where it *starts*). We have this information from the role played between verticle adjacencies.

[show pawn rules]   

This is a valuable lesson: write logic close to the way you, as a rational animal, can infer things. Avoid using a more "programming" way of doing it. The result is a reasoner that acts more naturally, and perhaps with fewer explicit inferences from yourself.

We found a potentially great bit of abstraction:



This means to make a traversal transitive (like rook moves), we just make it a subtype of trans-traversal. The issue is that the reasoner will have to know that $trav is the type of traversal it's looking for. But since that's defined in the conditional, how will it know that? This demonstrates a limit of abstraction the reasoner has. Possibly we can sneak it in with attributes, I will have to think on that.

We can also amend transitivity to take account of pieces blocking:

[code of blocking-transitivity]

We just need to add a rule for pieces check there isn't a piece it's landing of the same colour. But we now gurantee it hasn't gone past another pice, whatever color.

Tomorrow we will launch with the code we have, and see if the transitivity rule breaks

