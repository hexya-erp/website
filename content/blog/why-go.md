+++
title = "Why we chose Go for Hexya"
description = "With a dynamic iterative model definition system, the choice for Hexya's programming language would logically have gone to a dynamic language. Yet, we chose Go, and we explain why."
weight = 20
draft = false
date = "2017-08-28T20:00:00+02:00"
+++
## Origin of Hexya

As explained in our [design](/docs/design/) doc, Hexya is a rewrite of Odoo.
At the beginning of 2016, a guy who participate in a personnal project written in Python asked me if I ever heard about Go.
I didn't, so I went to Go's website and read _"Go is an attempt to combine the ease of programming of an interpreted, dynamically typed language with the efficiency and safety of a statically typed, compiled language"_.

At the same time, I was struggling on a professional Odoo project. 
The nightly scheduler process for my customer took over 55 hours when we ran it the first time with only initial data.
With my team, we had to override loads of core Odoo code, by-passing the ORM with direct SQL queries in many places.
We also had to parallelize some processes to take advantage of the 32 cores our customer had.
With all our efforts, the same process time was brought back to 2 hours, more compatible with a nightly scheduled process.
But at the end of the day, we thought: _"What's the point of having a brilliant ORM if we need to by-pass it all the time?"_

So I had a performance issue with Odoo on the one side, and a language I didn't know and I wanted to test on the other side.
I had almost always coded in Python and I thought that if I could code Odoo's iterative model definition in Go, then I would definitely be able to do anything with this language.
And here I went starting developping a proof of concept of an Odoo implementation in Go: Hexya.

## Iterative Model Definition (IMD)

### What is IMD ?

Iterative model definition (IMD) is the killer feature of Odoo and a must-have for Hexya.
Before we go on with the reasons of why we chose Go, I'll digress a little on this subject, because the way Hexya is implemented largely depends on this topic. 

Iterative model definition is the ability to extend a given model in a different _module_ than the one it has been created in.
In contrast to usual class inheritance, you do not get a new (inherited) model, but instead replace the original model with the inherited one in all the application.

Typically this would be the ability to have (pseudo code):

```cs
// Module A
class Partner {

    string firstName;
    string lastName;
    
    string Name() {
        return firstName + " " + lastName;
    }

}
    
// Module B
class PartnerExtension(Partner) {

    string title;
    
    string Name() {
        return title + ". " + super().Name();
    }
        
// Anywhere else
myPartner = Partner({
    firstName: "John",
    lastName: "Doe",
    title: "Dr",
})
print myPartner.Name()  
// output: "Dr. John Doe"
```

### Why IMD ?

IMD may seem weired or dangerous to people who are not used to Odoo, since it breaks many of the best practices of development such as the SOLID paradigm.

To really understand, one should remember that Odoo (and Hexya too) is a framework for building modular business applications.
Each module is meant to bring in a functional feature to a monolithic app, but not to be a general purpose library (such as a Go package for instance).
IMD allows to modify the application without modifying the original code.
In other words you can adapt an application to your need without making a fork, and thus benefits from the original author's updates too. 

## Fast

As I said earlier, the first reason for choosing Go is because it is fast.

Odoo implements IMD with a massive use of Python's dynamic and reflective features.
Since we also have reflection capabilities in Go, the first IMD implementation of Hexya was also mainly based on reflection.

This led to an (ugly) API which looked like this:

```go
package A

func init() {
    models.NewModel("Partner", 
        &struct {
            FirstName string `hexya:"string(First Name);help(This is the first name of our partner)"`
            LastName  string `hexya:"string(Last Name);required"`
        }{})
    
    models.Registry.MustGet("Partner").AddMethod("Name", 
        `Name returns the complete display name of this partner.`,
        func(rc models.RecordCollection) string {
            return rc.Get("FirstName").(string) + " " + rc.Get("LastName").(string)
        })
}
```
```go
package B

func init() {
    models.ExtendModel("Partner", 
        &struct {
            Title  string `hexya:"help(e.g. Mr, Mrs, Dr ...)"`
        }{})
        
    models.Registry.MustGet("Partner").ExtendMethod("Name", 
        func(rc models.RecordCollection) string {
            return rc.Get("Title").(string) + ". " + rc.Super().(string)
        })
}
```
```go
// Anywhere else
partner := env.Pool("Partner").Call("Create", models.FieldMap{
    "Title": "Dr",
    "FirstName": "John",
    "LastName": "Doe",
}).(models.RecordCollection)
fmt.Println(partner.Call("Name").(string))
// output : Dr. John Doe
```
So we had it! A working proof of concept of a IMD ORM like Odoo but written in Go.
When put together, it was certainly not as fast as it could due to the large amount of reflection that it uses, but it was very much faster than Odoo.
So our goal first was achieved.

