---
title: First Reagent Component
style: contents-page
---

## Fist reagent component
Writing a simple react component using reagent is just writing a normal CLJS
functions that takes some inputs and returns a hiccup

A simple reagent component looks something like 
```clojure
(defn simple-component [greeting name]
  [:div {:style {:color "blue"}}
   [:span greeting] [:span name] "!"])
```
We call this component as `[simple-component "Good Morning " "Robin Nagpal"]`.
It is important to note that we call the component using `[]` and don't call it
as a normal function like `(simple-component "Good Morning " "Robin Nagpal")`
 
```clojure
(reagent/render
    [simple-component "Good Morning " "Robin Nagpal"]
    (.getElementById js/document "app"))
```

# Internals  `CLJS function <==> React Component`
You will be overwhelmed with this section if you are just getting started with 
reagent. This might be of interest to you after you have created a few components
and now want to understand what exactly is happening behind the scenes. 

If we try to debug the rendered component in [react developer tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)
we can relate how reagent converts function to a react component.

![Reagent Component](/book/images/web/01-first-component/01-reagent-component.png "Reagent Component")

We see here that reagent has created a react component corresponding to our function
and the name of that component is the fully qualified name of the function.


This is done in following steps

#### 1. Determine element type 
Reagent looks at the element and determines if its an vector. If its an vector and the 
first element is a not a HTML tag like `:div` or `:span` etc, then it tries to fetch
the component from the cache 


#### 2. Looks for component in cache
Reagent checks if the component is already present with this name(fully qualified) 
in the cache. Reagent doesn't keep a separate cache, but it looks for the property
`:cljsReactClass` on that function(JavaScript object), if not present it creates 
a new component. 

#### 3. Creates new
If a component is not present as `:cljsReactClass` property of the CLJS function,
it then  creates a new react component of the function and sets `:cljsReactClass`
property as newly created component class. Next time the same cljs function/component 
is accessed, the react component set on `:cljsReactClass` will be reused.  

The creation process is as follows
1. Reagent fetches the `meta` of the function. `meta` for our above function is `nil`.
2. `assoc` the `:reagentRender` key with value as the CLJS function to the result of `meta`.
 The result of this is a map which looks like
`{:reagentRender #object[reagent_internals$core$simple_component]}`
3. Reagent then adds empty  `:shouldComponentUpdate` `:componentWillMount` `:componentWillUnmount` to 
this map. 
4. Reagent then creates wrapper functions which confirm to the react lifecycle method names and signatures
5. Converts the CLJS map from above to JS object and calls `create-react-class` on it. 
This is the step that results in react component.
6. Sets this react component as `:cljsReactClass` property on that function(JavaScript object)


7. Passes the props as `{:argv v}`. Here v is `[simple-component "Good Morning " "Robin Nagpal"]`

Below 
![Component Props](/book/images/web/01-first-component/03-simple_component.png "Reagent Component")  
![Component Props](/book/images/web/01-first-component/04-cljsReactClass-prtotype.png "Reagent Component")  

![Component Props](/book/images/web/01-first-component/02-argv-props.png "Reagent Component")    

  


### 4.  

## Updating the component



#### By passing new props

#### By updating ratom


## FAQs
#### How is a function mapped to a react component?

#### What are the props passed to the component?

#### How does component refresh, when a ratom is updated?

#### How is hiccup converted to HTML?

#### Calling simple component as `[SimpleComponent ...]` vs `(SimpleComponent ...)`
