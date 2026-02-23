# Release Notes

### December 2025

* Improved builds UI, we now include time the build took as well as branch and commit hash details
* Successful automatic builds have a link to the git commits diff between previous and current build
* New deployment strategy configuration, select between rolling update or re-create (forces the new instance regardless of the previous builds instance shutting down)
* Improved metrics accuracy as well as graphs for multiple replicas across capsule types
* Private enterprise customers: GPU flag to ensure your workloads consume GPU compute
* Performance improvements to egress networking
* General bug fixes and improvements across the site
* New agent capsule type: [build and deploy your langchain based agents](https://docs.codecapsules.io/products/agent-capsule/deploy)
* Improvements to Postgres data capsule horizontal scaling
* Capsules shouldn't rebuild on certain config changes like port and environment variables, it will just restart the capsule
* Improvements to instance restarts, previously duplicate restarts occurred

### November 2025

* Our new CLI has been released. Check it out [here](https://app.gitbook.com/o/zJVrvoPm6oe6islWnPll/s/mbIMmtQGsG9TeQSECL0E/).
* Ability to proxy and connect to your deployed workloads via the cli, allowing you to debug services or access your databases from your local machine
* Fixed a bug when trying to scale storage down (this is not possible)
* Fixed a bug when trying to scale databases horizontally, the correct replicas are created without any issues

### October 2025

* New and improved alerting. Check it out [here](https://app.gitbook.com/s/oyCI3rJYfUxA3cJhHZbu/database-capsule/alerting).
* Ability to get email notifications for any workload alerts like OOM, too much CPU being used, unexpected app crashes, etc.

### September 2025

* Performance improvements
* Bug fixes

### August 2025

***

We’ve rolled out some big updates across logging, storage, monitoring, and scaling.

Here’s what’s new:

**Logging Overhaul: Logging just got a whole lot smoother**

* Brand new logging UI with a cleaner experience.
* Filter logs by replica/instance for horizontally scaled workloads.
* Blazing fast querying & searching.
* Real-time logs via websockets (no more delayed polling).
* Date/time filters with improved performance for historical digging.
* Download logs in JSON, text, or CSV.
* Full-screen mode for easier reading.
* Beta: Log retention by time and size.

_For Internal Developer Platform (IDP) customers:_

* Improved backend performance & stability → lowers infra costs while boosting performance.
* Updated dependencies and security patches across the logging stack.

#### Storage Management (IDP only)

* Smarter handling of storage and database workloads.
* Automatic self-healing for horizontally scaled workloads during restarts.
* Helps right-size infrastructure for lower costs and better performance.

#### State Management Foundation

* Laying the groundwork for websocket-based capsule state management.
* Coming soon: real-time capsule status updates (starting, running, scaling, shutting down, OOM, etc).

#### Monitoring (IDP only)

* Enhanced monitoring stack with advanced alerting and integrations.
* Custom dashboards & reporting across infra, platform, and applications.
* Support for custom metrics scraping/importing.

#### Database Backup Improvements

* Flexible backup schedules: daily, weekly, or monthly.
* Supported across Storage, MySQL, Postgres, and Mongo.

#### Workload Scaling (IDP only)

*   Scale CPU and RAM independently to fine-tune workload requirements.

    (e.g. high CPU + low RAM or the other way around).

That’s it for now, with these updates, you’ll see faster insights, better reliability, and more flexibility across your workloads.
