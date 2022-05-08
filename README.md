# Enqueue It
Easy and scalable framework to schedule, queue and process background jobs as microservices for .NET applications.

## Benefits and Features
- Provides a simple way to run jobs/tasks in the background as an independent microservice and monitoring the performance of the running jobs.
- Cross-platform solution that works on any OS with .NET runtime and it does not require any other service or application.
- Schedule jobs/tasks that can be created by your application, which can also be repeated by a certain frequency.
- Schedule jobs/tasks to be run when another job is finished.
- Web dashboard that access the jobs details and results and viewing the CPU and memory usage by each job/task.

## Getting Started
In order to use EnqueueIt you need to set up one of the supported storage databases
- Add EnqueueIt package to your project
  - EnququeIt


- Add the storage package to connect to your database.
  - EnqueueIt.Redis (Recommended, to get the best performance)
  - EnqueueIt.SqlServer
  - EnqueueIt.PostgreSQL
  - EnqueueIt.MySql


- Configure EnqueueIt to connect to the storage database and that can be by editing EnqueueIt.json inside your project
  ```
  {
    "ConnectionString": "localhost:6379",
    "StorageType": "Redis",
    "Servers": [
      {
        "Queues": [
          {
            "Name": "default",
            "WorkersCount": 50,
            "Retries": 0,
            "RetryDelay": 5
          }
        ]
      }
    ]
  }
  ```
First thing you will need to setup the app to be able to enqueue and perform the jobs
  ```
  GlobalConfiguration.Current.SetupEnqueueIt(args);
  ```
Also you will need to call configure method at lease once before start using EnqueueIt, the exact name of configure method can be different based on selected storage type.
  ```
  GlobalConfiguration.Current.ConfigureRedisStorage();
  ```

To run the server service that manage and fire the enqeueued/scheduled jobs in the background
  ```
  Servers.StartServer();
  ```
  
### Start run background jobs
  ```
  BackgroundJobs.Enqueue(() => Console.WriteLine("Easy Job!"))
  ```
  This job will run for one time immediately if EnqueueIt.Server is running


### Schedule job to run later
  There are 3 types of schedule jobs:
  - Schedule job to run once, for that you only need to specify the time
    ```
    Jobs.Schedule(() => Console.WriteLine("Run this later"), DateTime.Now.AddMinutes(5))
    ```


  - Schedule a recurring job to be run every amount of time, like daily, hourly or any amount of time, to do that you can use the help RecurrnecePattren class, the following code will run the job daily at 06:00 AM
    ```
    Jobs.Schedule(() => Console.WriteLine("Run this later"), RecurrnecePattren.Daily(6))
    ```


  - Set the job to run after finishing another background job without specifying time for that, you will need the id of the background job
    ```
    //this is a background job
    string jobId = BackgroundJobs.Enqueue(() => Console.WriteLine("Easy Job!"));
    
    //this is another job to be run after the background job is being completed
    Jobs.Enqueue(() => Console.WriteLine("Run this after the easy job!"), jobId);
    ```

### Web Dashboard
  EnqueueIt.Dashboard is not required to run the jobs, you can only add it to an Asp.NET application that will give you access to the dashboard. If you use EnqueueIt for applications other than Web application, then you can create a new web application project and add EnqueueIt.Dashboard to the new project to give you access to this dashboard.
  - Configure the web application to set up the web dashboard
    ```
    builder.Services.AddEnqueueItDashboard();
    ...
    app.UseEnqueueItDashboard();
    ```
  - Access the dashboard by using http://localhost:{port}/EnqueueIt
