PyIntervalTree
==============

A mutable, self-balancing interval tree. Queries may be by point, by range overlap, or by range envelopment.

Features
--------
  * Initialize blank or from an iterable of Intervals in O(n * log n).
  * Insertions
    * `tree[a:b] = value`
    * `tree.add(Interval(a, b, value))`
    * `tree.extend(list_of_interval_objs)`
  * Deletions
    * `tree.remove(interval)`             (raises `ValueError` if not present)
    * `tree.discard(interval)`            (quiet if not present)
    * `tree.remove_overlap(point)`
    * `tree.remove_overlap(begin, end)`   (removes all overlapping the range)
    * `tree.remove_envelop(begin, end)`   (removes all enveloped in the range)
  * Overlap queries:
    * `tree[point]`
    * `tree[begin, end]`
    * `tree.search(point)`
    * `tree.search(begin, end)`
  * Envelop queries:
    * `tree.search(begin, end, strict = True)`
  * Membership queries:
    * `interval_obj in tree`              (this is fastest, O(1))
    * `tree.overlaps(point)`
    * `tree.overlaps(begin, end)`
  * Sizing:
    * `len(tree)`
    * `tree.is_empty()`
    * `not tree`
    * `tree.begin()`
    * `tree.end()`
  * Iterable:
    * `for interval_obj in tree:`
    * `tree.items()`
  * Copy- and typecast-able:
    * `IntervalTree(tree)`    (`Interval` objects are same as those in tree)
    * `tree.copy()`           (`Interval` objects are shallow copies of those in tree)
    * `set(tree)`             (can later be fed into `IntervalTree()`)
    * `list(tree)`            (ditto)
  * Equal-able
  * Hashable
  * Pickle-friendly
  * Automatic AVL balancing
    
Examples
--------
* Getting started
* 
        from PyIntervalTree import Interval, IntervalTree
        t = IntervalTree()

* Adding intervals - you don't have to use strings!

        t[1:2] = "1-2"
        t[4:7] = "4-7"
        t[5:9] = "5-9"

* Query by point

        ivs = t[6]            # set([Interval(4, 7, '4-7'), Interval(5, 9, '5-9')])
        iv = sorted(ivs)[0]   # Interval(4, 7, '4-7')
  
* Accessing an `Interval` object

        iv.begin  # 4
        iv.end    # 7
        iv.data   # "4-7"
  
* Query by range

  Note that ranges are inclusive of the lower limit, but non-inclusive of the upper limit. So:

        t[2:4]    # set([])

  But:

        t[1:5]    # set([Interval(1, 2, '1-2'), Interval(4, 7, '4-7')])

* Constructing from lists of `Interval`s

  We could have made the same tree this way:

        ivs = [ [1,2], [4,7], [5,9] ]
        ivs = map( lambda begin,end: Interval(begin, end, "%d-%d" % (begin,end), 
                   *zip(*ivs) )
  
        t = IntervalTree(ivs)

* Removing intervals

        t.remove( Interval(1, 2, "1-2") )
        list(t)     # [Interval(4, 7, '4-7'), Interval(5, 9, '5-9')]
        
        t.remove( Interval(500, 1000, "Doesn't exist") # raises ValueError
        t.discard(Interval(500, 1000, "Doesn't exist") # quietly does nothing
        
        t.remove_overlap(5)   
        list(t)     # []

  We could also empty a tree by removing all intervals, from the lowest bound to the highest bound of the `IntervalTree`:
  
        t.remove_overlap(t.begin(), t.end())

Based on
--------
  * Eternally Confuzzled's [AVL tree](http://www.eternallyconfuzzled.com/tuts/datastructures/jsw_tut_avl.aspx)
  * Wikipedia's [Interval Tree](http://en.wikipedia.org/wiki/Interval_tree)
  * Heavily modified from Tyler Kahn's [Interval Tree implementation in Python](http://forrst.com/posts/Interval_Tree_implementation_in_python-e0K)