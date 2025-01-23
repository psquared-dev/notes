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


    * `start`, `stop`, and `restart` execute the corresponding actions on the cluster.
    * `status` reports the current status (running or not) of the cluster.
    * `initdb` (or init for short) executes the initialization of the cluster, possibly removing
any previously existing data.
    * `reload` causes the PostgreSQL server to reload the configuration, which is useful when you want to apply configuration changes.
    * `promote` is used when the cluster is running as a replica server (namely a standby node) and, from now on, must be detached from the original primary becoming independent (replication will be explained in later chapters).

* Generally speaking, pg_ctl interacts mainly with the postmaster (the first process launched within
a cluster), which in turn “redirects” commands to other existing processes. For instance, when `pg_ctl` starts a server instance, it makes the postmaster process run, which in turn completes all the startup activities, including launching other utility processes (as briefly explained in the previous chapter). On the other hand, when pg_ctl stops a cluster, it issues a halt command to the postmaster, which in turn requires other active processes to exit, waiting for them to finish.

* The postmaster process is just the very first PostgreSQL-related process launched within the instance; on some systems, there is a process named “postmaster,” while on other operating systems, there are only processes named “postgres.” The first process ever launched, despite its name, is referred to as the postmaster. The name postmaster is just that, a name used to identify a process among the others (in
particular, the first process launched within the cluster).

* PostgreSQL, in order to mitigate the side effects of privilege escalation, does not allow a cluster to
be run by privileged users, such as root. Therefore, PostgreSQL is run by a “normal” user, usually
named postgres on all operating systems. This unprivileged user owns the `PGDATA` directory and
runs the postmaster process, and, therefore, also all the processes launched by the postmaster
itself. `pg_ctl` must be run by the same unprivileged operating system user that is going to run
the cluster.

* In order to report the status of the cluster, `pg_ctl` needs to know where the database is storing
its own data—that is, where the `PGDATA` is on disk. There are two ways to make `pg_ctl` aware of
where the `PGDATA` is:
    * Setting an environment variable named `PGDATA`, containing the path of the data directory
    * Using the `-D` command-line flag to specify the path to the data director

* The command-line argument, specified with -D, always has precedence against any `PGDATA` en-
vironment variable, so if you don’t set or misconfigure the `PGDATA` variable but, instead, pass the
right value on the command line, everything works fine:

* The same concepts of `PGDATA` and the `-D` optional argument are true for pretty much any “low-level”
commands that act against a cluster and make clear that, with the same set of executables, you can run multiple instances of PostgreSQL on the same machine, as long as you keep the `PGDATA` directory of each one separate.

* Do not use the same `PGDATA` directory for multiple versions of PostgreSQL. While it could be tempting, on your own test machine, to have a single `PGDATA` directory that can be used in turn by a PostgreSQL 16 and a PostgreSQL 15 instance, this will not work as expected and you risk losing all your data. Luckily, PostgreSQL is smart enough to see that `PGDATA` has been created and used by a different version and refuses to operate, but please be careful not to share the same `PGDATA` directory with different instances.

* The `pg_ctl` command launches the postmaster process, which prints out a few log lines before
redirecting the logs to the appropriate log file. The server started message at the end confirms
that the server has started. During the startup, the PID of the postmaster is reported within square
brackets; in the above example, the postmaster is the operating system process number `27765`.
Now, if you run `pg_ctl` again to check the server, you will see that it has been started:

```bash
$ pg_ctl status
pg_ctl: server is running (PID: 27765)
/usr/pgsql-16/bin/postgres
```

As you can see, the server is now running and `pg_ctl` shows the PID of the running postmaster
(`27765`), as well as the executable command line (in this case, `/usr/pgsql-16/bin/postgres`).

