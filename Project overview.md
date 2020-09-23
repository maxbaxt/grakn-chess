Project overview:
Create a model of chess in Grakn. If given a chess puzzle in the style eg "white checkmates in 2 moves", the reasoner engine can work out the solution since it "knows" the rules of chess.

I chose this because I wanted a project that really tested the limits of the reasoner. The idea is not to expect the most efficeint chess puzzle solver, but to see how much can be understood by giving it merely the rules of the game, and chess puzzles without either programming in strageties explicitly or without any sort of genetic or learning algorithm, it will demonstrate the versatilty of the reasoner. If it fails to do this, it will teach me a lot about where the limits of the reasoner lie.

Day 1:
Modelling chess was harder than I initially anticipated. I had to step out of not only the OOP paradigm, but also procedural coding in general. 

My instint was therefore to use enitities as objects, rules as methods and relations as a sort of cousin of inheritance and composition. For example, pieces and sqaures are entities, the movements allowed as rules.
Unfortuantely, such a crude translation from OOP to Grakn causes headaches.

The issue comes from the fact that you think in a way of procedural programming, where there is a preset *beahviour* that *acts* leading to *state changes*. In "logical" or "reasoning" programming (for lack of a better term), there is no ananlougous movement or state change. Things simply *are*, and the work done is what was already true from the data given.

From a philsophical point of view, "procedural programming" enables *change* by allowing a *potential* to be *actualised*.  In "logical programming", there is only the acutal, things are actualised immediately from the initalised state of the data (but aren't nencersary known until queried). But if we want to know things due to their consequence of future moves in chess, we will have to make use of things that aren't *actualised*, in exist *in potentia*.

This is all to sayy, things must be looked at with a fresh and open mind rather than loosely translating old concepts to new ones.

Going back to the task at hand, I started with the entities piece, position and board. Each peice (eg rook) would be a sub type of piece, each piece would have a square, and the board would be a collection of these states. This was my mind in OOP mode. But this is wrong, entities don't contain each other, they relate to each other.

Instead, James helped me construct the idea of positon as a relation between a square (now the name of an entity) and a piece. This opened my eyes to unique advantage: if postion was an attribute of a piece, it was easy to find where a piece was, but not the other way around (ie given a square, what was on it?). Now we can immediately see our world from either a piece perspective or a board perspective, and we different have to code that in explicitly.

The next challenge was related to the piece-board dual. Did I need to bother encoding the geometry of the board? Or was the relation to the squares had to each other only a result of the rules the pieces happened to have? It might seem obvious that square A and B are next to each other, but if you imagine chess with only knights, "adjaceny" actually only exist in **L** shapes. And if you consider draughts, blacks square don't really exist, and daigonl adjaceny is the only adjacecy with meaning. So could I just give the squares as nodes, and adjacency  relative to pieces, and abstract away the geometry of the board completely?

I thought at first this was the way to go, but the need to include blocking by other pieces does necersitate the acknowledgement of the metrics (ie distance) of the board. Nonetheless, the idea of such abstraction of a model intrigues me, and I look forward to exploring it further in future projects.

I then got to rwriting the rules, and this led to the second lightbulb moment of the day. I wrote the rules of a fairy chess piece, a "lame rook", which can only move one peice at a time. Once that I had done that, I was able to every quickly extend this to a full rook, by making adjacecny transitive. The only caveat was splittluing adjacency into vertical and horizontal, thus preventing any bent movements. The next steps will be to add in blocking, and amend the rules for bishops.