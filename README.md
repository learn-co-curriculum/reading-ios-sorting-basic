# Sorting

## What Problem Are We Solving?

As with most data, when we present it to the user, we will want it to be shown
in a certain order. In a contact list, we might want to show our data sorted
alphabetically by first or last name. With a population study, we might order
the data from the cities with the greatest populations to the least.

There are many ways to sort data in Cocoa. In this reading, we will teach you
the most common and simplest way to sort `NSArray` and `NSDictionary`.

## What is NSSortDescriptor?

The most common  sorting mechanism in Cocoa is `NSSortDescriptor`. With it we can do quite a
bit and with ease. Here is the syntax for a basic `NSSortDescriptor`.

```objc

NSArray *testArray = @[@"John",@"Mary",@"Margaret",@"Joshua",@"Biff",@"Ezekiel" ];
    
NSSortDescriptor *aToZDescriptor = [[NSSortDescriptor alloc] initWithKey:nil 
ascending:YES selector:@selector(localizedStandardCompare:)];
    
NSArray *sortedTestArray = [testArray sortedArrayUsingDescriptors:@[aToZDescriptor]];
    
NSLog(@"%@",sortedTestArray);

```

This will result in the following output:

```
2014-10-16 11:27:25.210 sortingTests[95961:1626860] (
    Biff,
    Ezekiel,
    John,
    Joshua,
    Margaret,
    Mary
)
```

You'll notice that there are three arguments in an `NSSortDescriptor`:

1. Key: Used for `NSDictionary` keys (so not relevant for our `NSArray` example
   above, which is why it is `nil` there)
2. Ascending: Whether the sort order is ascending (`YES`) or descending (`NO`)
3. Selector: This has a number of uses, but most frequently is used for alphabetically sorting based on local language. Figure that if you are writing a sort descriptor that sorts on letter, you'll probably want to set this. There is a version of the method without the selector argument if you want to use that though. We have used this version of the method in our next example.

Our array is then sorted using the `NSSortDescriptor` we have created. 

You'll notice that the method `sortedArrayUsingDescriptors:` takes an array argument. This is because we might want to sort and sub-sort. This is only relevant for `NSDictionary` sorting. 

For instance, if we were to sort a group of people by `name` and then `age`. If two people had the same `name`, we would have implemented two sort descriptors, one to sort by `age`, and then one to further sort those who have the same `name` but different `age`. Keep in mind, the order of your array of `NSSortDescriptor`s here does matter. The first `NSSortDescriptor` will take precedence, meaning that if we have a person named Aaron and a person named Zach whose ages are 20 and 60 respectively and we sort using `@[@ageAscendingSortDescriptor,@nameAscendingSortDescriptor]`, the output will place Zach first and Aaron second.

Here is an example with an NSDictionary, sorted by age.

```
NSDictionary *johnDict = @{@"name":@"John",@"age":@18};
NSDictionary *maryDict = @{@"name":@"Mary",@"age":@39};
NSDictionary *margDict = @{@"name":@"Margaret",@"age":@24};
NSDictionary *joshuaDict = @{@"name":@"Joshua",@"age":@21};
NSDictionary *biffDict = @{@"name":@"Biff",@"age":@21};
NSDictionary *ezekDict = @{@"name":@"Ezekiel",@"age":@400};
    
NSArray *ourCharacters = @[johnDict, maryDict, margDict, joshuaDict, biffDict, ezekDict];
    
NSSortDescriptor *youngestToOldestDescriptor = [[NSSortDescriptor alloc] initWithKey:@"age" ascending:YES];
    
NSArray *charactersSortedByAge = [ourCharacters sortedArrayUsingDescriptors:@[youngestToOldestDescriptor]];
    
NSLog(@"%@",charactersSortedByAge);
```

The output of this code will result in the following output in our debug console:

```
2014-10-16 14:01:56.936 sortingTests[6152:1692016] 
(
        {
        age = 18;
        name = John;
    },
        {
        age = 21;
        name = Joshua;
    },
        {
        age = 21;
        name = Biff;
    },
        {
        age = 24;
        name = Margaret;
    },
        {
        age = 39;
        name = Mary;
    },
        {
        age = 400;
        name = Ezekiel;
    }
)
```
For an example of multiple sort descriptors, here is what we would get if we also sorted by age (ascending).

First, the code:

```
NSDictionary *johnDict = @{@"name":@"John",@"age":@18};
NSDictionary *maryDict = @{@"name":@"Mary",@"age":@39};
NSDictionary *margDict = @{@"name":@"Margaret",@"age":@24};
NSDictionary *joshuaDict = @{@"name":@"Joshua",@"age":@21};
NSDictionary *biffDict = @{@"name":@"Biff",@"age":@21};
NSDictionary *ezekDict = @{@"name":@"Ezekiel",@"age":@400};
    
NSArray *ourCharacters = @[johnDict, maryDict, margDict, joshuaDict, biffDict, ezekDict];
    
NSSortDescriptor *youngestToOldestDescriptor = [[NSSortDescriptor alloc] initWithKey:@"age" ascending:YES];
   
NSSortDescriptor *aToZDescriptor = [[NSSortDescriptor alloc] initWithKey:@"name" ascending:YES];
    
NSArray *charactersSortedByAge = [ourCharacters sortedArrayUsingDescriptors:@[youngestToOldestDescriptor,aToZDescriptor]];
    
NSLog(@"%@",charactersSortedByAge);
```

And the output:
```
2014-10-16 14:15:28.736 sortingTests[14511:1711217] (
        {
        age = 18;
        name = John;
    },
        {
        age = 21;
        name = Biff;
    },
        {
        age = 21;
        name = Joshua;
    },
        {
        age = 24;
        name = Margaret;
    },
        {
        age = 39;
        name = Mary;
    },
        {
        age = 400;
        name = Ezekiel;
    }
)
```
And in the above, you see, Biff is listed before Mary, whereas in the earlier example, we left it to chance.

And those are the basics of `NSSortDescriptor`!
