# Coffee-Resque

Coffeescript/Node.js port of Resque.  

## USAGE

First, you'll want to queue some jobs in your app:

    var resque = require('resque').connect({
      host: redisHost, port: redisPort});
    resque.enqueue('math', 'add', [1,2])

Next, you'll want to setup a worker to handle these jobs.  	Upon 
completion of the job invoke the passed callback with a result, 
if a result was produced by the job, or an error if an error was 
encountered.  An empty arg list passed to the callback indicates 
successful completion of the job.  The callback is important as 
it notifies resque that the worker has completed the current job 
and is ready for another.  Neglecting to invoke the callback 
will result in worker starvation.

    // implement your job functions.  
    var myJobs = {
      add: function(a, b, callback) { callback(null, a + b); }
    }

    // setup a worker
    var worker = require('resque')
      .connect({host: redisHost, port: redisPort})
      .worker('*', myJobs)

    // some global event listeners
    //
    // Triggered every time the Worker polls.
    worker.on('poll', function(worker, queue) {})

    // Triggered before a Job is attempted.
    worker.on('job', function(worker, queue, job) {})

    // Triggered every time a Job errors.
    worker.on('error', function(err, worker, queue, job) {})

    // Triggered on every successful Job run.
    worker.on('success', function(worker, queue, job, result) {})

    worker.start()

## Development

All code is written in Coffee Script and converted to javascript as it's 
published to npm.

For normal development, all you need to be concerned about is testing:

  $ make test

If you need to generate javascript for production purposes and don't want to use npm packages, you can use:

  $ make generate-js
  $ make remove-js

You can also have coffeescript watch the src directory and generate javascript files as they're updated.

  $ make dev

## TODO

* Generic failure handling
* Better polling