* Shutting down a cluster can be much more problematic than starting it, and for that reason, it
is possible to pass extra arguments to the stop command in order to let `pg_ctl` act accordingly.
There are three ways of stopping a cluster:

    * The `smart` mode means that the PostgreSQL cluster will gently wait for all the connected clients to disconnect and only then will it shut the cluster down.

    * The `fast` mode will immediately disconnect every client and will shut down the server without having to wait (this is default).

    * The `immediate` mode will abort every PostgreSQL process, including client connections, and shut down the cluster in a dirty way, meaning that the server will need some specific activity on the restart to clean up such dirty data (more on this in the next chapters).

* In any case, once a `stop` command is issued, the server will not accept any new incoming connections from clients, and depending on the stop mode you have selected, existing connections will be terminated. The default stop mode, if none is specified, is fast, which forces an immediate disconnection of the clients but ensures data integrity.    

* If you want to change the stop mode, you can use the -m flag, specifying the mode name, as follows:

```bash
$ pg_ctl stop -m smart
waiting for server to shut down........................ done
server stopped
```

In the preceding example, the pg_ctl command will wait, printing a dot every second until all the
clients disconnect from the server. In the meantime, if you try to connect to the same cluster from
another client, you will receive an error, because the server has entered the stopping procedure.

* You have already learned how the postmaster is the root of all PostgreSQL processes, but as ex-
plained in Chapter 1, Introduction to PostgreSQL, PostgreSQL will launch multiple different processes
at startup. These processes are in charge of keeping the cluster operational and in good health.
This section provides a glance at the main processes you can find in a running cluster, allowing
you to recognize each of them and their respective purposes.

    ```bash
    postgres@716d46d91c8d:/$ ps -C postgres -af
    UID          PID    PPID  C STIME TTY          TIME CMD
    postgres       1       0  0 Jan21 ?        00:00:16 postgres
    postgres      27       1  0 Jan21 ?        00:00:00 postgres: checkpointer 
    postgres      28       1  0 Jan21 ?        00:00:00 postgres: background writer 
    postgres      30       1  0 Jan21 ?        00:00:00 postgres: walwriter 
    postgres      31       1  0 Jan21 ?        00:00:02 postgres: autovacuum launcher 
    postgres      32       1  0 Jan21 ?        00:00:00 postgres: logical replication launcher 
    root          40      33  0 Jan21 pts/0    00:00:00 su - postgres
    postgres      41      40  0 Jan21 pts/0    00:00:00 -bash
    postgres      43      41  0 Jan21 pts/0    00:00:00 /usr/lib/postgresql/17/bin/psql
    postgres    2100       1  0 Jan21 ?        00:00:00 postgres: postgres india [local] idle
    root       12297   12291  0 03:09 pts/1    00:00:00 su postgres
    postgres   12298   12297  0 03:09 pts/1    00:00:00 bash
    postgres   12299   12298  0 03:09 pts/1    00:00:00 ps -C postgres -af
    ```

    As you can see, the process with PID 1 is one that spawns several other child processes and hence
    is the first and main PostgreSQL process launched, and as such, is usually called postmaster. The
    other processes are as follows:

    * `checkpointer` is the process responsible for executing the checkpoints, which are points in time where the database ensures that all the data is actually stored persistently on the disk.

    * `background writer` is responsible for helping to push the data out of the memory to permanent storage.

    * `walwriter` is responsible for writing out the Write-Ahead Logs (WALs), the logs that are needed to ensure data reliability even in the case of a database crash.

    * logical replication launcher is the process responsible for handling logical replication. 
    
    Depending on the exact configuration of the cluster, there could be other processes active:
    * `Background workers`: These are processes that can be customized by the user to perform background tasks.
    
    * `WAL receiver and/or WAL sender`: These are processes involved in receiving data from or sending data to another cluster in replication scenarios.


    When a client connects to your cluster, a new process is spawned: this process, named the back-end process, is responsible for serving the client requests (meaning executing the queries and returning the results). You can see and count connections by inspecting the process list




