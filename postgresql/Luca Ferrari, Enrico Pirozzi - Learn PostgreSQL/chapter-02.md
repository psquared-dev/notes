* A PostgreSQL cluster is a collection of several databases that all run under the very same Post-
greSQL service or instance.

* PostgreSQL ships with a specific tool called `pg_ctl`, which helps in managing the cluster and the
related running processes. This section introduces you to the basic usage of `pg_ctl` and to the
processes that you can encounter in a running cluster. It does not matter which service manage-
ment system your operating system is using, `pg_ctl` will always be available to the PostgreSQL
administrator in order to take control of a database instance.


* The `pg_ctl` command-line utility allows you to perform different actions on a cluster, mainly
initialize, start, restart, stop, and so on. pg_ctl accepts the command to execute as the first ar-
gument, followed by other specific arguments—the main commands are as follows:


