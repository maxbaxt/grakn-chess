day 3:

Enough writing code dry, let's see what happens when we run this thing.

I run a grakn server and load in the schema, but commented out the controversial transitivity rule which we guessed would cause problems. Not surprisingly I hit a syntax error. But it only takes me a few minutes to fix these, demonstrating Grakn's intuitive syntax. This upgraded me to compilation errors in my code. This exposed a bug that is to be fixed in Grakn 2.0, and another previously unknown. Fortunately there were simple work-arounds, and the code commits without error.

OK then, time for something interesting. I uncomment the transitivity rule, reload the schema and prepare for fireworks. 

The code commits. Does it work? I run a test, and find that while the rule does comile, the reasoner never uses it because the unifier doesn't know it's what it wants, as described in the last entry. The rule slips past the compiler into a limbo.

I can easily get around this by writing a separate transitivity rule for each direction. It would only take a few more rules. But I really wanted to do it through abstraction, because it felt *natural*. Transitivity is a fundamental rule, it doesn't act different across different moves. I thought the type hieracrchy I had created was too all-encompassing, so I thought to split the abstractions of transtivity and non-transitivity and direction type separately. This would allow some composition and reduce some of the duplication. I thought of implementing direction as an attribute. When a traversal rule is written, it has to first make the traversal, and then assign the atribute. This can't be done in a single rule, but a neat trick allows it to do it in two:

"""
    Transitivity sub rule,
    when {
        (start-node: $a, end-node: $b) isa trans-traversal, has traversal-type $t;
        (start-node: $b, end-node: $c) isa trans-traversal, has traversal-type $t;
        $a != $c;
    }, then {
        (start-node: $a, end-node: $c) isa trans-traversal;
    };

    transitivity2 sub rule,
    when {
        (start-node: $a, end-node: $b) isa trans-traversal, has traversal-type $t;
        (start-node: $b, end-node: $c) isa trans-traversal, has traversal-type $t;
        $a != $c;
        $g (start-node: $a, end-node: $c) isa trans-traversal;
    }, then {
        $g has traversal-type $t;
    };
"""

So this works, but now I need to refactor the rest of the code:

```graql
vst-rule sub rule,
when {
    (adjacent: $a, adjacent: $b) isa adjacency, has orientation "vertical";
    $t (start-node: $a, end-node: $b) isa single-traversal;
}, then {
    $t has traversal-type "vertical";
};
```
Now I'd written it, I thoght this looked too verbose. That there was some way of constructiing the traversals from the adjacencies all at once. I wanted two things:
1. The attribute of direction to carry on from adjency to traversal.
2. The direction attribute to exist in a hierarchy that allowed for easy implementation of rules.

I spent a couple of two, with help from James, on how to implement this. My orifginal type-hierarchary was giving me 2., but not 1. The attribute implemention could give me 1., but creating it with a hierarchy of attributes was tricky. (You can make hierarchies of attributes, but doing it in the way I wanted was finicky)

James suggested an idea that didn't occur to me: Make adjacencies and traverserals a *tenary* relation between sqaures and a direction entitiy. I admit I thought first that creationg a tenary relationship here was unnecersarily complicated. I also couldn't see how direction could be an entity. But after tryiong to implement  the attribute solution, I tried James's idea, and this was the result:

    single-traversal-rule sub rule,
    when {
        (adjacent: $a, adjacent: $b, adj-direction: $dir) isa adjacency;
    }, then {
        (start-node: $a, end-node: $b, trav-direction: $dir) isa single-traversal;
    };
    
    trans-traversal-rule sub rule,
    when {
        (adjacent: $a, adjacent: $b, adj-direction: $dir) isa adjacency;
    }, then {
        (start-node: $a, end-node: $b, trav-direction: $dir) isa trans-traversal;
    };


    transitivity sub rule,
    when {
        (start-node: $a, end-node: $b, trav-direction: $dir) isa trans-traversal;
        (start-node: $b, end-node: $c, trav-direction: $dir) isa trans-traversal;
    }, then {
        (start-node: $a, end-node: $c, trav-direction: $dir) isa trans-traversal;
    };
This was a remarkable improvement, causing a huge simplication of the code. Not only is transitivity done in a single move, but 

What did we learn from this? Firstly, that we must think carefully how we choose to model the data we are trying to represent. Secondly, that type hierachies, while extremely powerful, shouldn't be extended beyond what is truly a hierarchy, or else they become brittle and force duplication. Much better is composition, which leads to the third lesson: tenary (and n-ary) relations alllow for encoding more information that exists *inherently* in a relationship, and by making that third element an entity, we can use that elsewhere in the code, which is ideal for composition.

I look forward now to finishing this implementation and extending it to allow for takes, checks and checkmates.