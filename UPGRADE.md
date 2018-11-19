## v1/v2 => v3

### Implementationof TaskScheduler\Process

If you do not use the return value of `TaskScheduler\Scheduler::addJob` or `Scheduler::addJobOnce` the upgrade in user space will be fully compatible.
One major change is, that you will receive an instance of `TaskScheduler\Process` instead just the process id from those methods.

To retrieve the job id, you will need to do the following in v3 (Note the getId() call):

```
$job_id = $taskscheduler->addJob(MyJob::class, ['foo' => 'bar'])->getId();
```

### New queue name

The job structure is different from v2 and v1. Meaning a queued task from v2 will break compatibility in v3. 
By default v3 will create new queues (`taskscheudler.jobs` and `taskscheduler.events`). You have to make sure that your old v2 queue
has no job left before upgrade. (Those will not get upgraded automatically).
You may change the new queue names in the scheduler options.

### pcntl dependency

v3 requires the php pcntl dependency to handle forks. PHP must be compiled with ` --enable-pcntl` which should be the case for most distributed php packages. 
If your app runs on docker, you may easly add `docker-php-ext-install pcntl && docker-php-ext-enable pcntl` to your Dockerfile.

### sysvmsg dependency

v3 requires a systemv message queue besides the mongodb queue to communicate with forks. PHP must be compiled with `--enable-sysvmsg` which should be the case for most distributed php packages.
If you build your app on docker, you may easly add `docker-php-ext-install sysvmsg && docker-php-ext-enable sysvmsg` to Dockerfile.

### Run once (cron)

If you did use TaskScheduler\Queue::processOnce before, this wont be possible anymore. There is no support for running jobs once anymore (via cron for example). You will likely need to migrate to TaskScheduler\Queue::process which acts as a daemon.

### Worker factory

v3 does require a worker factory to initialize a queue node. Please see the documentation [here](README.md#execute-jobs).
