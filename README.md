# Sorting With `NSSortDescriptor`

## Objectives

1. Identify use cases for sorting data.
2. Recognize the difficulty of hardcoding sort algorithms.
3. Create `NSSortDescriptor` objects with the correct key paths and ascending options.
4. Know that `@selector(caseInsensitiveCompare:)` is available when sorting strings.
5. Utilize a sort descriptor on the data in an `NSArray` with the `sortedArrayUsingDescriptors:` method.
6. Utilize subordinate sort descriptors on the data in an `NSArray`.
7. Make a reversed copy of a sort descriptor with the `reversedSortDescriptor` method.

## The Prevalence Of Sorting

It's difficult to think of a situation when interacting with data that wouldn't benefit from some form of sorting. Sorting helps us access, understand, and analyze data quickly and efficiently. Sorting books helps us find a physical copy on the shelf. Sorting music helps us find the song stuck in our heads. Sorting receipts helps us track our spending.

More importantly, sorting can help us determine when any particular data is not present. Imagine looking through one of your friends' iTunes libraries to see if they have any songs by one of your favorite (but obscure) artists; without being able to sort the artists by name, you'd have to look through their *entire* library to be certain of your disappointment in their musical taste. However, being able to sort your friend's library not only lets you display their songs by rating or play count, but it can also reveal that they have no artists between *Carbon Leaf* and *Cat Stevens*—meaning that your friend urgently requires an introduction to the obscure Irish-folk fiddle group *Casadh An tSúgáin*.

### Sorting Algorithms

Sorting is actually quite a lot of work. Think of the last time that you sorted some set of physical objects. Perhaps it was your personal library of books, or perhaps it was an envelope of receipts, or perhaps it was a team building exercise that required everyone to sort themselves by height or by birthday. What methodology did you use to accomplish the task? Was it efficient? Did you do it 100% correctly, or did you make mistakes?

There are various approaches to the problem of sorting each with different merits and deficiencies. Whether you realized it or not, it's likely that you've implemented some of these "sort algorithms" in your daily life at some point or another. 

Hardcoding them into computer programs, however, is actually complex enough that we're not even going to show you a code example of the logic (but if you're feeling adventurous, try to decipher these [sort algorithm][sort_algorithms] implementations). In Objective-C, Apple provides us with a framework class that does the heavy lifting for us!

## `NSSortDescriptor`

It's difficult to improve upon Apple's own summary description of this class in the [reference documentation][NSSortDescriptor_reference]:

>An instance of `NSSortDescriptor` describes a basis for ordering objects by specifying the property to use to compare the objects, the method to use to compare the properties, and whether the comparison should be ascending or descending. Instances of `NSSortDescriptor` are immutable.

Two or three things. That's all we have to define when making a sort descriptor.

### The Example Data Set

In this reading, we'll be working with a data set (an array of dictionaries) containing information (name, age, and height) on each of the fourteen adventuring heroes from J.R.R. Tolkien's *The Hobbit*. This set of sample data is sufficiently large to meaningfully apply a few different sort options, and should be inferred as being "in scope" for all of the examples throughout this reading:

```objc
NSArray *middleEarthers = @[ @{ @"name"   : @"Bilbo"  ,
                                @"age"    : @50       ,
                                @"height" : @1.27     } ,
                             @{ @"name"   : @"Thorin" ,
                                @"age"    : @195      ,
                                @"height" : @1.49     } ,
                             @{ @"name"   : @"Balin"  ,
                                @"age"    : @178      ,
                                @"height" : @1.38     } ,
                             @{ @"name"   : @"Dwalin" ,
                                @"age"    : @169      ,
                                @"height" : @1.50     } ,
                             @{ @"name"   : @"Bifur"  ,
                                @"age"    : @155      ,
                                @"height" : @1.35     } ,
                             
                             @{ @"name"   : @"Bofur"  ,
                                @"age"    : @155      ,
                                @"height" : @1.45     } ,
                             @{ @"name"   : @"Bombur" ,
                                @"age"    : @155      ,
                                @"height" : @1.35     } ,
                             @{ @"name"   : @"Fíli"   ,
                                @"age"    : @82       ,
                                @"height" : @1.35     } ,
                             @{ @"name"   : @"Kíli"   ,
                                @"age"    : @77       ,
                                @"height" : @1.43     } ,
                             @{ @"name"   : @"Glóin"  ,
                                @"age"    : @158      ,
                                @"height" : @1.41     } ,
                             
                             @{ @"name"   : @"Óin"    ,
                                @"age"    : @167      ,
                                @"height" : @1.45     } ,
                             @{ @"name"   : @"Dori"   ,
                                @"age"    : @155      ,
                                @"height" : @1.36     } ,
                             @{ @"name"   : @"Ori"    ,
                                @"age"    : @155      ,
                                @"height" : @1.35     } ,
                             @{ @"name"   : @"Gandalf",
                                @"age"    : @2019     ,
                                @"height" : @1.80     }
                             ];
```
**A Note On The Data For Fellow Tolkien Nerds:** *The characters' [heights][heights] are extrapolated from the heights of the actors cast in the Peter Jackson films, while the characters' [ages][ages] are based on their descriptions from the book. For the dwarves without an explicitly stated age, they have been listed as being 155 years old—the median age of the possible range. [Gandalf's listed age][Gandalf_age] of 2,019 years is that of his physical form in Middle Earth; as a Maiar, he is some 11,000 years old, having been created before recorded history.*

### Creating An `NSSortDescriptor` Object

There are two class methods on `NSSortDescriptor` that we'll be using to define sort options. These are:

* `sortDescriptorWithKey:ascending:` — the simplified two-argument creation method, and
* `sortDescriptorWithKey:ascending:selector:` — the optional three-argument creation method.

The **key path** argument takes the instructions for accessing the value by which we wish to sort. When accessing a dictionary like we have in the examples here, this is the name of the relevant key.

The **ascending** argument takes a `BOOL` instruction for whether the information should be ordered from low to high (ascending, or `YES`), or from high to low (descending, or `NO`). **Note:** *When sorting strings, alphabetical order is* `ascending:YES`.

The **selector** argument (optional) provides additional functionality that is quite expansive in its entirety. The primary option useful to you at this point is submitting `@selector(caseInsensitiveCompare:)` when sorting strings. This will treat uppercase and lowercase letters as equivalent, which is *not* the default.

Let's create a sort descriptor that will alphabetize our adventurers by name. We'll want to submit `@"name"` as the key argument and `YES` to ascending (since we want the names sorted alphabetically). Since we're sorting strings, let's also submit the `@selector(caseInsensitiveCompare:)` to the optional `selector:` argument. We should also name our sort descriptor appropriately to remind us later of the key path and ascending option with which it was created; something like `sortByNameAsc` should do: 

```objc
NSSortDescriptor *sortByNameAsc = [NSSortDescriptor sortDescriptorWithKey:@"name"
                                                                ascending:YES
                                                                 selector:@selector(caseInsensitiveCompare:) ];
```
Great! Now we have the `sortByNameAsc` sort descriptor that we can use on any group of dictionaries that contain a key `@"name"`.

### Utilizing An `NSSortDescriptor` Object

Setting up the sort descriptor is only the first half of the process. The second step is to apply the sort descriptor to a data set. This can be accomplished by using the `sortedArrayUsingDescriptors:` method on `NSArray`, or the `sortUsingDescriptors:` method on `NSMutableArray`. 

Since our adventurers are stored in an `NSArray`, we'll need to use `sortedArrayUsingDescriptors:` and capture its return in a new array that we'll call `alphabetizedMiddleEarthers`:

```objc
NSArray *alphabetizedMiddleEarthers = [middleEarthers sortedArrayUsingDescriptors:@[sortByNameAsc]];
    
for (NSDictionary *character in alphabetizedMiddleEarthers) {
    NSLog(@"%@", character[@"name"]);
}
```
This will print:

```
Balin
Bifur
Bilbo
Bofur
Bombur
Dori
Dwalin
Fíli
Gandalf
Glóin
Kíli
Ori
Óin
Thorin
```

### Adding Secondary Sort Descriptors

Did you notice that `sortedArrayUsingDescriptors:` actually takes an `NSArray` as an argument? That's because sort methods can sub-sort their results according to a theoretically unlimited list of subordinate parameters. 

It's like organizing your books by author and then by title; "by author" is the primary sort parameter while "by title" is the secondary sort parameter. We could further add a tertiary parameter "by edition" if we have multiple copies of the same book but from different publishing runs (called "editions"). Our three sort parameters "by author", "by title", and "by edition" could be translated into an array of sort descriptors.

Let's write a new sort descriptor to sort our adventurers from tallest to shortest. We can use the two-argument creation method since we're not sorting strings. We'll want to submit `@"height"` as the key path and `NO` for ascending:

```objc
NSSortDescriptor *sortByHeightDesc = [NSSortDescriptor sortDescriptorWithKey:@"height"
                                                                   ascending:NO];
```
Great! Now we have two sort descriptors that we can use together. Let's make our new `sortByHeightDesc` sort descriptor the primary one by adding it to the array of descriptors first:

```objc
NSArray *middleEarthersByHeightByName = [middleEarthers sortedArrayUsingDescriptors:@[sortByHeightDesc, sortByNameAsc] ];

for (NSDictionary *character in middleEarthersByHeightByName) {
    NSLog(@"%@ is %@ meters tall.", character[@"name"], character[@"height"]);
}
```
This will print:

```
Gandalf is 1.8 meters tall.
Dwalin is 1.5 meters tall.
Thorin is 1.49 meters tall.
Bofur is 1.45 meters tall.
Óin is 1.45 meters tall.
Kíli is 1.43 meters tall.
Glóin is 1.41 meters tall.
Balin is 1.38 meters tall.
Dori is 1.36 meters tall.
Bifur is 1.35 meters tall.
Bombur is 1.35 meters tall.
Fíli is 1.35 meters tall.
Ori is 1.35 meters tall.
Bilbo is 1.27 meters tall.
```
Notice how Bifur, Bombur, Fíli, and Ori's names are all alphabetized since they're the same height? Our secondary sort descriptor was applied correctly.

#### Adding A Tertiary Sort Descriptor

We can create another sort descriptor to arrange our adventures by age. Let's call it `sortByAgeDesc`:

```objc
NSSortDescriptor *sortByAgeDesc = [NSSortDescriptor sortDescriptorWithKey:@"age"
                                                                ascending:NO];
```
Now we can sort our adventurers by age and then by height. Let's see what arrangement we get when we do this:

```objc
NSArray *middleEarthersByAgeByHeight = [middleEarthers sortedArrayUsingDescriptors:@[sortByAgeDesc, sortByHeightDesc]];

for (NSDictionary *character in middleEarthersByAgeByHeight) {
    NSLog(@"%@ is %@ years old and %@ meters tall.", character[@"name"], character[@"age"], character[@"height"]);
}
```
This could print:

```
Gandalf is 2019 years old and 1.8 meters tall.
Thorin is 195 years old and 1.49 meters tall.
Balin is 178 years old and 1.38 meters tall.
Dwalin is 169 years old and 1.5 meters tall.
Óin is 167 years old and 1.45 meters tall.
Glóin is 158 years old and 1.41 meters tall.
Bofur is 155 years old and 1.45 meters tall.
Dori is 155 years old and 1.36 meters tall.
Bifur is 155 years old and 1.35 meters tall.
Ori is 155 years old and 1.35 meters tall.
Bombur is 155 years old and 1.35 meters tall.
Fíli is 82 years old and 1.35 meters tall.
Kíli is 77 years old and 1.43 meters tall.
Bilbo is 50 years old and 1.27 meters tall.
```
Did you notice that Bifur, Ori, and Bombur are all the same age and height, but didn't print alphabetically by name? Let's add our `sortByNameAsc` sort descriptor to the descriptors array to subordinately organize these three adventurers by name:

```objc
NSArray *middleEarthersByAgeByHeightByName = [middleEarthers sortedArrayUsingDescriptors:@[sortByAgeDesc, sortByHeightDesc, sortByNameAsc]];

for (NSDictionary *character in middleEarthersByAgeByHeightByName) {
    NSLog(@"%@ is %@ years old and %@ meters tall.", character[@"name"], character[@"age"], character[@"height"]);
}
```
This will print:

```
Gandalf is 2019 years old and 1.8 meters tall.
Thorin is 195 years old and 1.49 meters tall.
Balin is 178 years old and 1.38 meters tall.
Dwalin is 169 years old and 1.5 meters tall.
Óin is 167 years old and 1.45 meters tall.
Glóin is 158 years old and 1.41 meters tall.
Bofur is 155 years old and 1.45 meters tall.
Dori is 155 years old and 1.36 meters tall.
Bifur is 155 years old and 1.35 meters tall.
Bombur is 155 years old and 1.35 meters tall.
Ori is 155 years old and 1.35 meters tall.
Fíli is 82 years old and 1.35 meters tall.
Kíli is 77 years old and 1.43 meters tall.
Bilbo is 50 years old and 1.27 meters tall.
```
Our three equal dwarves are now subordinately arranged alphabetically by name!

### Reversing Sort Descriptors

The Apple documentation tells us that sort descriptors cannot be changed once they're created. But what if we just want to switch the order of the sort descriptor; do we have to create a whole new one? Well, yes, *but* we can take a shortcut by using `NSSortDescriptor`'s handy `reversedSortDescriptor` method to make a copy of the submitted sort descriptor but with the opposite `ascending:` argument from the original.

Let's take our `sortByNameAsc` sort descriptor and use it to make a similar, but opposite, `sortByNameDesc` sort descriptor:

```objc
NSSortDescriptor *sortByNameDesc = [sortByNameAsc reversedSortDescriptor];
```
Easy enough, right? Let's apply it to our array of adventurers and print the result:

```objc
NSArray *reverseAlphabetizedMiddleEarthers = [middleEarthers sortedArrayUsingDescriptors:@[sortByNameDesc]];

for (NSDictionary *character in reverseAlphabetizedMiddleEarthers) {
    NSLog(@"%@", character[@"name"]);
}
```
This will print:

```
Thorin
Óin
Ori
Kíli
Glóin
Gandalf
Fíli
Dwalin
Dori
Bombur
Bofur
Bilbo
Bifur
Balin
```
Great! That's the exact opposite of sorting them alphabetically.


[sort_algorithms]: http://www.knowstack.com/sorting-algorithms-in-objective-c/
[NSSortDescriptor_reference]: https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSSortDescriptor_Class/index.html

[heights]: http://24.media.tumblr.com/fe736cede58224929734a827ba260f76/tumblr_mhos2lSLs51rmwdaro1_500h.jpg
[ages]: http://lotrproject.com/blog/2014/06/07/character-age-at-the-time-of-the-hobbit/
[Gandalf_age]: http://scifi.stackexchange.com/questions/58952/how-old-is-gandalf