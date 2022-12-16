
## Hey! Where’s the context?

If you're not familiar with [Hangfire](https://www.hangfire.io/), it's a background job runner. Long running tasks enqueue to a temporary storage medium, then dequeued and processed. If you've ever used Hangfire you may have found out that your background code lost access to the app context. This post will outline the issue and one possible solution.

Let's first take a look at how Hangfire works. It serializes the parameters passed to the function you call to run on a background thread. As jobs are ran, job parameters get deserialized and passed back into your function.

Hangfire is running your code on a thread outside the context of your application. Ambient contexts such as HttpContext are unavailable. This is the reason you can't access the apps context from within a background job.

One simple solution is to pass values from the context into your job as a parameter. This is simple, but if you have a large application with several background tasks it may not be a good solution. Do you want to add another parameter to all Hangfire jobs?

There is another strategy we can use to get context data into Hangfire jobs. Hangfire, like mediatr or the .net webstack has a pipeline. Using filters provided in the Hangfire pipeline, job data can be enhanced inline.

## Filters

The pipeline actions are abstracted to two interfaces: IClientFilter and IServerFilter. When jobs are enqueued, filters of type IClientFilter are invoked.

![[ClientExampleFilter.png]]
![](file:///C:/Users/DW/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)
Example filter that adds the current user as a Job Parameter to each Hangfire job.

Filter values are pushed into jobs as parameters when enqueued and picked back up when ran. Jobs filters can inherit from JobFilterAtrribute and be scoped per job. Or they can be registered globally.

The interface for IServerFilter matches that of IClient filter. The only difference is when the filter runs. IServerFilter implementations are ran when the job is invoked. Look at the client example and then imagine the IServerFilter code ;^)

The Hangfire documentation gives an example of filters, it’s worth a read. However, it does not give an example of how to handle dependency injection. In the example above note that IServiceFactoryScope gets injected. A new scope is created from that factory, and services are resolved off that scope. This needs done because of how dependency resolution works in the Hangfire pipeline. Resolving scoped services directly will result in error.

 Note: Services are scoped to the lifetime of the Hangfire job. If you have nested background jobs, the nested jobs hitting the filter will have a null context.


## Activators

Hangfire, has another concept called an activator. Activators, like filters may be set per job or registered globally. They give access to the jobs context, parameters, method invoked, and the DI scope used for the job. We can use the activator to pull values out of the jobs parameters and do something with it.  Below is an example where a job parameter is pulled from the current job and used to set a property on a scoped service.
![[ExampleJobActivator.png]]

Example activator getting value from job and setting it on scoped service

Here you can see we are overriding AspNetCoreJobActivator. If you aren’t using .net core, you may need to override JobActivator. If that is the case, you may also need to override JobActivatorScope.

## Configuring Hangfire

In ASP.Net Core Hangifre is configured with a set of extensions methods off IServiceCollection. This familiar builder pattern makes setup simple. However, I was unable to find an example that showed how to get access to the DI container. Thankfully, it’s simple. The method AddHangfire has an override that includes ‘provider’, this gives access to the DI container allowing IServiceScopeFactory to be injected into the filter and activator instances.

![[ConfigureHangfire.png]]
Hangfire registration with activator and filter instances

*References*:

[Are your methods ready to run in background? (hangfire.io)](https://www.hangfire.io/blog/2014/05/10/are-your-methods-ready-to-run-in-background.html)

[Hangfire run background job with user context - Stack Overflow](https://stackoverflow.com/questions/58101626/hangfire-run-background-job-with-user-context)

[JobActivator with ASP.NET Core DI - question - Hangfire Discussion](https://discuss.hangfire.io/t/jobactivator-with-asp-net-core-di/8024/2)

[One JobStorage for multiple tenant · Issue #1485 · HangfireIO/Hangfire · GitHub](https://github.com/HangfireIO/Hangfire/issues/1485#:~:text=Filters%20in%20Hangfire%20are%20singletons%20by%20nature%2C%20so,the%20following%20snippet%20%28I%20haven%27t%20tested%20it%20myself%29%3A)

[c# - Cannot resolve scoped service from root provider .Net Core 2 - Stack Overflow](https://stackoverflow.com/questions/48590579/cannot-resolve-scoped-service-from-root-provider-net-core-2)

[Using Hangfire and MediatR as a Message Dispatcher - CodeOpinion](https://codeopinion.com/using-hangfire-and-mediatr-as-a-message-dispatcher/)