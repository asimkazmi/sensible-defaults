#NEVER use Thread.sleep() unless there are specific test requirements
 

This is a golden rule that you need to follow regardless of the complexity of your web application. Sleep or exact timeout (better known as Thread.sleep() function and analogs) block your test thread for an exact specified number of seconds. In other words, it gives you the ability to pause your tests. When would you need such a capability?

 

The behavior of web applications depends on many factors like network speed, your machine capabilities or the current load on application servers. Because of all these factors, you cannot always predict how much time it will take to load a specific page or web element. That’s why sometimes you might want to add a timeout and pause script check execution for at least a certain amount of time.

 

If you do not know how to deal with this in the right way, you will never see stability in your UI automation.

 

Let’s assume that in our test we are going to open the home page and verify the heading of the main page. Extremely simple. You just need to implement two functions. One that opens the page and the second that verifies if the heading element is presented and has the right value. But what if we knew that our application can take up to 7-8 seconds to start?

 

Selenium, a web browser automation tool, works pretty fast and honestly saying, even faster than you. It can open the page in milliseconds and will try to get the text of heading element while the application itself is still starting. In this situation…. please, NEVER write a code like this:

*** 
`
new.openHomePage();
Thread.sleep(10000);
new.verifyThatTitleAndHeadingTextIsCorrect();
 `
***

This is the strongest killer of UI automation tests stability. Why?

 

We lose time because you know that in 95% of cases, the application should be up and running in 7-8 seconds. Therefore, each time we lose about 2-3 seconds of execution time.
 

Do you think this is nothing? I have seen many projects that have about 3000 UI tests. Each time you need to open the application and wait until it is up and running. Maybe you even want to run it on 3 different browsers? 3000 (tests) * 3 (browsers) * 2.5 (lost seconds in average) = 22500 (seconds) = 375 (minutes) = 6.25 HOURS! To sum up, in this situation we lose around 6 hours just because we do not want to create it properly.

 

What happens if application load takes 10.5 seconds one day? Just because of some network slowness. We will get failed tests. But when you try to run it locally on the next day, it works perfectly fine. This is one more example of the troubles you might get into by using this way of waiting in your tests. 
 

I guess you see that it is bad, right? Then how should such a situation be managed? You can find the answer in the main Selenium documentation - implicit and explicit waits! Exactly in this order. An implicit wait tells the browser to wait for a specified time for all elements. If a certain element is not found in this time, report this as a failure. If the element is found faster than the specified time, move on and do not wait the full time. For example, if the implicit wait specified 5 seconds but the element appeared after 2 seconds, then our script will not wait the rest of the 3 seconds. This is a huge time saver for your UI automation tests.

 

This is how you can specify the implicit wait in Java by using Selenium:

***
`
WebDriver driver = new FirefoxDriver();
driver.manage().timeouts().implicitlyWait(5, TimeUnit.SECONDS);
 `
***
What is the explicit wait then? The explicit wait is designed for needs when a certain web element or action takes a much longer time to load than the rest. What if your application works in a way that it takes a long time to start (7-8 seconds) but after that it works very fast? There is no sense to specify the implicit wait as 10 or even 15 seconds only because you have a slow application loading. For this, you can use the explicit wait, which waits for a certain condition for a specified amount of time.

 

Here is how we can rewrite our previous example by using the idea of the explicit wait:

*** 
`
new.openHomePage();
withTimeoutOf(15, TimeUnit.SECONDS).waitFor(MAIN_HEADING_LABEL);
new.verifyThatTitleAndHadingTextIsCorrect();
 `
***
In this case, we also do not waste any time and the script execution will continue immediately after the expected element will be found.

 

Looks clear, right? Not so clear as you might think… the official Selenium website displays this very important note:

 

“Do not mix implicit and explicit waits. Doing so can cause unpredictable wait times. For example setting an implicit wait of 10 seconds and an explicit wait of 15 seconds, could cause a timeout to occur after 20 seconds.”