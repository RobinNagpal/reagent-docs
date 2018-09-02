---
title: Components 
style: contents-page
---
### RAtom
CLJS is immutable i.e. when we modify anything on the CLJS data structures,
we get a new object back.

Almost every application needs some way of producing/handling side effects.
CLJS/Clojure provides a very nice way of managing change of application state
by using atoms. More on [atoms](https://clojure.org/reference/atoms)

#### RAtom vs atom
If we look at source code of CLJS [atom](https://github.com/clojure/clojurescript/blob/r1.10.339-24-g0773689e/src/main/cljs/cljs/core.cljs#L4378),
it implements the following protocols: 
1. IAtom - Marker protocol indicating an atom.
2. IEquiv - Protocol for adding value comparison functionality to a type.
3. IDeref - Protocol for adding dereference functionality to a reference.
4. IMeta - Protocol for accessing the metadata of an object.
5. IWatchable - Protocol for types that can be watched. Currently only implemented by Atom.
It has following methods 
    * -notify-watches - Calls all watchers with this, oldval and newval.
    * -add-watch - Adds a watcher function f to this. Keys must be unique per reference,
                   and can be used to remove the watch with -remove-watch.
    * -remove-watch - Removes watcher that corresponds to key from this.
6. IHash - Protocol for adding hashing functionality to a type.

RAtom implements all the above and a few more 
7. IReset - Protocol for adding resetting functionality.
8. ISwap - Protocol for adding swapping functionality.
9. IWithMeta - Protocol for adding metadata to an object.
10. IPrintWithWriter

#### RAtom features
By just looking at the above doc strings of each of the protocols, once can easily relate to the
functionality RAtom provides.

RAtom is built on same fundamental blocks as Atom but more tailored towards React.
When a RAtom is derefed, reagent adds the RAtom to the list of `captured` list of 
the reagent component. 

While executing the render function (CLJS function for type 1 or 2), reagent notes
down all the RAtoms derefed in the `captured` field. After noting down all the 
RAtoms, reagent adds component as watcher on each of these RAtoms by calling `add-watch`.

Since each RAtom is aware of the components (CLJS functions) that deref it, reagent 
calls force update on those component when the value of the RAtom changes.

#### Some other details
* When `add-watch` is called on a RAtom, reagent adds `key` and the watcher `function`
to `watches` map/object.  
* When `notify-watches` is called, reagent copies the values from the `watches` to
`watchesArr` just to speed up the iteration.   

### Open questions
- Does reagent remove the component form the captured list after its unmounted?

 


  

    

 