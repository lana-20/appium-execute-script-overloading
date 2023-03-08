# Appium Execute Script Overloading 

driver.execute_script()  |
------------- |
Used to execute JavaScript inside of a webpage you are automating |
Native apps don't run JavaScript, so this method is basically useless as it stands for native apps |
You can use this method for Appium. It is "overloaded" to give access to a bunvh of extra features for automation |


Let's learn about something that I call Execute Script Overloading in Appium. Basically, it's a way of using the <code>driver.execute_script<c/ode> method for purposes beyond its original intent.

In Selenium, there's a command called <code>driver.execute_script<c/ode>. It is supposed to take a string representing the body of a Javascript function, and other parameters as arguments for that function. The idea is that you use this method to run Javascript inside of a webpage you are automating. Now, native apps don't run on Javascript and HTML, so this method is sort of useless when it comes to Appium's automation of native apps. But, the Appium developers have found a very interesting use for this method, which puts it to service in implementing a whole host of new commands that Appium provides. We call this overloading the execute script function, in other words, adding responsibilities to it that it wasn't originally written for. But in practice, it works well! Let's discuss a bit more.

Why does Appium want to use the execute script function for a different purpose? Why not just leave it alone and ignore it, if it makes no sense for mobile apps?

Why overload? |
------------- |
There are many new features the Appium team wanted to add to the WebDriver spec |
Some features were added by extending the WebDriver spec |
Other features weren't appropriate as WebDriver extensions, but Appium wanted to make them available anyway |

Well, early on, the Appium team realized there are lots of new features that we wanted to add to the WebDriver spec. For some of these features, it made sense to extend the WebDriver spec like we talked about in an earlier video. We created new commands and new routes, and then we added support for these new routes to the Appium client. But for other features, we weren't quite sure if this was the right approach. Maybe the feature was experimental and we weren't sure if it would stick around for a while. In this case, setting a route and parameters in stone would be hard to change. Or, maybe the feature was only available on a single platform, and so it felt odd to create a route for just a single platform. Or, maybe we wanted the feature to be available immediately, without requiring updates in the Appium client libraries. Because we kept running into cases like this, we needed another way to make these commands available.

And lo an behold, we actually had a very natural way to solve this problem sitting right in front of us, in the form of the Execute Script command.

The Execute Script command was designed from the get-go to allow for the execution of code, with arbitrary parameters. That is very much like what we wanted for all the Appium features that fell into one of the categories I just mentioned. Moreover, the Execute Script command was not currently being used with Appium, so it was available for repurposing. And finally, the Execute Script command was already available in every Appium and Selenium client, with a flexible and future-proof API. So, what we did is establish a sort of convention, where we could expose certain commands from Appium via <code>driver.execute_script</code>, simply by putting the name of the command as the Javascript string, with a special <code>mobile:</code> prefix. Then, the command parameters, if it required any, could be passed in as the first "Javascript" parameter in the execute script command. Let's look at an example.

Here's an example of an execute_script call:

    driver.execute_script('mobile: enrollBiometric', {'isEnabled': True})

The particular command we're running here is called <code>enrollBiometric</code>, and to access it we use the command name as the body of the execute script javascript string, but with the prefix <code>mobile:</code> in front of it. Why do we have this prefix? Well, in the event where executing normal Javascript ever does make sense with Appium, we want to make sure that our special Appium commands aren't interpreted somehow as valid Javascript.

What does this <code>enrollBiometric</code> command do? Well, it's something that works on iOS only, and it can either enroll or unenroll a user from the biometric system, meaning TouchID or FaceID. For security reasons, this command only works on simulators, by the way. But you can see that in addition to the command itself which is sent as the first parameter of the method call, we are passing a Python dictionary as the second parameter. This is because the <code>enrollBiometric</code> command takes arguments. By convention, all these execute script mobile methods take a single parameter, which on the server side of things is just a JSON object. In the Python client, we can send in a Python dictionary with keys and values that will be automatically converted to a JSON object when the command is called.

Each command will specify what sort of object should be sent in. In the case of this <code>enrollBiometric</code> command, it's an object with an <code>isEnabled</code> key, and a boolean value. If the value is set to true, then the simulator will be enrolled in the biometric system. If it's false, then it will be unenrolled.

So that's it! The point of this discussion isn't to get into details of biometric automation for iOS. Rather, we needed to discuss the way that Appium overloads the execute_script method, and the reasons that it does this as well.
