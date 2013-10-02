---
layout: post
title: "Counting the number of midnights between two dates"
date: 2013-10-02 00:09
comments: true
categories: [Objective-C, iOS]
---

Math with dates is always tricky, calculate the number of midnights between two days is not an exception.
Here I have extended the example from the [Apple documentation](https://developer.apple.com/library/ios/documentation/cocoa/Conceptual/DatesAndTimes/Articles/dtCalendricalCalculations.html#//apple_ref/doc/uid/TP40007836-SW8) to avoid `nil` dates.
 
<!-- more -->

```objc
- (NSInteger)midnightsBetweenFromDate:(NSDate *)startDate
                               toDate:(NSDate *)endDate
{    
    if (startDate == nil || endDate == nil)
        return NSNotFound;

    NSCalendar *calendar = [[NSCalendar alloc] initWithCalendarIdentifier:NSGregorianCalendar];

    NSInteger startDay = [calendar ordinalityOfUnit:NSDayCalendarUnit
                                             inUnit:NSEraCalendarUnit
                                            forDate:startDate];

    NSInteger endDay = [calendar ordinalityOfUnit:NSDayCalendarUnit
                                           inUnit:NSEraCalendarUnit
                                          forDate:endDate];
    [calendar release];

    if (startDay == NSNotFound)
        return startDay;

    if (endDay == NSNotFound)
        return endDay;

    return endDay - startDay;
}
```