---
layout: post
title:  "Recursions"
date:   2018-12-22 21:27:09 -0800
categories: stackoverflow recursion
---

(This post is a slightly edited version of my Stack Overflow answer, here: 
https://stackoverflow.com/questions/717725/understanding-recursion/717839#717839,
to a question about "Understanding recursion".)


> I'm having major trouble understanding recursion at school. Whenever the professor is talking about it, I seem to get it but as soon as I try it on my own it completely blows my brains.

> I was trying to solve Towers of Hanoi all night and completely blew my mind. My textbook has only about 30 pages in recursion so it is not too useful. Does anyone know of books or resources that can help clarify this topic?



How do you empty a vase containing five flowers?

Answer: if the vase is not empty, you take out one flower
 and then you empty a vase containing four flowers.

How do you empty a vase containing four flowers?

Answer: if the vase is not empty, you take out one flower
 and then you empty a vase containing three flowers.

How do you empty a vase containing three flowers?

Answer: if the vase is not empty, you take out one flower
 and then you empty a vase containing two flowers.

How do you empty a vase containing two flowers?

Answer: if the vase is not empty, you take out one flower
 and then you empty a vase containing one flower.

How do you empty a vase containing one flower?

Answer: if the vase is not empty, you take out one flower
 and then you empty a vase containing no flowers.

How do you empty a vase containing no flowers?

Answer: if the vase is not empty, you take out one flower
  but the vase is empty so you're done.


That's repetitive. Let's generalize it:

How do you empty a vase containing *N* flowers?

Answer: if the vase is not empty, you take out one flower
  and then you empty a vase containing *N-1* flowers.

Hmm, can we see that in code?

<!-- language: c -->

    void emptyVase( int flowersInVase ) {
      if( flowersInVase > 0 ) {
       // take one flower and
        emptyVase( flowersInVase - 1 ) ;
    
      } else {
       // the vase is empty, nothing to do
      }
    }

Hmm, couldn't we have just done that in a for loop?

Why, yes, recursion can be replaced with iteration, but often recursion is more elegant.

Let's talk about trees. In computer science, a *tree* is a structure made up of *nodes*, where each node has some number of children that are also nodes, or null. A *binary tree* is a tree made of nodes that have exactly *two* children, typically called "left" and "right"; again the children can be nodes, or null. A *root* is a node that is not the child of any other node.

Imagine that a node, in addition to its children, has a value, a number, and imagine that we wish to sum all the values in some tree.

To sum value in any one node, we would add the value of node itself to the value of its left child, if any, and the value of its right child, if any. Now recall that the children, if they're not null, are also nodes. 

So to sum the left child, we would add the value of child node itself to the value of its left child, if any, and the value of its right child, if any.

So to sum the value of the left child's left child, we would add the value of child node itself to the value of its left child, if any, and the value of its right child, if any.

Perhaps you've anticipated where I'm going with this, and would like to see some code? OK:

<!-- language: c -->

    struct node {
      node* left;
      node* right;
      int value;
    } ;
    
    int sumNode( node* root ) {
      // if there is no tree, its sum is zero
      if( root == null ) {
        return 0 ;
    
      } else { // there is a tree
        return root->value + sumNode( root->left ) + sumNode( root->right ) ;
      }
    }


Notice that instead of explicitly testing the children to see if they're null or nodes, we just make the recursive function return zero for a null node.

So say we have a tree that looks like this (the numbers are values, the slashes point to children, and @ means the pointer points to null):

         5
        / \
       4   3
      /\   /\
     2  1 @  @
    /\  /\
    @@  @@

If we call sumNode on the root (the node with value 5), we will return:

<!-- language: c -->

    return root->value + sumNode( root->left ) + sumNode( root->right ) ;
    return 5 + sumNode( node-with-value-4 ) + sumNode( node-with-value-3 ) ;

Let's expand that in place. Everywhere we see sumNode, we'll replace it with the expansion of the return statement: 

<!-- language: c -->

    sumNode( node-with-value-5);
    return root->value + sumNode( root->left ) + sumNode( root->right ) ;
    return 5 + sumNode( node-with-value-4 ) + sumNode( node-with-value-3 ) ;

    return 5 + 4 + sumNode( node-with-value-2 ) + sumNode( node-with-value-1 ) 
     + sumNode( node-with-value-3 ) ;  

    return 5 + 4 
     + 2 + sumNode(null ) + sumNode( null )
     + sumNode( node-with-value-1 ) 
     + sumNode( node-with-value-3 ) ;  
 
    return 5 + 4 
     + 2 + 0 + 0
     + sumNode( node-with-value-1 ) 
     + sumNode( node-with-value-3 ) ; 

    return 5 + 4 
     + 2 + 0 + 0
     + 1 + sumNode(null ) + sumNode( null )
     + sumNode( node-with-value-3 ) ; 

    return 5 + 4 
     + 2 + 0 + 0
     + 1 + 0 + 0
     + sumNode( node-with-value-3 ) ; 

    return 5 + 4 
     + 2 + 0 + 0
     + 1 + 0 + 0
     + 3 + sumNode(null ) + sumNode( null ) ; 

    return 5 + 4 
     + 2 + 0 + 0
     + 1 + 0 + 0
     + 3 + 0 + 0 ;

    return 5 + 4 
     + 2 + 0 + 0
     + 1 + 0 + 0
     + 3 ;

    return 5 + 4 
     + 2 + 0 + 0
     + 1 
     + 3  ;

    return 5 + 4 
     + 2 
     + 1 
     + 3  ;

    return 5 + 4 
     + 3
     + 3  ;

    return 5 + 7
     + 3  ;

    return 5 + 10 ;

    return 15 ;

