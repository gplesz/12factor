## IX. Dev/prod parity
### Parity between development and production

Historically, there has been a big gap between development (a developer making live edits to a local deploy of the app) and production (a running deploy of the app accessed by end users).  This is a gap in time: a developer may work on code that doesn't go to production for days, weeks, or even months.  It's a gap in personnel: developers write code, ops engineers deploy it.  And it's a gap in environments: a developer may be using Nginx, SQLite, and OS X, while the production deploy uses Apache, MySQL, and Linux.

The twelve-factor app is designed for continuous deployment.  The gap in time between writing code and deploying it is small: a developer may write code and have it deployed hours or even minutes later.  The gap in personnel is small: developers who wrote code should be closely involved in deploying it and watching its behavior in production.  So it follows that the gap between development and production environments must also be made small.

<table>
  <tr>
    <th></th>
    <th>Traditional app</th>
    <th>Twelve-factor app</th>
  </tr>
  <tr>
    <th>Time between deploys</th>
    <td>Weeks</td>
    <td>Hours</td>
  </tr>
  <tr>
    <th>Code authors vs code deployers</th>
    <td>Different</td>
    <td>Same</td>
  </tr>
  <tr>
    <th>Dev vs production environments</th>
    <td>Different</td>
    <td>Same</td>
  </tr>
</table>

[Backing services](#), such as the app's database, queueing system, or cache, is one area where dev/prod parity is important.  Many languages offer libraries which simplify access to the backing service.  Some examples are in the table below.

<table>
  <tr>
    <th>Type</th>
    <th>Language</th>
    <th>Library</th>
    <th>Adapters</th>
  </tr>
  <tr>
    <td>Database</td>
    <td>Ruby/Rails</td>
    <td>ActiveRecord</td>
    <td>MySQL, PostgreSQL, SQLite</td>
  </tr>
  <tr>
    <td>Queue</td>
    <td>Python/Django</td>
    <td>Celery</td>
    <td>RabbitMQ, Beanstalkd, Redis</td>
  </tr>
  <tr>
    <td>Cache</td>
    <td>Ruby/Rails</td>
    <td>ActiveSupport::Cache</td>
    <td>Memory, filesystem, Memcached</td>
  </tr>
</table>

Developers find great appeal in using a lightweight backing service in their local environments, while a more serious and robust backing service will be used in production.  For example, using SQLite locally and PostgreSQL in production; or local process memory for caching in development and Memcached in production.

The twelve-factor app developer resists the urge to use different backing services between development and production, even when adapters theoretically abstract away any differences in backing services.  Otherwise, tiny incompatibilities crop up, causing code that worked and passed tests in development or staging to fail in production.  These types of errors, though infrequent, are maddening to debug, and create friction that disincentives continuos deployment.  The cost of this frustration and the subsequent dampening of continous deployment is extremely high over time.

The appeal of lightweight local services is also much less when you consider that modern backing services such as Memcached, PostgreSQL, and RabbitMQ are not difficult to install and run thanks to modern packaging systems, such as [Homebrew](http://mxcl.github.com/homebrew/) and [apt-get](https://help.ubuntu.com/community/AptGet/Howto).  The cost of installing and using one of these is low compared to the high benefit of dev/prod parity and continous deployment.

Adapters to different backing services are still useful, because they make porting to new backing services relatively painless.  But all deploys of the app (developer environments, staging, production) should be using the same type and version of each of the backing services.