## Type safe

Yet, when I looked at the code above, I couldn't say that I was proud of it.
Actually, one could wonder if this is actually Go.
Type assertions everywhere increase the probability of runtime errors and makes the code unreadable.
Finally, one of my colleagues put all this in a few words: 

> "Don't expect anybody to use your framework if you don't provide a clean API with autocompletion" 

He was right of course. 
Too much of Python development made me think that it was normal to rely on a function's documentation to know parameters and returned values types.
And even to jump to the function implementation, reading its whole code to get the information when the documentation is missing (and this is unfortunately often the case with Odoo).   
And that's how I (re)discovered the virtues of type safety.

It also meant that I could not rely only on reflection to build the API.
Actually there is only one way to have a dynamic behaviour with a statically typed language: **code generation**.
The final implementation is a mix of the original reflection implementation with a code generated layer on top of it.
I'll explain this in a future blog post, but for now, here is what the API now looks like:

```go
package A

func init() {
    pool.Partner().AddCharField("FirstName", models.StringFieldParams{
        String: "First Name", 
        Help: "This is the first name of our partner",
    })
    pool.Partner().AddCharField("LastName", models.StringFieldParams{
        String: "Last Name",
        Required: true,
    })
    
    pool.Partner().Methods().Name().DeclareMethod( 
        `Name returns the complete display name of this partner.`,
        func(rc pool.PartnerSet) string {
            return rc.FirstName() + " " + rc.LastName()
        })
}
```
```go
package B

func init() {
    pool.Partner().AddCharField("Title", models.StringFieldParams{
        Help: "e.g. Mr, Mrs, Dr ...",
    })
        
    pool.Partner().Methods().Name().Extend(
        func(rc poolPartnerSet) string {
            return rc.Title() + ". " + rc.Super().Name()
        })
}
```
```go
// Anywhere else
partner := pool.Partner().Create(env, pool.PartnerData{
    Title: "Dr",
    FirstName: "John",
    LastName: "Doe",
})
fmt.Println(partner.Name())
// output : Dr. John Doe
```

## Designed for large code bases

While I began to obsess about type safety, I still have profesionnal projects with Odoo at the same time.
And the more I came to understand Go the more I saw the limitations of Odoo from the code point of view.
With over a million lines of code of hardly documentated Python code, Odoo is a nightmare. 

When trying to implement a new feature in Odoo, my team and I spend about 90% of our time figuring out which method to override, finding it in the code, understanding what it does exactly, inferring its parameter and return types, and then test it again and again since even the smallest type mismatch error will only show up at runtime.
The remaining 10% we actually think and write our code.

Go is designed for large code bases and makes it easy and natural to have clean code:

- Static typing makes function declaration already give half of the information you want
- Simple function documentation format: you don't ask yourself "what's the keyword for return type declaration?", but just write the doc.
- `gofmt` makes everyone feel at home on any piece of Go code

## Simplicity

I have to admit that my first impression of Go was that it was a harsh language.
Coming from Python, I missed all the syntactic sugar at first.
I was used to writing things like:

```python
total = sum([item.value for item in my_list if item.ref in ref_list])
```

I know some people don't like this kind of one-line syntax, but I find it really clean.
It almost reads like English, and I find it easy to understand what the developer meant.
At least easier than reading the Go equivalent:

```go
var total int
for _, item := range myList {
    for _, refItem := range refList {
        if refItem == item.Ref {
            total += item.Value
            break
        }
    }
}
```

But then I remembered my performance issue with Odoo in Python, and realize that my clean python code above hides actually 3 iterations (2 nested ones to build the list and the last one to sum the resulting list items).
And when I looked again at the Go code just above, it became obvious that:

- I've already saved an iteration by summing directly
- Iterating over my `refList` is a waste of time.

The fact that Go doesn't have a `Contains` function forces me to write the whole `for` loop and to admit that my code is clumsy.
I should have used a `refMap` of `bool` values here instead of a `refList` :

```go
var total int
for _, item := range myList {
    if refMap[item.Ref] {
        total += item.Value
}
```

Because Go is simple, nice looking code is naturally efficient. 
This is particularly important for Hexya which will be mostly used by business developers.

## Conclusion

While the original reason for choosing Go was only a matter of speed, the more I use Go, the more I discovered how all other aspects are as many new reasons to keep using it.
Now I'm sure that Go is the ideal language for Hexya.