Now see how we conquered a structure of arbitrary depth and "branchiness", by considering  it as the repeated application of a composite template? each time through our sumNode function, we dealt with only a single node, using a singe if/then branch, and two simple return statements that almost wrote themsleves, directly from our specification?
 
    How to sum a node:
     If a node is null 
       its sum is zero
     otherwise 
       its sum is its value 
       plus the sum of its left child node
       plus the sum of its right child node

*That's* the power of recursion.

---
The vase example above is an example of *tail recursion*. All that *tail recursion* means is that in the recursive function, if we recursed (that is, if we called the function again), that was the last thing we did.

The tree example was not tail recursive, because even though that last thing we did was to recurse the right child, before we did that we recursed the left child.

In fact, the order in which we called the children, and added the current node's value didn't matter at all, because addition is commutative.

Now let's look at an operation where order does matter. We'll use a binary tree of nodes, but this time the value held will be a character, not a number.

Our tree will have a special property, that for any node, its character comes *after* (in alphabetical order) the character held by its left child and *before* (in alphabetical order) the character held by its right child.

What we want to do is print the tree is alphabetical order. That's easy to do, given the tree special property. We just print the left child, then the node's character, then right child.

We don't just want to print willy-nilly, so we'll pass our function something to print on. This will be an object with a print( char ) function; we don't need to worry about how it works, just that when print is called, it'll print something, somewhere.

Let's see that in code:

<!-- language: c -->

    struct node {
      node* left;
      node* right;
      char value;
    } ;

    // don't worry about this code
    class Printer {
      private ostream& out;
      Printer( ostream& o ) :out(o) {}
      void print( char c ) { out << c; }
    }

    // worry about this code
    int printNode( node* root, Printer& printer ) {
      // if there is no tree, do nothing
      if( root == null ) {
        return ;
    
      } else { // there is a tree
        printNode( root->left, printer );
        printer.print( value );
        printNode( root->right, printer );
    }

    Printer printer( std::cout ) ;
    node* root = makeTree() ; // this function returns a tree, somehow
    printNode( root, printer );

In addition to the order of operations now mattering, this example illustrates that we can pass things into a recursive function. The only thing we have to do is make sure that on each recursive call, we continue to pass it along. We passed in a node pointer and a printer to the function, and on each recursive call, we passed them "down".

Now if our tree looks like this:

             k
            / \
           h   n
          /\   /\
         a  j @  @
        /\ /\
        @@ i@
           /\
           @@
    
What will we print?
    
    From k, we go left to
      h, where we go left to
        a, where we go left to 
          null, where we do nothing and so
        we return to a, where we print 'a' and then go right to
          null, where we do nothing and so
        we return to a and are done, so
      we return to h, where we print 'h' and then go right to
        j, where we go left to
          i, where we go left to 
            null, where we do nothing and so
          we return to i, where we print 'i' and then go right to
            null, where we do nothing and so
          we return to i and are done, so
        we return to j, where we print 'j' and then go right to
          null, where we do nothing and so
        we return to j and are done, so
      we return to h and are done, so
    we return to k, where we print 'k' and then go right to
      n where we go left to 
        null, where we do nothing and so
      we return to n, where we print 'n' and then go right to
        null, where we do nothing and so
      we return to n and are done, so 
    we return to k and are done, so we return to the caller


So if we just look at the lines were we printed:

        we return to a, where we print 'a' and then go right to
      we return to h, where we print 'h' and then go right to
          we return to i, where we print 'i' and then go right to
        we return to j, where we print 'j' and then go right to
    we return to k, where we print 'k' and then go right to
      we return to n, where we print 'n' and then go right to

We see we printed "ahijkn", which is indeed in alphabetical order.

We manage to print an entire tree, in alphabetical order, just by knowing how to print a single node in alphabetical order. Which was just (because our tree had the special property of ordering values to the left of alphabetically later values) knowing to print the left child before printing the node's value, and tto print the right child after  printing the node's value.

And *that's* the power of recursion: being able to do whole things by knowing only how to do a part of the whole (and knowing when to stop recursing).


Recalling that in most languages, operator \|\| ("or") short-circuits when its first operand is true, the general recursive function is:

<!-- language: c -->

    void recurse() { doWeStop() || recurse(); } 


You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
